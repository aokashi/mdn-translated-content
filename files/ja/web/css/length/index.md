---
title: <length>
slug: Web/CSS/length
l10n:
  sourceCommit: bb6092c4230b69c2eceae6910af68c73955cae1c
---

{{CSSRef}}

**`<length>`** は [CSS](/ja/docs/Web/CSS) の[データ型](/ja/docs/Web/CSS/CSS_Types)で、長さの値を表します。長さは {{Cssxref("width")}}、{{Cssxref("height")}}、{{Cssxref("margin")}}、{{Cssxref("padding")}}、{{Cssxref("border-width")}}、{{Cssxref("font-size")}}、{{Cssxref("text-shadow")}} など数多くの CSS プロパティで使用されています。

> **メモ:** {{cssxref("&lt;percentage&gt;")}} 値も `<length>` 値を受け付ける同じプロパティの一部でも使用することができますが、 `<length>` 値と同じものではありません。 {{cssxref("&lt;length-percentage&gt;")}} を参照してください。

## 構文

`<length>` データ型は一つの {{cssxref("&lt;number&gt;")}} とそれに続く以下に挙げる単位の一つから成ります。単位と数値の間に空白は置きません。数値 `0` の後の単位は省略可能です。

> **メモ:** 負の `<length>` を許容するプロパティとそうでないプロパティがあります。

<!-- TODO 「表されます」の表記については議論あり。後で調べて適切な形に修正すべき。 -->
長さの [指定値](/ja/docs/Web/CSS/specified_value) (_specified length_) はその量と単位で表されます。長さの [計算値](/ja/docs/Web/CSS/computed_value) (_computed length_) は指定された長さを絶対値へと計算したものであり、単位は区別しません。

`<length>` は絶対的な長さあるいは相対的な長さからなります。相対的な長さとは、他の長さに従って長さを指定することです。単位によって、これは特定の文字の寸法であったり、[行の高さ](/ja/docs/Web/CSS/line-height)であったり、{{glossary("viewport", "ビューポート")}}の寸法であったりします。相対的な長さの単位を用いたスタイルシートを使用することで、ある場所で作った出力環境を別の場所に簡単に拡張できます。

> **メモ:** 子要素は親要素で指定された相対値を継承しません。計算値を継承します。

ここに記載されている相対的な長さの単位は、フォントとビューポートをベースとして計算しています。

### フォントの相対長

フォントの相対長では、 `<length>` の値を、要素やその親で現在使われているフォントにおける、特定の文字やフォント属性の寸法で定義します。

