---
title: タイルとタイルマップの概要
slug: Games/Techniques/Tilemaps
l10n:
  sourceCommit: 6d4fc564c9428eb242470b2bdf4f7db22d91612f
---

{{GamesSidebar}}

タイルマップは 2D ゲーム開発において非常に一般的なテクニックで、 **タイル** と呼ばれる小さく、規則的な形の画像からゲームの世界やレベルマップを構成します。これによりパフォーマンスの向上やメモリ使用量の節約につながります。レベルマップ全体を含む大きな画像ファイルは不要で、小さな画像または画像の断片によって複数回構築されるためです。この一連の記事は、 [JavaScript](/ja/docs/Web/JavaScript) と [Canvas](/ja/docs/Web/API/Canvas_API) を使用してタイルマップの作り方の基本をカバーしています (ただし、どのプログラミング言語でも同じような高レベルのテクニックを使用することができます)。

パフォーマンスの向上に限らず、タイルマップを論理的なグリッドにしてマッピングにすることもできます。これをゲームロジック内部で他の用途に使用したり (例えばパスファイディンググラフを用いて衝突検出にしたり)、レベルエディターを作成したりすることができます。

このテクニックを使用した有名なゲームタイトルは「スーパーマリオブラザーズ」や「パックマン」、「ゼルダの伝説 夢をみる島」、「スタークラフト」、「シムシティ2000」などがあります。正方形の背景が規則的に繰り返し表示されるゲームを考えてみると、タイルマップが使用されていることがわかると思います。

## タイルアトラス

タイル画像を管理する最も効率的な方法は、タイルアトラスかスプライトシートにすることで、必要なタイルを 1 つの画像ファイルにまとめます。タイルを描くときは、この大きな画像の一部分だけがゲームのキャンバスに描写されます。下の画像は 8 x 4 タイルのタイルアトラスを示しています。

![Tile atlas image](tile_atlas.png)

タイルアトラスを使用することで、各タイルに自動で **インデックス** が付与されるメリットもあります。インデックスはタイルマップを作成する時のタイルの識別子として適しています。

## タイルマップの構造

