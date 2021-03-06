---
layout: post
title: "Frontendの課題2" 
date: 2022-01-06 
category: read 
excerpt: ""
---

## やったこと

フロントの課題を進めた  

### Github Chromaticって何？？
Chromaticは、UIフィードバックの収集、ビジュアルテスト、ドキュメンテーションを自動化することで、開発者は少ないマニュアル作業で迅速にイテレーションを行うことができる  
Storybookと繋げてデザインを公開したり、公開したデザインに対してコメントをつけたりテストや承認フローを追加したりできるサービス  

### SCSSの変数定義
SCSS の中で定義した変数名に含まれるアンダースコア 「`_`」と、ハイフン「`-`」 は同じ物として扱われる。   
例えば、 $color-text という名前で定義した変数は、 $color_text という名前でも参照できる。  

- Sassの変数名はスネークケースで書く
  ```css
  $base_margin: 12px;
  $base_padding: 12px;
  ```
- クラス名と同じく、Sassの変数名も、ファイル名と同じ接頭辞で始める。
  ```css
  /* GOOD */
  $header_color: #999;
  /* BAD */
  $color_of_header: #999;
  ```
- Sassのグローバル変数は_v.scssに書き、$v_で始める
- Sass変数名の命名ルール
  - @extendは避ける
  - mixinも極力避ける
  - レスポンシブ対応はmixinで行う
[font-familyの正しい指定方法](https://willcloud.jp/knowhow/font-family/)  
font-familyは実際にユーザーの端末に表示されるフォントはユーザー側の閲覧環境に依存する  
font-familyは、あらゆる閲覧環境を想定し、できるだけ多くの環境に対応したフォントを指定しておく必要がある  
#### font-familyプロパティには、「フォントファミリー名」か、「総称フォント名」のいづれかを指定する  
- フォントファミリー名
  ```css
  body {
    font-family: "Arial", "メイリオ";
  }
  ```
- 総称フォント名
  font-familyに指定できる代表的な総称フォント名は、以下の6つ  
  <img width="685" alt="スクリーンショット 2022-01-06 14 44 24" src="https://user-images.githubusercontent.com/78260526/148334677-5d7f9439-207f-4a14-99a1-894473b22a93.png">  
  総称フォントを指定した場合は、通常はOSやブラウザごとに設定されたデフォルトフォントが自動的に選ばれる  
  総称フォントには、sans-serif（ゴシック体）か、serif（明朝体）のいづれかを指定するのが一般的  

#### 必ず抑えておくべきポイント
- OSごとにフォント名を指定する
  Windows、macOS、iOS（iPhone、iPad）などのOSは、それぞれ標準フォント（初めから入っているフォント）が異なる  
  font-familyには、各OS表示用の複数のフォント名を指定しておくことが推奨  
- 最後に総称フォントを指定する
  指定したフォントが全て表示されなかった場合に備えて、最低限の指定として、値の最後に総称フォント（ゴシック体や明朝体）を指定  

ページ全体にcssを当てる場合は、cssを`html`に適用する  

#### align-items
flexboxアイテムを配置する際に、上部や下部、高さに合わせて自動調整が可能  
![スクリーンショット 2022-01-06 20 30 27](https://user-images.githubusercontent.com/78260526/148376435-103f3f4d-7a06-4c8b-b1ed-b6c1b4fcff69.png)  
