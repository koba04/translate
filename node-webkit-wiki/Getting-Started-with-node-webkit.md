# Getting Started with node-webkit

この章はnode-webkitを始めるためのチュートリアルになっています。
node-webkitのバイナリはすでに持っているものとして進めます。
(バイナリはREADMEのDownloadセクションから入手することが出来ます。もし自身でビルドしたい場合はBuilding node-webkitのセクションを代わりに参照してください。)

node-webkitはChromiumとnode.jsをベースにしています。
node.jsのモジュールをDOMから直接使用することができます。
そしてWebのテクノロジーを使うことも出来ます。
さらに、簡単にWeb Applicationをネイティブアプリとしてパッケージングすることも出来ます。

## Basics

nodd-webkitの紹介をするために、可能なかぎりシンプルプログラムから始めましょう。

### Example 1. Hello World

![Capture3](https://f.cloud.github.com/assets/2891424/279516/5fba0cca-912b-11e2-983d-c2e8a66c3706.PNG)

`index.html`を作成します。

```html
<html>
<head>
<title>Hello World!</title>
</head>
<body>
<h1>Hello World!</h1>
</body>
</html>
```

`package.json`を作成します。

```json
{
  "name": "nw-demo",
  "main": "index.html"
}
```

`index.html`と`package.json`をzipに圧縮して`app.nw`という名前に変更します。

    app.nw
    |-- package.json
    `-- index.html


あなたの環境にあった事前のダウンロードしたバイナリを使って`app.nw`を開きます。

```bash
$ ./nw app.nw
```

Note: Windowsでは、`app.nw`を`nw.exe`にドラッグして開くことも出来ます。


### Example 2. Native UI API

node-webkitにはネイティブUIをコントロールするためのAPIがあります。
それらをmenuやwindowのコントロールなどに使うことが出来ます。

menuを例としてその方法を示したいと思います。


### Example 3. Using node.js

node.jsのモジュールをDOMから直接使うことが出来ます。
なので、node-webkitでアプリを作ることは無限の可能性があります。

## Run and Package Apps

これで簡単なnode-webkitのアプリを書くことが出来るようになりました。
次は実行のさせ方とパッケージングの方法について学びましょう。

### Run the App




