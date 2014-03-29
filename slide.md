# Perl入学式 #6

# 今日の内容
Mojolicious を使って Web アプリケーションを作ろう!

- Mojolicious の準備
- HTTP の基礎
- Mojolicious 入門
- 簡易 BBS の作成

# Mojolicious の準備

## Mojolicious のインストール

    $ cpanm Mojolicious

- Mojolicious とは?
    - Perl の Web アプリケーションフレームワーク (通称 WAF)
    - Perl 5.10.1 以降で動作する (コアモジュール以外に依存モジュールがない)
    - 詳しくは: [mojolicious.us](http://mojolicio.us/)
    - ドキュメントの日本語役: [Mojoliciousドキュメント 日本語訳](https://github.com/yuki-kimoto/mojolicious-guides-japanese/wiki)

## Mojolicious のインストール

- cpanm がインストールされていない場合, 次のようにして導入(準備)することができます
    - [http://latest.mojolicio.us/](http://latest.mojolicio.us/) から最新版をダウンロード
    - ダウンロードしたzipファイルを解凍
- 以後, 作業は基本的に解凍したディレクトリの中で行います
    - 一番いいのは cpanm 等でインストールすることなので, 出来ていない方は後日チャレンジしてみよう!

# HTTP の基礎

## HTTP の基礎
- HTTP ... HyperText Transfer Protocol
    - Web ブラウザと Web サーバの間でコンテンツの送受信を行うためのプロトコル
- 基本的な考え方は, サーバに｢何を｣｢どうしたいか｣を伝える
    - ｢何を｣が URL , ｢どうしたいか｣がメソッド

## HTTP のメソッド

- よく使うのは以下の2つ
    - GET
        - サーバからデータを取ってきたい時に使う
    - POST
        - GET とは逆に, サーバにデータを送りたい時に使う
        - フォームや, BBS への投稿など...

# Mojolicious 入門

## 雛形を作る

    $ mojo generate lite_app hello # cpanm でインストールした方
    $ perl -Ilib script/mojo generate lite_app hello # zipを解凍して準備した方

- Mojolicious を使った Web アプリケーションの簡単な雛形を作成します
    - Mojolicious の場合, `lite_app` 以外にも大規模な Web アプリケーション用の `app` という雛形が用意されています

## Web アプリケーションの起動

    $ morbo ./hello # cpanm でインストールした方
    $ perl -Ilib script/morbo ./hello # zipを解凍して準備した方

- Web ブラウザで `localhost:3000` にアクセスしてみましょう!

## コード解説

    #!/usr/bin/env perl
    use Mojolicious::Lite;

- `Mojolicious::Lite` は `Mojolicious` の Lite 版
    - `Mojolicious::Lite` は 1 つのスクリプトで複数のページを作成
    - `Mojolicious` は 1 ページ = 1 スクリプト
- 総ページ数の少ない(1〜2ページ程度), 簡単な Web アプリケーションであれば Lite で十分

## コード解説

    #!/usr/bin/env perl
    use Mojolicious::Lite;

- `use Mojolicious::Lite;` とすることで, 自動的に `use warnings` と `use strict` が書かれているのと同じ(有効な)状態になる

## コード解説
    get '/' => sub {
      my $self = shift;
      $self->render('index');
    };

- `get '/' => sub { ... };` は, get メソッドで `'/'` というURLにアクセスしたとき, sub 内の処理を行う
- 2 行目は「お約束」のようなもの
- 3 行目 はテンプレート `index` を使ってコンテンツを生成

## コード解説
    app->start;

- アプリケーションを実行します, という意味
- これも「お約束」のようなもの

## コード解説
    __DATA__

    @@ index.html.ep
    % layout 'default';
    % title 'Welcome';
    Welcome to the Mojolicious real-time web framework!

- `ep` は `embedded perl` の略称
- `layout 'default';` は次項で説明するレイアウトファイルの指定
- `title` は `default.html.ep` の `title` 変数, `Welcome to...` は `content` 変数に渡される

## コード解説
    @@ layouts/default.html.ep
    <!DOCTYPE html>
    <html>
      <head><title><%= title %></title></head>
      <body><%= content %></body>
    </html>

- `localhost:3000` のソースと見比べてみましょう
- `<%= xxx %>` がテンプレートの中で使用できる変数に相当する(詳しくは後述)

## 変数を渡す
    get '/' => sub {
      my $self = shift;
      $self->stash(title => 'Hello');
      $self->render('index');
    };
    % title 'Welcome'; # => 削除する

- `$self->stash` で, テンプレート内の変数に変数を渡せる

## 練習問題
    @@ profile.html.ep
    <html>
    <head><title><%= $name %>のプロフィール</title></head>
    <body style='padding: 30px;'>
      私の名前は<%= $name %>です.<br>
      趣味は<%= $hobby %>で, 好きなプログラミング言語は<%= $language %>です.
    </body>
    </html>

- このようなテンプレートを用意し, stash で `name`, `hobby`, `language` 変数に値を代入し, 自己紹介ページを作成しよう
    - `render` で profile テンプレートを指定しましょう

## テンプレートの中の特殊記号

    % if ($num1 == 1 ) { # %
    %= 'hoge';    # %=
        <% if ($num2 == 1 ) { %> # <% ... %>
            <%= $hoge %>         # <%= ... %>
        <% } %>
    % }

- `%`のように, テンプレートの中で特別な意味を持つ記号がある

## テンプレートの中の特殊記号

    % if ($num1 == 1 ) { # %
        ...
    % }

- `%`が先頭にある行は, その後ろのPerlのコードを実行する

## テンプレートの中の特殊記号

    %= 'hoge';    # %=

- `%=`が先頭にある行は, その後ろのPerlのコードを実行して, その結果をテンプレート内に直接埋め込む
    - 返り値を出力するので, `print 'hoge';`と書いてしまうと`1`になる!
    - `print`の実行結果は`1`なので...

## テンプレートの中の特殊記号
    <% if ($num2 == 1 ) { %> # <% ... %>
        <%= $hoge %>         # <%= ... %>
    <% } %>

- `<% ... %>`, `<%= ... %>`は, それぞれ`%`, `%=`をHTMLタグやテキストの中で使えるようにしたもの

## 練習問題
- `fizzbuzz`テンプレートを用意して, 1から100までのfizzbuzzを表示してみましょう  
    - 但し, fizzbuzzの処理は全て`fizzbuzz`テンプレートの中に書くようにしましょう
- fizzbuzz
    - 3で割り切れる場合｢Fizz｣
    - 5で割り切れる場合｢Buzz｣
    - 3でも5でも割り切れる場合｢fizzbuzz｣
    - いずれも満たさない場合｢その数をそのまま｣

# 簡易 BBS の作成

## 雛形を作る
    $ mojo generate lite_app BBS

## PODRenderer
    # Documentation browser under "/perldoc" # 削除
    plugin 'PODRenderer'; # 削除

- PODRenderer は Mojolicious のプラグインで, perldoc を見れるようにするもの
- 削除する前に、[localhost:3000/perldoc](http://localhost:3000/perldoc) にアクセスしてみよう!

## FORM 作成
    Welcome to the Mojolicious real-time web framework! # 削除

- `index` テンプレートにある, もともとの文字列を削除する

## FORM 作成
    %= form_for '/' => begin
      %= text_field 'body'
      %= submit_button '投稿する'
    % end

- 削除したところに, フォームを出力するコードを書く
  - form\_for, text\_field, submit\_buttonなどは, Mojolicious のhelperという機能で定義されたPerlの関数(サブルーチン)

## FORM 作成
- ここまで出来たら, 保存してから, ブラウザをリロード (あるいは[http://localhost:3000](http://localhost:3000)にアクセス) してみましょう
- フォームから投稿しても画面上は何も変わりません
    - 次に, フォームから投稿された文字列を画面に表示するようにしてみよう

## GET
      %= submit_button '投稿する'
    % end
    <p><%= $entry %></p>

- まずは `index` テンプレートを上記のように変更する
- `<%= $entry %>` は, テンプレート内の変数

## GET
    get '/' => sub {
      my $self = shift;
      my $entry = $self->param('body'); # 追加
      $self->render('index');
    };

- form の情報を取得するために, 上記のように 1 行追加する
- `$self->param('body')` は フォームから投稿した `body` という名前の値を取得する

## GET
    get '/' => sub {
      my $self = shift;
      my $entry = $self->param('body');
      $self->stash(entry => $entry); # 追加
      $self->render('index');
    };

- 取得した情報をテンプレートに渡すため, `$self->stash(entry => $entry)` を挿入する
- `entry` に変数 `$entry` を渡したので, テンプレートで `$entry` が使用可能になる

## GET
- ここまで出来たら, 保存してからブラウザをリロード (あるいは[http://localhost:3000](http://localhost:3000)にアクセス) してみよう
- フォームに文字を入力して, ｢投稿する｣ボタンをクリックしてみよう!

## POST
- 先ほどのフォームは, HTTP でいうところのGETメソッドを利用してデータを送信していました
- GET でのリクエストは文字数の制限(おおよそ 2KB 程度)があるので、掲示板のような多くのデータを送信する必要がある場合は適当ではありません
  - このような場合, POST によるリクエストを行うとよいです

## POST
    @@ post.html.ep # 新しいテンプレートを用意する
    % layout 'default';
    % title '出力'; # タイトルを変更
    %= form_for '/post' => method => 'POST' => begin # 投稿先などを変更
      %= text_field 'body'
      %= submit_button '投稿する'
    % end
    <p><%= $entry %></p>

- `index.html.ep` の部分をコピーして, `post.html.ep` というテンプレートを作成する
- `form_for` に書いた `method => 'POST'` で, get ではなく post で送信するようになる

## POST
    @@ index.html.ep
    % layout 'default';
    % title '入力フォーム'; # タイトルを変更
    %= form_for '/post' => method => 'POST' => begin # 投稿先などを変更
      %= text_field 'body'
      %= submit_button '投稿する'
    % end

- `@@ index.html.ep` では, `$entry` を表示させないようにする
- その他, メソッドやタイトルも変更しておこう

## POST
    get '/' => sub {
      my $self = shift;
      $self->render('index');
    };

    post '/post' => sub {
      my $self = shift;
      my $entry = $self->param('body');
      $self->stash(entry => $entry);
      $self->render('post');
    };

- perl コードも変更しよう
  - 細々とした違いに注意!

## POST
- ここまで出来たら, 保存してから, ブラウザをリロード (あるいは[http://localhost:3000](http://localhost:3000)にアクセス) してみよう
- フォームに長い文字を入力して, ｢投稿する｣ボタンをクリックしてみよう!

## 記事を蓄える
- 記事を蓄えるための方法としては, いくつかの方法がある
    - DB (データベース) を利用
    - 外部ファイルに書き込み保存
- 今回は時間の制約上, データを蓄える方法として, **配列**を用いる
    - 言うまでもなく, 配列は Web アプリケーションが停止した時点で全てのデータが消えるので, 現実的ではない!
    - `MySQL`や`SQLite`などのデータベースを使うのがオススメです

## 記事を蓄える
    @@ index.html.ep
    % layout 'default';
    % title '入力フォーム';
    %= form_for '/post' => method => 'POST' => begin
      %= text_field 'body'
      %= submit_button '投稿する'
    % end
    % for my $entry (@{$entries}) {
        <p><%= $entry %></p>
    % }

- テンプレートに, 投稿済みの記事( `$entries` に格納されている )を表示するよう変更を加える

## 記事を蓄える
    my @entries = (); # 空の配列を宣言
    get '/' => sub {
      my $self = shift;
      $self->stash(entries => \@entries); # 配列のリファレンスをテンプレートに渡す
      $self->render('index');
    };

    post '/post' => sub {
      my $self = shift;
      my $entry = $self->param('body');
      push @entries, $entry; # 配列に格納
      $self->stash(entry => $entry);
      $self->render('post');
    };

## 記事を蓄える
- ここまで出来たら, 保存してから, ブラウザをリロード (あるいは[http://localhost:3000](http://localhost:3000)にアクセス) してみよう
- 文字の投稿をいくつか繰り返した後に, [http://localhost:3000](http://localhost:3000)にアクセスしてみよう!

## リダイレクト
- 別のページへ遷移(移動)するための機能
    - 今回の場合, `/post`で記事を投稿した後に記事を表示するページである`/`に戻るようにする
- Mojolicious ビルトインの `redirect_to` を使用すればよい

## redirect\_to
    post '/post' => sub {
      my $self = shift;
      my $entry = $self->param('body');
      push @entries, $entry;
      $self->stash(entry => $entry);
      $self->redirect_to('/'); # 追加
    };

- `redirect_to` を利用して, `/`へのページ遷移を追加する
  - `post`のテンプレートはもう必要ないので, 削除しても問題ない

## 最終問題

- これまで作成してきた簡易 BBS を, 改造してみましょう!
- 例えば...?
    - 名前/メールアドレスを入力/表示できるようにする...
    - メールアドレスが｢age｣であれば, 記事を`push`ではなく`unshift`する...
    - テンプレートを整理して, 見た目を綺麗にする...
        - Twitter Bootstrapを使ってみる...

## まとめ
- 非常に簡単ではありますが, BBS (のような) Web サービスを開発してみました
    - 時間の制約上, 紹介できなかった部分 (適切な記事の蓄え方など...) は紹介できませんでしたが, Perl を使った Web サービスの開発の基本的な流れは, このようになっています
    - 今日ここまで紹介してきた内容は, 基礎中の基礎です. ｢ Web サービスを作ろう!｣となると, やはりまだまだ挑戦しなければならない｢壁｣はいくつもあります
- その時困ったら, Perl入学式の資料や, スタッフを是非頼って下さい!

## まとめ

1年間お疲れ様でした!

