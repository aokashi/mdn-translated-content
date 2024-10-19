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

正方形のタイルマップは最もシンプルに実装できます。より汎用的なケースとして正方形ではなく長方形のタイルマップもありますが、それほど一般的ではありません。スクエアタイルでは、 2 つの見方があります。

- トップダウン (「ウォークラフト 2」や「ファイナルファンタジー」シリーズのフィールド画面のような多くの RPG やストラテジーゲーム)
- サイドビュー（「スーパーマリオブラザーズ」のようなプラットフォームゲーム）

### Static tilemaps

A tilemap can either fit into the visible screen area screen or be larger. In the first case, the tilemap is **static** — it doesn't need to be scrolled to be fully shown. This case is very common in arcade games like _Pacman_, _Arkanoid_, or _Sokoban_.

Rendering static tilemaps is easy, and can be done with a nested loop iterating over columns and rows. A high-level algorithm could be:

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

You can read more about this and see an example implementation in [Square tilemaps implementation: Static maps](/en-US/docs/Games/Techniques/Tilemaps/Square_tilemaps_implementation:_Static_maps).

### Scrolling tilemaps

**Scrolling** tilemaps only show a small portion of the world at a time. They can follow a character — like in platformers or RPGs — or allow the player to control the camera — like in strategy or simulation games.

#### Positioning and camera

In all scrolling games, we need a translation between **world coordinates** (the position where sprites or other elements are located in the level or game world) and **screen coordinates** (the actual position where those elements are rendered on the screen). The world coordinates can be expressed in terms of tile position (row and column of the map) or in pixels across the map, depending on the game. To be able to transform world coordinates into screen coordinates, we need the coordinates of the camera, since they determine which section of the world is being displayed.

Here are examples showing how to translate from world coordinates to screen coordinates and back again:

```js
// these functions assume that the camera points to the top left corner

function worldToScreen(x, y) {
  return { x: x - camera.x, y: y - camera.y };
}

function screenToWorld(x, y) {
  return { x: x + camera.x, y: y + camera.y };
}
```

#### Rendering

A trivial method for rendering would just be to iterate over all the tiles (like in static tilemaps) and draw them, subtracting the camera coordinates (like in the `worldToScreen()` example shown above) and letting the parts that fall outside the view window sit there, hidden. Drawing all the tiles that can not be seen is wasteful, however, and can take a toll on performance. **Only tiles that are at visible should be rendered** ideally — see the [Performance](#performance) section for more ideas on improving rendering performance.

You can read more about implementing scrolling tilemaps and see some example implementations in [Square tilemaps implementation: Scrolling maps](/en-US/docs/Games/Techniques/Tilemaps/Square_tilemaps_implementation:_Scrolling_maps).

### Layers

The visual grid is often made up of several layers. This allows us to have a richer game world with fewer tiles, since the same image can be used with different backgrounds. For instance, a rock that could appear on top of several terrain types (like grass, sand or brick) could be included on its own separate tile which is then rendered on a new layer, instead of several rock tiles, each with a different background terrain.

If characters or other game sprites are drawn in the middle of the layer stack, this allows for interesting effects such as having characters walking behind trees or buildings.

The following screenshot shows an example of both points: a character appearing _behind_ a tile (the knight appearing behind the top of a tree) and a tile (the bush) being rendered over different terrain types.

![A grid of layered background terrains. A bush tile is rendered at the top, over a large grass terrain, and again over a layered rectangular terrain with brown sand at the bottom. A tree tile is rendered over the grass terrain at the bottom left and again at the bottom right. A knight tile appears behind the tree tile that is rendered at the bottom left.](screen_shot_2015-10-06_at_15.56.05.png)

### The logic grid

Since tilemaps are an actual grid of visual tiles, it is common to create a mapping between this visual grid and a logic grid. The most common case is to use this logic grid to handle collisions, but other uses are possible as well: character spawning points, detecting whether some elements are placed together in the right way to trigger a certain action (like in _Tetris_ or _Bejeweled_), path-finding algorithms, etc.

> [!NOTE]
> You can take a look at our demo that shows [how to use a logic grid to handle collisions](https://mozdevs.github.io/gamedev-js-tiles/square/logic-grid.html).

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
