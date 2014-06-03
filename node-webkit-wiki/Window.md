
_Window APIはnode-webkitのバージョンがv0.3.0以上である必要があります。_

`Window`はDOMの`window`をラップしたオブジェクトで、拡張されていてwindowのイベントを受け取ることが出来ます。

全ての`Window`はEventEmitterのインスタンスオブジェクトです。
`Window.on(...)`という形式でネイティブwindowのイベントを受け取ることが出来ます。

## Synopsis

```javascript
// Load native UI library
var gui = require('nw.gui'); //or global.window.nwDispatcher.requireNwGui() (see https://github.com/rogerwang/node-webkit/issues/707)

// Get the current window
var win = gui.Window.get();

// Listen to the minimize event
win.on('minimize', function() {
  console.log('Window is minimized');
});

// Minimize the window
win.minimize();

// Unlisten the minimize event
win.removeAllListeners('minimize');

// Create a new window and get it
var new_win = gui.Window.get(
  window.open('https://github.com')
);

// And listen to new window's focus event
new_win.on('focus', function() {
  console.log('New window is focused');
});

```

## Reference


### get([window_object])

`window_object`が指定されていない時は現在のwindowの`Window`オブジェクトを返して、指定されているときは指定された
`window_object`の`Window`オブジェクトを返します。

```javascript
// Get the current window
var win = gui.Window.get();

// Create a new window and get it
var new_win = gui.Window.get(
  window.open('https://github.com')
);
```


### open(url[, options])

新しいwindowを開いて`url`をロードします。さらにオプションを追加で指定することが出来ます。
全ての`Window`のsubfieldには[Manifestformat](https://github.com/rogerwang/node-webkit/wiki/Manifest-format)が使われています。v0.4.0からは`new-instance`というbooleanのフィールドを使うことが出来、新しいNodeのインスタンス(webkitのプロセス)を立ち上げることが出来ます。
v0.9.0と0.8.5からは`inject-js-start`と`inject-js-end`が使用出来、JavaScriptを差し込むことが出来ます。詳しくはManifestformatを参照してください。

v0.7.3からはwindowを開いた時にデフォルトではフォーカスが当たらなくなりました。それはプラットフォーム間での挙動を統一するためです。もしデフォルトでフォーカスを当てたい場合は、オプションで`focus`を`true`に設定してください。

```javascript
var win = gui.Window.open('https://github.com', {
  position: 'center',
  width: 901,
  height: 127
});
```


### Window.window

DOMの`window`オブジェクトを取得します。


### Window.x/Window.y

windowのleftとtopのoffsetを取得・設定することが出来ます。


### Window.width/Window.height

windowのサイズを取得・設定することが出来ます。


### Window.title

windowのタイトルを取得・設定することが出来ます。


