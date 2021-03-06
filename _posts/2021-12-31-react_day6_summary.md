---
layout: post
title: "React の総復習" 
date: 2021-12-31 
category: read 
excerpt: ""
---

## やったこと
Reactの復習

### memoで囲うことの意味
(前提)  
Reactでは親のコンポーネントが再レンダリングされた場合、その子コンポーネントの再レンダリングされてしまう。  
そこで、propsに変化がなければ再レンダリングをする必要が無いのが分かっている場合に、そのコンポーネントのレンダリングを抑制することで、パフォーマンスが向上する可能性がある。  
`React.memo`を使うことで、Propsが変更された時のみ再レンダリングされるようになる！  
複数のコンポーネントで成り立っているコンポーネントは基本**React.memo**を使用するのがいい  
しかし、闇雲にmemo化するのも違う気がしている  
メモ化する際は
1. コンポーネントの再レンダリングに時間がかかる
2. Propsが変わらない
3. 何度も再レンダリングされる
4. パフォーマンスに大きく影響を与えている  

これらに当てはまる際にReact.memoを使用するといい  
このサイトがとても面白かった!!  
[メモ化することへのコストについて](https://cam-inc.co.jp/p/techblog/530551646413914939)  

### コンポーネントにアロー関数をpropsとして渡す場合はuseCallbackを使う
(重要)*アロー関数はレンダリング時に再生成されるため、レンダリング前の関数とは別物として評価される*  
すると、渡された側のコンポーネントは異なるpropsが渡されたと認識し、再レンダリングが走ってしまう  

### default marginについて
`<p>`の場合デフォルトで上下にmarginが当たっている！！  
![スクリーンショット 2021-12-31 10 44 43](https://user-images.githubusercontent.com/78260526/147798225-b354cf09-bb32-4290-9eb7-d0d2b06e1fea.png)  
それを解除するために`margin: 0`を指定することで、上下のmarginがなくなる  
参考資料 [default marginを覚えておこう](http://msw316.jpn.org/hp_kouza/html215/dft_margin.html)  

### Atomic Designについて

#### Atoms
Atomic designで大切なのは、そのコンポーネントの**役割**を考えること  
例えば、 components/atoms/button/PrimaryButton.jsx の場合
```js
export const PrimaryButton = (props) => {
  const { children } = props;
  return <button>{children}</button>;
};
```
表示するボタンの文字をchildrenで受け取って、再利用できるようにする  

#### templates
templatesではページの枠組みだけを定義し、具体的なデータはあるかわない  

#### Atomic Designに取り組む際のポイント  
- あくまでベース！！
  - それ通りにする必要はないので、プロジェクトごとにtemplatesがなくすこともOK
  - チームでカスタマイズしていく
- 初めから分けない
  - まずは一つのコンポーネントで作成し、定期的にリファクタリングする
- 要素の関心を意識する
  - 関心をもとにpropsを定義したりする  

### styled-componentsについて
#### styled-componentsの要素をまとめてBase化して使用できる  
components/atoms/button/BaseButton.jsx
```js
import styled from "styled-components";

export const BaseButton = styled.button`
  color: #fff;
  padding: 6px 24px;
  border-radius: 9999px;
  border: none;
  outline: none;
  &:hover {
    cursor: pointer;
    opacity: 0.8;
  }
`;
```
components/atoms/button/PrimaryButton.jsx
```js
import styled from "styled-components";
import { BaseButton } from "./BaseButton";

export const PrimaryButton = (props) => {
  const { children } = props;
  return <SButton>{children}</SButton>;
};

const SButton = styled(BaseButton)`
  background-color: #40514e;
`;
```

#### styled-componentsでも子要素に対して、sassのようにネスとして記載できる  
```js
const SDl = styled.dl`
  text-align: left;
  dt {
    float: left;
  }

  dd {
    padding-left: 32px;
    padding-bottom: 8px;
    overflow-wrap: break-word;
  }
`
```


###  高さを指定する100vhと100%の違い
CSSで大きさを指定する際の単位  
- `px`：ピクセル。画素数
- `%`：親要素に対する割合
- `vh`：ビューポート（画面サイズ）の高さに対する割合。100vhは画面の高さと同じ（100%）を表す
- `vw`：ビューポートの幅に対する割合
- `vmin`：画面の高さか幅の小さい方に対する割合
- `vmax`：画面の高さか幅の大きい方に対する割合

＊ 要素の大きさをpxで指定するとレスポンシブ対応ができなくなるので、ビューポートに応じた大きさの調整をするべき  
**`vh`はビューポートに対する割合で、`%`は親要素に対しての割合**  

使い分け  
- ページの最上部に表示する要素には `100%` で指定したほうが良い
  - ページの最上部であれば常にアドレスバーが表示されるため、100vh で指定するとはみ出てしまう
- 逆にページの途中の要素であれば 100vh で良い
  - 下にスクロールしている途中であればアドレスバーが消え、100vh が画面にちょうどフィットする  
参考資料[100% と 100vh の違い](https://hysryt.com/archives/1092)  


### Contextについて
[document](https://ja.reactjs.org/docs/context.html)  
#### コンテクストをいつ使用すべきか
コンテクストは、ある Reactコンポーネントのツリーに対して「**グローバル**」とみなすことができる、  
現在の認証済みユーザ・テーマ・優先言語といったデータを共有するために設計されている  
##### しかし、コンテクストはコンポーネントの再利用をより難しくする為、慎重に利用する必要がある  

`React.createContext`   
この関数によって作成されるコンテキストオブジェクトは以下のようになっている  
```js
{
  Provider: <>...<>,
  Consumer: <>...<>,
  ...
}
```
- Providerコンポーネントの使用
```js
  return (
    // Providerコンポーネントで包むことによって、Provider配下のコンテキストを決定する
    <ResourceContext.Provider value={resource.name}>
      <NavigationComponent />
      <BodyComponent />
    </ResourceContext.Provider>
  );
```
  - propsとしてvalueを受け取る、このvalueを配下のコンポーネントに渡す。
  - valueが変更されると配下のコンポーネントを再レンダリングする
- Consumerコンポーネントの使用(実際は使わない)  
```js
    // Provider配下のコンテキストでは、その値をConsumerコンポーネントから受け取ることができる。
    // Render Propsと同じように受け取る。function as a childとも呼ばれる。
    <ResourceContext.Consumer>
      { (resourceName) => (
        <TitleComponent title={resourceName} />
      )}
    </ResourceContext.Consumer>
```
参考資料 [React Contextの使い方](https://qiita.com/ryokkkke/items/dc25111fcf52ea579d58)  

### Context のアンチパターン
1. 値本体と値を入れる関数を 1つ の Context に入れている
2. Provider の中に子コンポーネントを書いている

#### 1. 値本体と値を入れる関数を 1つ の Context に入れている
(悪い例)  
```js
const CountProvider: FC = ({ children }) => {
  const [count, setCount] = useState<number>(0);

  return (
    <countContext.Provider value={{ count, setCount }}>
      {children}
    </countContext.Provider>
  );
};
```
これをすると、setCount 関数自体は毎回同じでも、 count が変わるごとに新しいオブジェクトが生成されてしまうので、  
count に依存していないボタンコンポーネントもそれに引きづられてレンダリングされてしまうということになる   

そのため、値と値を変更する関数はそれぞれのContextを使用するべき  
```js
const CountProvider: FC = ({ children }) => {
  const [count, setCount] = useState<number>(0);

  return (
    <countContext.Provider value={count}>
      <setCountContext.Provider value={setCount}>
        {children}
      </setCountContext.Provider>
    </countContext.Provider>
  );
};
```

#### 2. Provider の中に子コンポーネントを書いている
(悪い例)  
```js
const Root = () => {
  const [count, setCount] = useState<number>(0);

  return (
    <countContext.Provider value={count}>
      <setCountContext.Provider value={setCount}>
        <App />
      </setCountContext.Provider>
    </countContext.Provider>
  );
};
```
これをすると、値が変わるたびにAppコンポーネントも再レンダリングされてしまう  
(いい例)
```js
// Provider は props で子コンポーネントを受ける
const CountProvider: FC = ({ children }) => {
  const [count, setCount] = useState<number>(0);

  return (
    <countContext.Provider value={count}>
      <setCountContext.Provider value={setCount}>
        {children}
      </setCountContext.Provider>
    </countContext.Provider>
  );
};

const Root = () => {
  return (
    <CountProvider>
      <App />
    </CountProvider>
  );
};
```
このようにProvider は props で子コンポーネントを受け取るべき  
参考資料 [正しく使う ReactContext](https://zenn.dev/yuta_ura/articles/react-context-api)  
