# Perl入学式 #6

# 今日の内容
Mojolicious を使って Web アプリケーションを作ろう!

# 準備
- Mojolicious

## Mojolicious のインストール

    cpanm Mojolicious

## Mojolicious
- Perl の Web アプリケーションフレームワーク (通称 WAF)
- Perl 5.10.1 以降で動作する (コアモジュール以外に依存モジュールがない)
- [mojolicious.us](http://mojolicio.us/)

## 雛形を作る

    $ mojo generate lite_app BBS

## 起動

    $ morbo ./BBS

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
