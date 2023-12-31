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

### ****2. 更新バリデーションエラー時の処理****

 編集のリクエスト

あなたが「更新する」ボタンをクリックした時、背後での動きはこんな感じです：

1. Turboは通常のフォーム送信をインターセプト（横取り）して、Turbo Frameリクエストを送信します。
2. サーバはそのリクエストを受け取り、更新を試みます。

バリデーションエラー発生

サーバー側で、送信されたデータが何らかの理由でバリデーションに通らなかった場合：

1. サーバは編集ページ（**`edit.html.erb`**）をレンダリングします。
2. そして、HTTPステータスとして**`422 unprocessable_entity`**をクライアントに返します。このステータスは、「処理できないエンティティ」という意味で、バリデーションエラーが起きたことを示すのに適しています。

クライアント側のレスポンス処理

1. Turboがサーバからのレスポンスを受け取ります。
2. 受け取ったレスポンスの中の**`<turbo-frame>`**タグの内容で、現在のページ上の対応する**`<turbo-frame>`**の中身を置き換えます。
3. この結果、バリデーションエラーのメッセージが表示された編集フォームがユーザーに再表示されます。

### ****3. 更新成功時の処理****

更新ボタンのクリック

ユーザーが編集フォームで情報を変更した後、「更新する」ボタンをクリックします。この時の背後での動きは以下のとおり：

1. Turboがフォーム送信をインターセプトします。
2. インターセプトしたリクエスト（Turbo Frameリクエスト）がサーバに送信されます。

サーバ側の処理

サーバは送られてきたデータを受け取り、更新を試みます。データが正常であれば：

1. 更新が完了し、サーバは**`cats#show`**アクションをトリガーして該当するCatの詳細ページへとリダイレクト指示を出します。
2. **`cats#show`**アクションでは、show.html.erbがレンダリングされます。
3. その結果として生成されたHTMLがレスポンスとしてクライアント側に送信されます。

クライアント側のレスポンス処理

1. クライアント側のTurboがサーバからのレスポンスを受け取ります。
2. show.html.erb内の**`<turbo-frame>`**タグの内容で、現在表示されているページの対応する**`<turbo-frame>`**タグの中身を置き換えます。
3. ユーザーには、更新された情報が反映されたCatの詳細ページが表示されます。

### ****4. キャンセル時の処理****

1. クライアント側: 「キャンセル」ボタンをクリックすると、サーバーにTurbo Frameリクエストを送る
2. サーバー側: `cats#show`で`show.html.erb`のレンダリング結果をレスポンスする
3. クライアント側: `show.html.erb`（でrenderしている`_cat.html.erb`）の`<turbo-frame>`部分を置換して、Catの詳細を表示する。

### ****編集のTurbo Streams化****

### **Turbo Streamsとは？**

Turbo Streamsは、Hotwireの一部として、特定のページ上の部分的な更新を容易に行うための仕組みです。HTMLの特定の部分を効率的に更新するための手段として提供されています。

### **Turbo Streamsの利点：**

1. **複数の更新を一つのレスポンスで**: 一つのサーバーレスポンスで複数のページ部分を更新できます。
2. **動的な操作**: **`replace`**、**`append`**、**`prepend`**など、さまざまな操作を用いてDOMを更新できます。
3. **サーバーサイド主導**: クライアントサイドでのJavaScriptの量を削減し、ロジックをサーバーサイドに集中させることができます。

### **猫の例での動作の流れ:**

1. **ユーザーのアクション**: ユーザーが猫の情報を更新し、更新ボタンをクリックします。
2. **サーバーサイドの処理**:
    - **`@cat.update(cat_params)`**が成功した場合、**`update.turbo_stream.erb`**をレンダリングします。
    - これは、通常のHTMLレスポンスとは異なり、Turbo Stream形式のレスポンスをクライアントに返します。
3. **`update.turbo_stream.erb`**:
    - このテンプレートは**`turbo_stream.replace @cat`**を使用して、ページの特定の部分（この場合は猫の情報）を置き換える指示を生成します。
    - 生成されるのは**`<turbo-stream>`**タグです。このタグは、クライアントにどの部分をどのように更新するかの情報を伝える役割があります。
4. **クライアントサイドの処理**:
    - **`<turbo-stream>`**タグの内容を受け取ったブラウザは、指示に従ってページの内容を更新します。
    - この例では、猫の情報が新しい内容に置き換えられます。

### **`<turbo-stream>`タグについての詳細:**

このタグは、操作（**`action`**）と対象（**`target`**）を指定することで、DOMのどの部分をどのように変更するかを定義します。**`action`**には、**`replace`**、**`append`**、**`prepend`**などの操作が指定できます。**`target`**は、更新するDOM要素のIDを指定します。

