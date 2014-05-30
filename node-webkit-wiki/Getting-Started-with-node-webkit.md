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

![Capture4](https://f.cloud.github.com/assets/2891424/279875/e8572dd0-913d-11e2-8a82-ea021ca07ce6.PNG)

node-webkitにはネイティブUIをコントロールするためのAPIがあります。
それらをmenuやwindowのコントロールなどに使うことが出来ます。

menuを例としてその方法を示したいと思います。


```html
<html>
<head>
  <title> Menu </title>
</head>
<body>
<script>
// Load native UI library
var gui = require('nw.gui');

// Create an empty menu
var menu = new gui.Menu();

// Add some items with label
menu.append(new gui.MenuItem({ label: 'Item A' }));
menu.append(new gui.MenuItem({ label: 'Item B' }));
menu.append(new gui.MenuItem({ type: 'separator' }));
menu.append(new gui.MenuItem({ label: 'Item C' }));

// Remove one item
menu.removeAt(1);

// Iterate menu's items
for (var i = 0; i < menu.items.length; ++i) {
  console.log(menu.items[i]);
}

// Add a item and bind a callback to item
menu.append(new gui.MenuItem({
label: 'Click Me',
click: function() {
  // Create element in html body
  var element = document.createElement('div');
  element.appendChild(document.createTextNode('Clicked OK'));
  document.body.appendChild(element);
}
}));

// Popup as context menu
document.body.addEventListener('contextmenu', function(ev) { 
  ev.preventDefault();
  // Popup at place you click
  menu.popup(ev.x, ev.y);
  return false;
}, false);

// Get the current window
var win = gui.Window.get();

// Create a menubar for window menu
var menubar = new gui.Menu({ type: 'menubar' });

// Create a menuitem
var sub1 = new gui.Menu();


sub1.append(new gui.MenuItem({
label: 'Test1',
click: function() {
  var element = document.createElement('div');
  element.appendChild(document.createTextNode('Test 1'));
  document.body.appendChild(element);
}
}));

// You can have submenu!
menubar.append(new gui.MenuItem({ label: 'Sub1', submenu: sub1}));

//assign the menubar to window menu
win.menu = menubar;

// add a click event to an existing menuItem
menu.items[0].click = function() { 
    console.log("CLICK"); 
};

</script>  
</body>
</html>
```


### Example 3. Using node.js

node.jsのモジュールをDOMから直接使うことが出来ます。
なので、node-webkitでアプリを作ることは無限の可能性があります。


```html
<html>
<body>
<script>
// get the system platform using node.js
var os = require('os')
document.write('Our computer is: ', os.platform())
</script>
</body>
</html>
```


## Run and Package Apps

これで簡単なnode-webkitのアプリを書くことが出来るようになりました。
次は実行のさせ方とパッケージングの方法について学びましょう。

### Run the App




