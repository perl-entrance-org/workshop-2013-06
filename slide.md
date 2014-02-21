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