> **メモ:** これらの単位、特に `em` と `rem` は、ユーザーがフォントサイズを変更しても[ページの縦方向のリズム](https://24ways.org/2006/compose-to-a-vertical-rhythm)を維持する、スケーラブルなレイアウトを作るためによく使われます。

- `cap` {{experimental_inline}}
  - : その要素の {{Cssxref("font")}} における "cap height" (ふつうの大文字の高さ) を表します。
- `ch`
  - : その要素の {{Cssxref("font")}} における "0" (ゼロ、Unicode 文字 U+0030) の幅、より正確に言えば advance measure です。
    "0" の文字の大きさを判断することが不可能または無意味である場合、幅が 0.5em、高さが 1em と仮定します。
- `em`
  - : その要素の {{Cssxref("font-size")}} の計算値を表します。{{Cssxref("font-size")}} プロパティ自身に使われた場合は、要素に<em>継承された</em>フォントの大きさを表します。
- `ex`
  - : その要素の {{Cssxref("font")}} における [x-height](https://en.wikipedia.org/wiki/X-height) です。 'x' の文字を持つフォントでは一般的に、フォントにおける小文字の高さに相当します。多くのフォントでは `1ex ≈ 0.5em` です。
- `ic`
  - : 描画に使用されるフォントにおける「水」 (CJK water ideograph, U+6C34) の文字の advance measure と同じです。
- `lh`
  - : 使用されている要素の {{Cssxref("line-height")}} プロパティの計算値と同じで、絶対的な長さに変換されます。
- `rem`
  - : ルート要素 (ふつうは {{HTMLElement("html")}}) の {{Cssxref("font-size")}} を表します。ルート要素の {{Cssxref("font-size")}} で使うと初期値を表します (多くのブラウザーでは既定値は `16px` ですが、ユーザー設定によって変わる可能性があります)。
- `rlh`
  - : ルート要素 (ふつうは {{HTMLElement("html")}}) の {{Cssxref("line-height")}} プロパティの計算値と同じで、絶対的な長さに変換されます。ルート要素の {{Cssxref("font-size")}} または {{Cssxref("line-height")}} プロパティに使われた場合は、プロパティの初期値になります。

### ビューポートの相対長

ビューポートの長さの単位はスモールとラージ、ダイナミック、デフォルトの4つのサイズからなります。異なるビューポートサイズとはブラウザーのインターフェイスを拡大縮小したり、ブラウザー下部の表示を切り替えたりすることで値が変わります。

- **スモール**

  - : ブラウザーのインターフェイスが動的に拡大されると同時に、可能な限り小さなビューポートが必要な場合は、スモールのビューポートサイズを使用する必要があります。スモールのビューポートサイズはブラウザーのインターフェイスが拡大されたときに、コンテンツがビューポート全体を満たすようにします。また、このサイズを選択すると、ブラウザーのインターフェイスを縮小した時に空白のスペースが残る可能性があります。

    例えば、スモールのビューポートサイズに基づいたビューポート単位を使用してサイズ設定された要素は、ブラウザーの動的なインターフェイスがすべて表示されている時に、コンテンツが隠れることなく完全に画面内に表示します。しかし、ブラウザーのインターフェイスが隠されると、その要素の周りには余分なスペースが見えるかもしれません。したがって、小さなビューポートサイズは一般的には「安全」ですが、ユーザーがページ上を操作したりしたときには、最も見栄えのいいレイアウトができないかもしれません。

    スモールのビューポートサイズは `sv` 接頭辞で表記され、 `sv*` サイズ単位になります。スモールのビューポートサイズの単位のサイズは固定なので、ビューポートのサイズが変更されない限り安定して計算されます。

- **ラージ**

  - : When you want the largest possible viewport in response to browser interfaces retracting dynamically, you should use the large viewport size. The large viewport size allows the content you design to fill the entire viewport when browser interfaces are retracting. You need to be aware though that the content might get hidden when browser interfaces expand.

    For example, on mobile phones where the screen real-estate is at a premium, browsers often hide part or all of the title and address bar after a user starts scrolling the page. When an element is sized using a viewport-percentage unit based on the large viewport size, the content of the element will fill the entire visible page when these browser interfaces are hidden. However, when these retractable browser interfaces are shown, they can hide the content that is sized or positioned using the _large_ viewport-percentage units.

    The large viewport unit is represented by the `lv` prefix and results in the `lv*` viewport-percentage units. The sizes of the large viewport-percentage units are fixed, and therefore stable, unless the viewport itself is resized.

- **ダイナミック**

  - : When you want the viewport to be automatically sized in response to browser interfaces dynamically expanding or retracting, you can use the dynamic viewport size. The dynamic viewport size allows the content you design to fit exactly within the viewport, irrespective of the presence of dynamic browser interfaces.

    The dynamic viewport unit is represented by the `dv` prefix and results in the `dv*` viewport-percentage units. The sizes of the dynamic viewport-percentage units are not stable, even when the viewport itself is unchanged.

    > **Note:** While the dynamic viewport size can give you more control and flexibility, using viewport-percentage units based on the dynamic viewport size can cause the content to resize while a user is scrolling a page. This can lead to degradation of the user interface and cause a performance hit.

- **デフォルト**

  - : The default viewport size is defined by the browser. The behavior of the resulting viewport-percentage unit could be equivalent to the viewport-percentage unit based on the small viewport size, the large viewport size, an intermediate size between the two, or the dynamic viewport size.

    > **Note:** For example, a browser might implement the default viewport-percentage unit for height (`vh`) that is equivalent to the large viewport-percentage height unit (`lvh`). If so, this could obscure content on a full-page display while the browser interface is expanded.

Viewport-percentage lengths define `<length>` values in percentage relative to the size of the initial [containing block](/en-US/docs/Web/CSS/Containing_block), which in turn is based on either the size of the {{Glossary("viewport")}} or the page area, i.e., the visible portion of the document. When the height or width of the initial containing block is changed, the elements that are sized based on them are scaled accordingly. There is a viewport-percentage length unit variant corresponding to each of the viewport sizes, as described below.

> **メモ:** ビューポートの寸法は {{cssxref("@page")}} で宣言されたブロックでは無効です。

- `vh`

  - : ビューポートの初期値の[包含ブロック](/ja/docs/Web/CSS/Containing_block)における高さの 1% と同じです。例えばビューポートの高さが `300px` とした場合、 `70vh` の値は `210px` になります。

    For small, large, and dynamic viewport sizes, the respective viewport-percentage units are `svh`, `lvh`, and `dvh`. `vh` represents the viewport-percentage length unit based on the browser default viewport size.

- `vw`

  - : ビューポートの初期値の[包含ブロック](/ja/docs/Web/CSS/Containing_block)における幅の 1% と同じです。例えばビューポートの幅が `800px` とした場合、 `50vh` の値は `400px` になります。

    For small, large, and dynamic viewport sizes, the respective viewport-percentage units are `svw`, `lvw`, and `dvw`.
    `vw` represents the viewport-percentage length unit based on the browser default viewport size.

- `vmax`

  - : `vw` と `vh` の大きい方です。

    For small, large, and dynamic viewport sizes, the respective viewport-percentage units are `svmax`, `lvmax`, and `dvmax`.
    `vmax` represents the viewport-percentage length unit based on the browser default viewport size.

- `vmin`

  - : `vw` と `vh` の小さい方です。

    For small, large, and dynamic viewport sizes, the respective viewport-percentage units are `svmin`, `lvmin`, and `dvmin`.
    `vmin` represents the viewport-percentage length unit based on the browser default viewport size.

- `vb`

  - : 初期値の[包含ブロック](/ja/docs/Web/CSS/Containing_block)における、ルート要素の[ブロック軸](/ja/docs/Web/CSS/CSS_Logical_Properties#block_vs._inline)の寸法の 1% と同じです。

    For small, large, and dynamic viewport sizes, the respective viewport-percentage units are `svb`, `lvb`, and `dvb`, respectively.
    `vb` represents the viewport-percentage length unit based on the browser default viewport size.

- `vi`

  - : 初期値の[包含ブロック](/ja/docs/Web/CSS/Containing_block)における、ルート要素の[インライン軸](/ja/docs/Web/CSS/CSS_Logical_Properties#block_vs._inline)の寸法の 1% と同じです。

    For small, large, and dynamic viewport sizes, the respective viewport-percentage units are `svi`, `lvi`, and `dvi`.
    `vi` represents the viewport-percentage length unit based on the browser default viewport size.


### Container query length units

When applying styles to a container using container queries, you can use container query length units.
These units specify a length relative to the dimensions of a query container.
Components that use units of length relative to their container are more flexible to use in different containers without having to recalculate concrete length values.
For more information, see [Container queries](/en-US/docs/Web/CSS/CSS_Container_Queries).

- `cqw`

  - : Represents a percentage of the width of the query container. `1cqw` is 1% of the query container's width. For example, if the query container's width is `800px`, then a value of `50cqw` on a property will be `400px`.

- `cqh`

  - : Represents a percentage of the height of the query container. `1cqh` is 1% of the query container's height. For example, if the query container's height is `300px`, then a value of `10cqh` on a property will be `30px`.

- `cqi`

  - : Represents a percentage of the inline size of the query container. `1cqi` is 1% of the query container's inline size. For example, if the query container's inline size is `800px`, then a value of `50cqi` on a property will be `400px`.

- `cqb`

  - : Represents a percentage of the block size of the query container. `1cqb` is 1% of the query container's block size. For example, if the query container's block size is `300px`, then a value of `10cqb` on a property will be `30px`.

- `cqmin`

  - : Represents a percentage of the smaller value of either the query container's inline size or block size. `1cqmin` is 1% of the smaller value of either the query container's inline size or block size. For example, if the query container's inline size is `800px` and its block size is `300px`, then a value of `50cqmin` on a property will be `150px`.

- `cqmax`

  - : Represents a percentage of the larger value of either the query container's inline size or block size. `1cqmax` is 1% of the larger value of either the query container's inline size or block size. For example, if the query container's inline size is `800px` and its block size is `300px`, then a value of `50cqmax` on a property will be `400px`.

### 絶対的な長さの単位

絶対的な長さの単位は、出力メディアの物理的な特性が分かっている場合 (印刷レイアウトなど) に物理的な長さを表します。これはある単位を物理的な単位に固定してから、これを基準に他の単位を定義します。固定する単位は画面のような低解像度の端末か、あるいはプリンターのような高解像度の端末かによって異なります。

低解像度の端末では、`px` の単位が物理的な*参照ピクセル*を表し、その他の単位はこれを基準に定義されます。例えば `1in` は `96px` と定義され、これは `72pt` と等しくなります。このような端末では結果的に、インチ (`in`)、センチメートル (`cm`)、ミリメートル (`mm`) といった単位で表される長さは、同名の物理的な単位と同じ長さになるとは限りません。</p>

高解像度の端末では、インチ (`in`)、センチメートル (`cm`)、ミリメートル (`mm`) は対応する物理的な単位と同じになります。従って、 `px` の単位は、それ (1 インチの 1/96) を基準に定義されます。

> **メモ:** たくさんのユーザーが、テキストが読みやすくなるよう{{glossary("user agent", "ユーザーエージェント")}}の既定のフォントの大きさを拡大しています。絶対的な長さで指定すると、大きさが固定され、ユーザー設定によって拡大しなくなるため、アクセシビリティ上の問題が発生します。この理由から、 `font-size` を設定する場合には、 (`em` や `rem` のような) 相対的な単位が適しています。

- `px`
  - : 1 ピクセルです。画面表示では、伝統的に端末の 1 ピクセル (ドット) を表します。しかし、*プリンター*や*高解像度の画面*では、 CSS の 1 ピクセルが端末の複数ピクセルになることがあります。 `1px` = `1in` の 1/96 です。
- `cm`
  - : 1センチメートルです。 `1cm` = `96px/2.54` です。
- `mm`
  - : 1ミリメートルです。 `1mm` = `1cm` の1/10です。
- `Q` {{experimental_inline}}
  - : 1/4ミリメートルです。 `1Q` = `1cm` の1/40です。
- `in`
  - : 1インチです。 `1in` = `2.54cm` = `96px` です。
- `pc`
  - : 1パイカです。 `1pc` = `12pt` = `1in` の1/6です。
- `pt`
  - : 1ポイントです。 `1pt` = `1in` の1/72です。

## 補間

アニメーションの場合、 `<length>` データ型の値は浮動小数点の実数として補間されます。補間は計算値に対して行われます。補間の速度は、アニメーションに関連付けられた[タイミング関数](/ja/docs/Web/CSS/easing-function)によって定められます。

## 例

<h3 id="Length_unit_comparison">長さの単位の比較</h3>

以下のデモでは、入力欄に `<length>` の値 (例えば `300px`, `50%`, `30vw`) を入力することができ、 <kbd>Return</kbd> を押したときにバーの長さが反映されます。

これにより、様々な長さの単位の効果を比較し対照させることができます。

#### HTML

```html
<div class="outer">
  <div class="input-container">
    <label>長さを入力してください:</label>
    <input type="text" id="length">
  </div>
  <div class="inner">

  </div>
</div>
<div class="results">
</div>
```

#### CSS

```css
html {
  font-family: sans-serif;
  font-weight: bold;
  box-sizing: border-box;
}

.outer {
  width: 100%;
  height: 50px;
  background-color: #eee;
  position: relative;
}

.inner {
  height: 50px;
  background-color: #999;
  box-shadow: inset 3px 3px 5px rgba(255,255,255,0.5),
              inset -3px -3px 5px rgba(0,0,0,0.5);
}

.result {
  height: 20px;
  background-color: #999;
  box-shadow: inset 3px 3px 5px rgba(255,255,255,0.5),
              inset -3px -3px 5px rgba(0,0,0,0.5);
  background-color: orange;
  display: flex;
  align-items: center;
  margin-top: 10px;
}

.result code {
  position: absolute;
  margin-left: 20px;
}

.results {
  margin-top: 10px;
}

.input-container {
  position: absolute;
  display: flex;
  justify-content: flex-start;
  align-items: center;
  height: 50px;
}

label {
  margin: 0 10px 0 20px;
}
```

#### JavaScript

```js
const inputDiv = document.querySelector('.inner');
const inputElem = document.querySelector('input');
const resultsDiv = document.querySelector('.results');

inputElem.addEventListener('change', () => {
  inputDiv.style.width = inputElem.value;

  const result = document.createElement('div');
  result.className = 'result';
  result.style.width = inputElem.value;
  result.innerHTML = `<code>width: ${inputElem.value}</code>`;
  resultsDiv.appendChild(result);

  inputElem.value = '';
  inputElem.focus();
})
```

#### 結果

{{EmbedLiveSample('Length_unit_comparison','100%', 700)}}

## 仕様書

{{Specifications}}

## ブラウザーの互換性

{{Compat}}

## 関連情報

- [CSS 値と単位チュートリアル](/ja/docs/Learn/CSS/Building_blocks/Values_and_units)
- [CSS 値と単位リファレンス](/ja/docs/Web/CSS/CSS_Values_and_Units)
- [ボックスモデル](/ja/docs/Web/CSS/CSS_Box_Model)
