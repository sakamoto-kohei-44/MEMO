# **[猫でもわかるHotwire入門 Turbo編](https://zenn.dev/shita1112/books/cat-hotwire-turbo)**

# ****チュートリアル2 Turboで管理画面をSPA風にする****

以下の6つの機能を画面遷移せずにその場で行えるようにしていく。

- ページネーション
- ソート
- 検索
- 編集
- 登録
- 削除

## ****Turbo Driveを有効化して画面遷移を高速化****

Turbo Driveを使うと画面遷移時に`<body>`だけを部分的に置換するから画面遷移は高速化する。

app/javascript/application.js

下記の行を削除

```jsx
- Turbo.session.drive = false
```

## ****ページネーションのTurbo Frames化****

※Turbo FramesはTurbo Driveをより部分的に置換させたもの。

Turbo Driveが`<body>`全体を置換するけど、Turbo Framesは`<turbo-frame>...</turbo-frame>`というHTMLタグのようなもので囲った箇所だけを置換する。

- ページネーションで次のページに移動する際はCat一覧（検索結果）の部分だけ更新すればいい
- 置換したい箇所を`turbo_frame_tag`で囲めばいい

```jsx
<%= turbo_frame_tag "cats-list" do %>
  <div>置換したい箇所</div>
<% end %>
```

レンダリング時には以下のようなHTMLになる

```jsx
<turbo-frame id="cats-list">
  <div>置換したい箇所</div>
</turbo-frame>
```

1. **速度**: 一部だけを更新するので、全体の読み込みよりも高速
2. **ユーザーエクスペリエンス**: ページの一部だけが更新されるので、ユーザーが入力した情報（例: 検索フォームのテキスト）が失われることがない。

## ****ソートのTurbo Frames化****

検索フォームが**`turbo-frame`**の外側にあるため、通常の方法ではTurbo Frameリクエストとして認識されません。そこで、検索フォームに特定の属性（**`data-turbo-frame`**）を追加することで、この問題を解決できる。

app/views/cats/index.html.erb

```jsx
 - <%= search_form_for @search do |f| %>

 + <%# data-turbo-frame属性を指定する %>
 + <%= search_form_for @search, html: { data: { turbo_frame: "cats-list" } } do |f| %>
```

`search_form_for`は↓のようなHTMLになる。

```jsx
<%# data-turbo-frame="cats-list"によって、Turbo Frameリクエストになる %>
<form class="cat_search" id="cat_search" data-turbo-frame="cats-list" action="/cats" accept-charset="UTF-8" method="get">
  ...
</form>
```

## ****編集のTurbo Frames化****

1. 編集リンククリック時の処理
2. 更新成功時の処理
3. 更新バリデーションエラー時の処理
4. キャンセル時の処理

### ****1. 編集リンククリック時の処理****

編集リンクをクリックした際に`_cat.html.erb`部分を`edit.html.erb`（内でrenderしている`_form.html.erb`）部分に置換するように修正する。

****実装手順:****

`_cat.html.erb`の中身全体を`<turbo-frame>`で囲う

app/views/cats/_cat.html.erb

```jsx
<%# 全体を`turbo_frame_tag`で囲う %>
<%= turbo_frame_tag cat do %>
  <div class="row py-2 border-top">
    <div class="col-4 my-auto">
      <%= cat.name %>
    </div>
    <div class="col-4 my-auto">
      <%= cat.age %>
    </div>
    <div class="col-4">
      <div class="d-flex justify-content-end">
        <%= link_to "編集", edit_cat_path(cat), class: "btn btn-sm btn-outline-primary me-2" %>
        <%= button_to "削除", cat, method: :delete, class: "btn btn-sm btn-outline-danger" %>
      </div>
    </div>
  </div>
<% end %>
```

- この際に`turbo_frame_tag`の引数には`cat`オブジェクトを渡す。すると`turbo_frame_tag`が内部で`dom_id`を利用して`cat`を`"cat_1"`のようなidに変換してくれる。`_cat.html.erb`はCatの数だけレンダリングされるので、`<turbo-frame>`のidがかぶらないようにオブジェクトを引数に渡す必要がある。
    
    わかりやすく説明すると、Railsのビューでは、繰り返し表示するための部分テンプレートがよく使われます。例えば、**`_cat.html.erb`**は、Catモデルの各レコードごとにHTMLを生成するための部分テンプレートです。
    
    **`Turbo Frames`**を使って部分的な更新を行う際には、その更新対象の領域を特定するための一意のIDが必要です。この一意のIDは**`<turbo-frame>`**タグの**`id`**属性として指定されます。
    
    ここで問題となるのは、複数のCatレコードがある場合、それぞれのレコードに対して一意のIDを持たせる必要があります。例えば、以下のように3つのCatレコードがある場合：
    
    ```
    Copy code
    Cat 1
    Cat 2
    Cat 3
    
    ```
    
    これら3つのエントリーはそれぞれ異なる**`<turbo-frame>`**タグのIDを持たなければなりません。
    
    ここで**`turbo_frame_tag`**ヘルパーはとても賢いです。引数として渡されたオブジェクト（この場合はcat）に基づいて一意のIDを生成します。この一意のIDの生成には**`dom_id`**ヘルパーが内部的に使用されます。
    
    たとえば、IDが1のCatオブジェクトがある場合、**`dom_id`**ヘルパーは**`"cat_1"`**という文字列を生成します。このように、各Catオブジェクトは一意のIDを持ちますので、**`<turbo-frame>`**タグのIDも一意になります。
    
    結果的に、これにより以下のようなHTMLが生成されることになります：
    
    ```bash
    bashCopy code
    <turbo-frame id="cat_1">...</turbo-frame>
    <turbo-frame id="cat_2">...</turbo-frame>
    <turbo-frame id="cat_3">...</turbo-frame>
    
    ```
    
    このように、各Catレコードに対して一意の**`turbo-frame`**タグが生成され、部分更新が正確に行われることを保証します。
    

`edit.html.erb`内からrenderしている`_form.html.erb`を修正

1. 全体を`turbo_frame_tag`で囲う
2. 見た目を`_cat.html.erb`に合わせて整える(この修正により、ユーザーにとって編集画面と一覧画面がシームレスに連携しているように見える)

**編集リンクの挙動**:

- 編集リンクをクリックすると、**`edit.html.erb`**がサーバーからレンダリングされて返される。
- ここでTurbo Framesの魔法が発動。**`<turbo-frame>`**で囲まれた部分だけがブラウザ上で動的に置換され、ページ全体の再読み込みを避けることができる。
- 例えば、IDが1のCatオブジェクトの編集リンクをクリックした場合、**`_cat.html.erb`**の**`<turbo-frame id="cat_1">`**部分が、**`_form.html.erb`**の**`<turbo-frame id="cat_1">`**部分に置換される。これにより、一覧画面上で直接編集フォームが表示されるように見える