例えば、**`<turbo-stream action="replace" target="cat_1">`**は、ID **`cat_1`**を持つDOM要素を、指定された内容で置き換えることを意味します。

### **テンプレートの名前: `update.turbo_stream.erb`**

通常、Railsではビューテンプレートは**`.html.erb`**という拡張子を持ちます。しかし、Turbo Streamsを使う場合、返すのは通常のHTML全体ではなく、**`<turbo-stream>`**要素を含む特定のフォーマットです。これを示すために、**`.turbo_stream.erb`**という拡張子を使います。Railsはこの拡張子を見て、レスポンスのフォーマットを**`text/vnd.turbo-stream.html`**として処理します。

### **Turbo Streamsの動作の流れ:**

1. **クライアント側**:
    - 「更新する」ボタンをクリックします。
    - Turboはこのフォームリクエストをインターセプトし、**`Accept`**ヘッダーに**`turbo_stream`**フォーマットを追加して送信します。
2. **サーバー側**:
    - サーバーはこの**`Accept`**ヘッダーを見て、レスポンスのフォーマットが**`turbo_stream`**であることを認識します。
    - これにより、**`update.turbo_stream.erb`**のような**`.turbo_stream.erb`**拡張子を持つテンプレートがレンダリング対象となります。
3. **クライアント側**:
    - レスポンスとして返ってきた**`<turbo-stream>`**要素を受け取ります。
    - Turboはこの**`<turbo-stream>`**要素を解析し、ページ上の対応する要素（この場合は**`#cat_1`**）を更新します。

### **Turbo Frames vs Turbo Streams:**

- **Turbo Frames**は特定の部分（frame）のみを更新するためのもので、通常の画面遷移に近い振る舞いをします。
- **Turbo Streams**は複数の部分を同時に、または特定の方法で更新するためのものです。

このチュートリアルのアプリでは、**`update`**, **`create`**, **`destroy`**の成功時には複数の要素（例: 一覧表示とFlashメッセージ）を更新する必要があるため、Turbo Streamsが使用されています。他のアクションでは、一つの要素のみを更新するため、Turbo Framesが使用されています。

### ****FlashのTurbo Streams化****

### **`turbo_stream.update`**ヘルパーの役割

**`turbo_stream.update`**は、その名の通り、特定のHTML要素を「更新」するためのヘルパーメソッドです。これにより、HTMLの一部を新しい内容で書き換えることができます。

### 使い方

このヘルパーを使う時、主に2つの情報を指定します：

1. 更新したいHTML要素のID
2. 更新する内容（通常はパーシャルを指定）

例：

```
erbCopy code
<%= turbo_stream.update "flash", partial: "flash" %>

```

上記のコードでは、**`"flash"`**というIDを持つHTML要素を、**`_flash.html.erb`**というパーシャルの内容で更新します。

### 実際の動き

1. クライアント側（ブラウザ）で「更新する」ボタンがクリックされる。
2. サーバー側で更新処理が行われ、成功した場合に**`update.turbo_stream.erb`**がレンダリングされる。
3. **`update.turbo_stream.erb`**には、元のHTML要素（この場合、**`<div id="flash">...</div>`**）を新しい内容で更新する指示が含まれている。
4. ブラウザがこの指示に従い、該当するHTML要素の内容を書き換える。結果として、新しいFlashメッセージが表示される。

---

要するに、**`turbo_stream.update`**ヘルパーを使うことで、ページのリロードや全体の再描画なしに、ページ内の特定の部分だけを新しい内容で更新することができるのです。今回の例では、Flashメッセージの部分を効率的に更新しています。

### ****Turbo Streamsでの削除機能の実装****

**目的**: ユーザーがデータを削除する際に、画面全体をリロードせずにその変更を動的に反映することを目指しています。

1. **コントローラーの変更**:
    - **`redirect_to`**の行を削除して、削除成功後のリダイレクトを停止します。
    - 代わりに**`flash.now.notice`**を使って、削除に成功した際のメッセージを設定します。こうすることで、リダイレクトせずともFlashメッセージが表示されるようになります。
2. **destroy.turbo_stream.erb**:
    - このファイルは削除成功時にクライアントに返すTurbo Streamのレスポンスを定義しています。
    - **`<%= turbo_stream.remove @cat %>`**: このコードは、削除されたCatのHTML要素をページから削除します。
    - **`<%= turbo_stream_flash %>`**: これは前に作成したヘルパーを使って、Flashメッセージを表示します。
3. **削除時の確認ダイアログ**:
    - ユーザーが誤ってデータを削除するのを防ぐため、削除を実行する前に確認ダイアログを表示します。
    - Rails 6では**`data-confirm`**を使用していましたが、Rails 7では**`data-turbo-confirm`**を使用します。
    - **`button_to`**ヘルパーは、実際にはフォームを作成するので、**`form:`**オプションを使って、フォーム要素に属性を追加することができます。
