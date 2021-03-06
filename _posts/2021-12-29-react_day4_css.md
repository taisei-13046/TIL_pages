---
layout: post
title: "MDNでCSSを学習" 
date: 2021-12-29 
category: read 
excerpt: ""
---

## やったこと 
CSSに不安を持っていたため、今日からひたすらCSSについて勉強する。  
わかった気になっているがどこまで分かってるかわからないので、超基本的なところからやる。

### CSS の構文
ページ上の `<h1>` 要素の文字を大きく、赤くしたいといった時  
```css
h1 {
    color: red;
    font-size: 5em;
}
```
流石にこれらはわかる  

複数セレクタに焦点を当てる場合
```css
p, li {
    color: green;
}
```

クラスに焦点を当てる場合
```css
.special {
  color: orange;
  font-weight: bold;
}
```

HTML要素セレクタに続けてクラスセレクタが記述されている場合
```css
li.special {
  color: orange;
  font-weight: bold;
}
```

`<li>` 要素の中にある `<em>` 要素を選択する場合
```css
li em {
  color: rebeccapurple;
}
```

状態に基づいてスタイリングする
```css
a:link {
  color: pink;
}

a:visited {
  color: green;
}

a:hover {
  text-decoration: none;
}
```

セレクタとコンビネーターを組み合わせる
```css
/* <article> 要素の内側にある <p> 要素の <span> 要素に焦点を当てるとき  */
article p span { ... }

/* <h1> 要素の直後に来る <ul> 要素の、そのまた直後に来る <p> 要素に焦点を当てるとき */
h1 + ul + p { ... }
```

### CSS の全体像
#### 詳細度
```css
.special {
  color: red;
}

p {
  color: blue;
}
```
上の二つが同時に当てられた場合、`p`セレクターが優先される  
後のスタイルは、それより前のスタイルシートに現れた競合するスタイルを置き換える。これが**カスケード規則**  

#### CSSは大きく2つの部品からなる
`プロパティ`: スタイルに関して変更できる何らかの特徴をあらわす、人間が理解できる識別子です。  
例えば、 font-size, width, background-color などです。  
`値`: 各プロパティには値が割り当てられています。  
この値は、プロパティをどのようにスタイル付けするかを示します。  

#### アット規則
CSS のアット規則 は、 CSS が実行すること、またはそれがどのように動作するべきかの指示を提供する
`@import` はスタイルシートを別の CSS スタイルシートにインポートする
```css
@import 'styles2.css';
```

`@media` があり、メディアクエリを作成するために使用される
@import 'styles2.css';
```css
body {
  background-color: pink;
}

@media (min-width: 30em) {
  body {
    background-color: blue;
  }
}
```

### CSSはどう働くか
1. ブラウザーが HTML をロードします（ネットワークから受信するなど）。
2. HTML が DOM (Document Object Model) に変換されます。DOM はコンピューターのメモリー内のドキュメントです。
3. その後ブラウザーは埋め込まれた画像やビデオなどの HTML ドキュメントにリンクされているリソースと CSS を取得します。
4. ブラウザーは取得した CSS を解析し、要素、クラス、ID などセレクタの種類ごとに分類します。見つけたセレクターに基づいて、DOM のどのノードにどのルールを適用するかを決定し、スタイルを適用します（この中間ステップはレンダーツリーと呼ばれます）。
5. レンダーツリーは、ルール適用後の構造にレイアウトされます。
6. ページが画面に表示されます（この段階はペイントと呼ばれます）。

![スクリーンショット 2021-12-27 12 53 50](https://user-images.githubusercontent.com/78260526/147432693-8051c1e7-65d5-46b7-b2ce-a6009bf872c4.png)

### カスケードと継承
CSS は **Cascading Style Sheets** の略で、最初の単語であるカスケード (cascading) を理解することは非常に重要  
プロジェクトに取り組んでいるとき、要素に適用されているはずの CSS が機能していないと感じることがあります。  
それは大抵の場合、同じ要素に適用される可能性のある 2 つ のルールを作ってしまったことに由来します。  

詳細度とは、複数のルールに異なるセレクターがある場合にブラウザーがどのルールを適用するかを決定する方法  
```css
.main-heading { 
    color: red; 
}
        
h1 { 
    color: blue; 
}
```
`<h1 class="main-heading">This is my heading.</h1>`  
このような状況の場合、h1は最終的に**赤色**になる
クラスセレクターはルールにより高い詳細度を与えるため、要素セレクターを使用したルールがソースの順序でさらに下の方にあっても適用される  

#### 継承を理解する
デフォルトで継承されるプロパティとそうでないものは、主に常識に基づいている  
幅（上述したとおり）、マージン、パディング、境界線などは継承されない  

**継承の制御**

- `inherit`
    - 選択した要素に適用されるプロパティ値を、その親要素と同じものに設定する。
    - 「継承を有効にする」ことを意味する。
- `initial`
    - 選択された要素に適用されるプロパティ値を、ブラウザーのデフォルトスタイルシートでその要素に設定されているものと同じ値に設定する
- `unset`
    - プロパティを自然な値にリセットする
    - つまり、プロパティが自然に継承される場合は inherit のように動作し、そうでない場合は initial のように動作します。  

**カスケードを理解する**
カスケードがどのようにしてCSSルールを適用していくか  
1. 重要性 (Importance)
2. 詳細度 (Specificity)
3. ソースオーダー (Source order)  
    CSS の最後にあるルールが優先されるというもの 

`!important`を使うと、無条件で優先されるようになる。  
しかし、**絶対に必要な場合を除きあまり使用しない**



## frontendコース課題を履修
その中で、ポイントになったところをまとめる

#### [擬似要素 (Pseudo-elements)](https://developer.mozilla.org/ja/docs/Web/CSS/Pseudo-elements)
CSS の 擬似要素Pseudo-elementsはセレクターに付加するキーワードで、  
選択された要素の特定の部分にスタイル付けできるようにするもの

したの例は段落の最初の行のフォントを変更するために使用する  
```css
/* 各 <p> 要素の最初の行です。 */
p::first-line {
  color: blue;
  text-transform: uppercase;
}
```

### positionについて
positionは**要素を好きな位置に固定するプロパティ**

`relative`: 要素が本来配置されるべき位置がそのまま基準になる  
relativeを使うときは、**”要素が自分自身を基準にする”**  

`absolute`: relativeが指定されている”親要素”を基準にして、自分の位置を決定する  
使い方、
1. 親要素(=基準位置)にrelativeを指定
2. 子要素(=動かしたい要素)にabsoluteを指定
3. 子要素にtopやleftなどを指定x


## Atomic Designについて
こっからは、フロントの最終課題を進めていく  

1. atoms/button  

[justify-contentとalign-itemsで楽々レイアウト](https://goodsan.jp/wp/justifycontent/)  
- `justify-content`: 横方向のレイアウトを指定  
- `align-items`: 垂直方向のレイアウトを指定  