タイルマップを扱うのに必要なすべての情報を、同じデータやオブジェクトにまとめるのが一般的です。これらのデータやオブジェクト ([マップオブジェクトの例](https://github.com/mozdevs/gamedev-js-tiles/blob/gh-pages/square/no-scroll.js#L1-L18)) は以下の内容を含むべきです。

- **タイルサイズ**: 各タイルのサイズ (横ピクセル / 縦ピクセル)。
- **画像**: 使用されるタイルアトラスの画像 (1 つまたは複数)。
- **マップサイズ**: マップのサイズ。縦横のタイル数、または縦横のピクセル数。
- **表示グリッド**: グリッドの各位置にどの種類のタイルを置くかをインデックスを含めて示す。
- **論理グリッド**: ゲームの種類によっては、衝突検出や経路探索などになる。

> [!NOTE]
> 表示グリッドでは、空のタイルを示すために特別な値 (通常は負の数か `0`、 `null` ) が必要です。

## スクエアタイル

正方形のタイルマップは最もシンプルに実装できます。より汎用的な形としては、正方形ではなく長方形のタイルとすべきでしょうが、それほど一般的ではありません。スクエアタイルでは、 2 つの見方があります。

- トップダウン (「ウォークラフト 2」や「ファイナルファンタジー」シリーズのフィールド画面のような多くの RPG やストラテジーゲーム)
- サイドビュー（「スーパーマリオブラザーズ」のようなプラットフォームゲーム）

### 静的タイルマップ

タイルマップは表示可能な画面領域内に収めるか、より大きくすることができます。最初の段階では、タイルマップは **静的** であり、マップ全体を表示するためにスクロールする必要はありません。このケースでは「パックマン」や「アルカノイド」、「倉庫番」などのアーケードゲームでよく見られます。

静的タイルマップのレンダリングは簡単で、列と行をネストさせたループで繰り返すことでできます。高レベルのアルゴリズムでは次のようにできます。

```js
for (let column = 0; column < map.columns; column++) {
  for (let row = 0; row < map.rows; row++) {
    const tile = map.getTile(column, row);
    const x = column * map.tileSize;
    const y = row * map.tileSize;
    drawTile(tile, x, y);
  }
}
```

詳細は [スクエアタイルマップの実装: 静的タイルマップ](/ja/docs/Games/Techniques/Tilemaps/Square_tilemaps_implementation:_Static_maps) で説明されていて、実装例も見ることができます。

### スクロールタイルマップ

**スクロールする** タイルマップはゲームワールドのほんの一部しか表示されません。プラットフォーマーや　RPG のようにキャラクターを追ったり、ストラテジーやシミュレーションゲームのようにプレイヤーがカメラを操作したりすることができます。

#### 位置合わせとカメラ

すべてのスクロールするゲームでは、 **ワールド座標** (スプライトや他の要素がゲームワールド上に位置する座標) と **画面上の座標** (それらの要素が画面上でレンダリングされる実際の位置) の間で変換する必要があります。ワールド座標はゲームによって、タイルの位置 (マップ上の行と列) またはマップ全体のピクセル単位で表すことができます。ワールド座標を画面上の座標に変換できるようにするには、カメラの座標が必要です。

ワールド座標から画面上の座標への変換と、その逆を示す例は以下の通りです。

```js
// これらの関数は、カメラが一番左上隅にあると仮定しています。

function worldToScreen(x, y) {
  return { x: x - camera.x, y: y - camera.y };
}

function screenToWorld(x, y) {
  return { x: x + camera.x, y: y + camera.y };
}
```

#### レンダリング

タイルマップを描画する大雑把な方法は (静的タイルマップのように) すべてのタイルを繰り返して描画し、 (上に示した `worldToScreen()` の例のように) カメラ座標を差し引いて、画面領域の外に落ちるタイルを隠して置くことです。しかし、見えないタイルをすべてを繰り返し描画するのは無駄が多く、パフォーマンスの低下に繋がります。理想としては、 **目に見えるタイルだけを描画するべき** です。レンダリングのパフォーマンスを向上させる方法については [パフォーマンス](#performance) を参照してください。

スクロールタイルマップの実装の詳細は [スクエアタイルマップの実装: スクロールタイルマップ](/ja/docs/Games/Techniques/Tilemaps/Square_tilemaps_implementation:_Scrolling_maps) で説明されています。

### レイヤー

ビジュアルグリッドは多くの場合、複数のレイヤーで構成されています。こうすることで、同じ画像をそれぞれ異なる背景で使用できるため、より少ないタイルでリッチなゲームを表現することができます。例えば、複数の地形 (草や砂、レンガなど) の上にある岩は、それぞれ背景の地形ごとの岩タイルを用意することなく、描画される新しいレイヤーに別々のタイルを含めることができます。

キャラクターや他のゲームのスプライトをレイヤー層の真ん中に描画すると、キャラクターが木や建物の後ろを歩くような面白い効果ができます。

以下のスクリーンショットは先ほど説明した両方の例を示しています。タイルの _後ろ_ に現れるキャラクター (木のてっぺんの後ろに現れる騎士) と、異なる地形の上に描画されるタイル (茂み) です。

![レイヤー化された背景の地形のグリッド。茂みタイルは前に描画され、大きな草の地形の上に置かれ、茶色の砂が敷かれた長方形の地形が後ろにある。木のタイルは左下と右下にある草の地形の前に描画される。騎士のタイルは左下に描画された木のタイルの後ろに現れる。](screen_shot_2015-10-06_at_15.56.05.png)

### ロジックグリッド

タイルマップは実際のビジュアルタイルのグリッドなので、このビジュアルグリッドとロジックグリッドの間にマッピングを作成するのが一般的です。最も一般的なのは、当たり判定を処理するためにこのロジックグリッドを使用することですが、他にもキャラクターのスポーンポイントや (「テトリス」や「Bejeweled」のように) 特定のアクションを起こすためにいくつかの要素が正しく一緒に配置されているか検出するパスファインディングアルゴリズムの用途も可能です。

> [!NOTE]
> [ロジックグリッドを使用して当たり判定を検出する](https://mozdevs.github.io/gamedev-js-tiles/square/logic-grid.html) デモを見ることができます。

## Isometric tilemaps

Isometric tilemaps create the illusion of a 3D environment, and are extremely popular in 2D simulation, strategy, or RPG games. Some of these games include _SimCity 2000_, _Pharaoh_, or _Final Fantasy Tactics_. The below image shows an example of an atlas for an isometric tileset.

![A 3x4 map of variously colored tiles in isometric projection](iso_tiles.png)

## Performance

Drawing scrolling tile maps can take a toll on performance. Usually, some techniques need to be implemented so scrolling can be smooth. The first approach, as discussed above, is to **only draw tiles that will be visible**. But sometimes, this is not enough.

One simple technique consists of pre-rendering the map in a canvas on its own (when using the Canvas API) or on a texture (when using WebGL), so tiles don't need to be re-drawn every frame and rendering can be done in just one blitting operation. Of course, if the map is large this doesn't really solve the problem — and some systems don't have a very generous limit on how big a texture can be.

One way consists of [drawing the section that will be visible off-canvas](https://mozdevs.github.io/gamedev-js-tiles/performance/offcanvas.html) (instead of the entire map.) That means that as long as there is no scrolling, the map doesn't need to be rendered.

A caveat of that approach is that when there _is_ a scrolling, that technique is not very efficient. A better way would be to create a canvas that is 2x2 tiles bigger than the visible area, so there is one tile of "bleeding" around the edges. That means that the map only needs to be redrawn on the canvas when the scrolling has advanced one full tile — instead of every frame — while scrolling.

In fast games that might still not be enough. An alternative method would be to split the tilemap into big sections (like a full map split into 10 x 10 chunks of tiles), pre-render each one off-canvas and then treat each rendered section as a "big tile" in combination with one of the algorithms discussed above.

## See also

- Related articles on the MDN:

  - [Static square tile maps implementation with Canvas API](/en-US/docs/Games/Techniques/Tilemaps/Square_tilemaps_implementation:_Static_maps)
  - [Scrolling square tile maps implementation with Canvas API](/en-US/docs/Games/Techniques/Tilemaps/Square_tilemaps_implementation:_Scrolling_maps)

- External resources:

  - [Demos and source code](https://mozdevs.github.io/gamedev-js-tiles/)
  - [Grid parts and relationships](https://www.redblobgames.com/grids/parts/) by Amit Patel (May 2021)
  - [Isometric graphics in videogames](https://en.wikipedia.org/wiki/Isometric_graphics_in_video_games_and_pixel_art) (Wikipedia)
