# Perl入学式 #6

# 今日の内容
Mojolicious を使って Web アプリケーションを作ろう!

- Mojolicious 入門
- BBS 作成

## 準備
- Mojolicious

## Mojolicious のインストール

    cpanm Mojolicious

## Mojolicious
- Perl の Web アプリケーションフレームワーク (通称 WAF)
- Perl 5.10.1 以降で動作する (コアモジュール以外に依存モジュールがない)
- [mojolicious.us](http://mojolicio.us/)

## 雛形を作る

    $ mojo generate lite_app hello

## 起動

    $ morbo ./hello

- Web ブラウザで `localhost:3000` にアクセスしてみましょう

## コード解説

    #!/usr/bin/env perl
    use Mojolicious::Lite;

- `Mojolicious::Lite` は `Mojolicious` の Lite 版
- `Mojolicious::Lite` は 1 つのスクリプトで複数のページを作成
  - `Mojolicious` は 1 ページ= 1 スクリプト

## コード解説

    #!/usr/bin/env perl
    use Mojolicious::Lite;

- `use Mojolicious::Lite;` とすることで, 自動的に `use warnings;`, `use strict` が有効になる

## コード解説
    get '/' => sub {
      my $self = shift;
      $self->render('index');
    };

- `get '/' => sub { ... };` は get メソッドで `'/'` にアクセスしたとき, sub 内の処理を行う
- 2 行目は「お約束」のようなもの
- 3 行目 はテンプレート `index` を使ってコンテンツを生成

## コード解説
    app->start;

- アプリケーションとして実行します, というような意味
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
- `<%= xxx %>` がテンプレートの中で使用できる変数に相当する

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
  - `render` で profile テンプレートを指定する

# BBS

## 雛形を作る
    $ mojo generate lite_app BBS

## use utf8;
- `use utf8;` した状態の Perl は日本語などのマルチバイト文字でも、文字として正しく扱うことができる
- ターミナルで試してみよう
    # use utf8;していない状態
    $ perl -e 'print length "abcあいう";'
    # use utf8;した状態
    $ perl -Mutf8 -e 'print length "abcあいう";'

## PODRENDER
    # Documentation browser under "/perldoc" # 削除
    plugin 'PODRenderer'; # 削除

- PODRENDER は Mojolicious プラグインで, perldoc を綺麗に見るためのもの
- 削除する前に、[localhost:3000/perldoc](http://localhost:3000/perldoc) にアクセスしてみよう

## FORM 作成
    Welcome to the Mojolicious real-time web framework! # 削除

- もともとの文字列を削除する

## FORM 作成
    %= form_for '/' => begin
      %= text_field 'body'
      %= submit_button '投稿する'
    % end

- 削除したところに, フォームを出力するコードを書く
  - form\_for, text\_field, submit\_buttonなどは Mojolicious のhelperという機能で定義されたPerlの関数(サブルーチン)

## FORM 作成
- ここまで出来たら, 保存してから, ブラウザをリロード (あるいは[http://localhost:3000](http://localhost:3000)にアクセス)
- しかし, フォームから投稿しても画面上は何も変わらない. 次に, フォームから投稿された文字列を画面に表示するようにしてみよう

## GET
      %= submit_button '投稿する'
    % end
    <p><%= $body %></p>

- まずはテンプレートを上記のように変更する
- `<%= $body %> は, テンプレート内の変数

## GET
    get '/' => sub {
      my $self = shift;
      my $body = $self->param('body'); # 追加
      $self->render('index');
    };

- form の情報を取得するために, 上記のように 1 行追加する
- `$self->param('body')` は フォームから投稿した `body` という名前の値を取得する

## GET
    get '/' => sub {
      my $self = shift;
      my $body = $self->param('body');
      $self->stash(body => $body); # 追加
      $self->render('index');
    };

- 取得した情報をテンプレートに渡すため, `$self->stash(body => $body)` を挿入する
- `body` に変数 `$body` を渡したので, テンプレートで `$body` が使用可能になる

## GET
- ここまで出来たら, 保存してから, ブラウザをリロード (あるいは[http://localhost:3000](http://localhost:3000)にアクセス)
- フォームに文字を入力して, ボタンをクリックしてみよう
