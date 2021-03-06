# ビューバインディング

(※以前は「テンプレートバインディング」と呼ばれていました)

すべての ```views/*.html``` ファイル (ビューファイル) は、```view``` バインディングを利用することで、他のビューの内部でレンダリングすることが可能です。


```html
{{ view "header" }}
```

```view``` に渡す文字列は *ビューのパス* でなければなりません。ビューと後述するタグは、同様の方法でビューとコントローラーを探索します。

誰もが、アプリケーションの各パートごとのスコープと必要な機能を事前に定めておきたいと考えていることでしょう。しかし現実には、予想していた以上に規模が大きくなってしまったりして、思った通りに上手くいかないこともありがちです。ビューとタグを利用すると、再利用可能なコードやビューを簡単にセットアップすることが可能です。ビューやタグが大きくなってきたら、その呼び出し側には変更を加えることなく、コントロールの場所を移すことができます。

サンプルのビューを例として、どのように探索されるのか見てみましょう。

```html
{{ view "header" }}
```

「header」の文字列が与えられると、Volt は次の場所からビューのファイルを (順番に) 探します:

| Section   | View File    | View Folder    | Component   |
|-----------|--------------|----------------|-------------|
| header    |              |                |             |
| :body     | header.html  |                |             |
| :body     | index.html   | header         |             |
| :body     | index.html   | index          | header      |
| :body     | index.html   | index          | gems/header |

ビューが見つかると、それに関連するコントローラーがまず読み込まれます。

上記の各パートについて説明します:

1. セクション - ビューはセクションから構成されます。セクションは ```<:セクション名>``` で始まり、閉じタグはありません。Volt はまず同じビュー内のセクションを探します。

2. ビュー - 次に、Volt はテンプレートのパスにあるビューのファイルを探します。もしそこで見つかれば、ビューの body セクションにレンダリングします。

3. ビューのフォルダー - 上記で見つからなければ、Volt はビューのフォルダーの中で、コントローラーの名前のファイル、もしくは index.html を探します。そして、:body セクションにレンダリングされます。もしコントローラーがビューのフォルダーに存在した場合には、そのコントローラーのインスタンスが新しく生成され、そのインスタンスでレンダリングが行われます。

4. コンポーネント - 次に、app/ 以下のすべてのフォルダーをチェックします。対象となるビューのパスは ```{component}/index/index.html``` で、:body セクションを持っているものになります。

5. Gem - 最後に、```volt``` で始まるすべての gem の app フォルダをチェックします。コンポーネントに対しても上記と同様のパスでチェックされます。


また、ビューバインディングを作成するときに、その名前に複数の探索パスを指定することもできます。各パートは ```/``` で区切る必要があります。例:

```html
    {{ view "blog/comments" }}
```

上記の例の場合は以下のように探索します:

| セクション   | ビューのファイル | ビューのフォルダー | コンポーネント |
|--------------|------------------|--------------------|----------------|
| :comments    | blog.html        |                    |                |
| :body        | comments.html    | blog               |                |
| :body        | index.html       | comments           | blog           |
| :body        | index.html       | comments           | gems/blog      |

タグやビューのためのファイルが見つかれば、それにマッチするコントローラーを探します。もし、ビューのファイルがコントローラーに関連付けられていなければ、新しい ```ModelController``` が使われます。コントローラーが見つかって読み込まれると、それに対応する「アクション (action)」メソッドが (存在すれば) コントローラー上で実行されます。アクションメソッドのデフォルトは、(.htmlを除いた)ビューファイルの名称と同じです。
