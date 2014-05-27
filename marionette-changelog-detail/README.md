# Marionette Changelog Detail

これは https://github.com/Puppets/marionette-changelog-detail を元にMarionette 2.0についての変更点をざっくりとまとめたドキュメントです。


## General

### Removed the `Marionette.$` proxy.

Backbone.$のプロキシとして存在していたMarionette.$は廃止されます。

かわりにBackbone.$を使いましょう


### triggerMethod now calls `on`MethodName before triggering the event. Previously it was the other way around.

triggerMethodによるonMethodNameの呼び出しはイベント(method:name)が発行される前に呼ばれるようになります。

v1ではmethod:nameのようなBackbone.Eventが発行された後にそれに対応するonMethodNameが呼ばれていましたがその順番が入れ替えられます。

イベントはまずそのクラスでハンドリングしてから外部に伝播させていった方がよいという考えからこのように変更されました。


### Stricter JSHint rules were implemented for more consistent style across the codebase.

JSHhntのルールが一貫性のあるより厳格なものになりました。

おそらくこの変更は利用者には影響はありません。


### A new property, `Marionette.VERSION`, was added.

Marionette.VERSIONというプロパティが追加されました。


### All instances of the word `type` in the API have been replaced with the word `class`

APIの中にあったtypeという表記をclassに置き換えられました。(例：regionType -> regionClass)

多くの開発者はMarionetteやBackboneのオブジェクトをtypeではなくclassとして扱っているので、混乱しないようにというのが理由です。


### Most classes now have `getOption` attached to their instance

Marionette.getOptionはthisを第一引数に渡すようなI/Fになっていましたが(Marionette.getOption(this, 'myOption'))、これは好ましくないという判断から、this.getOption('myOption')とメソッドの形式に変更しました。

もし古いI/Fを引き続き使用したい場合は、Marionette.proxyGetOptionで同じことが出来ますがdeprecated扱いですので対応することが推奨されています。


### Most classes now have `bindEntityEvents` attached to their instance

bindEntityEventsもgetOptionと同様です。


### Most classes now have `unbindEntityEvents` attached to their instance

unbindEntityEventsもgetOptionと同様です。


### The built version of Marionette comes in a UMD wrapper. 

これまでMarionetteは3つのビルド(WreqrやBabysitterも含まれてない本当にcoreだけのバージョン、coreがbundleされたバージョン、coreのUMDビルドバージョン)がありましたが、2つのビルドだけになりました。(bundleされているバージョンとbundleされてないバージョンで両方共UMDです)

* lib/coreの下にあったAMDのビルドがなくなってるぽいです。

この変更によって、ディレクトリ構成も変更されているので、UMDでMarionetteを使っている人はビルドのプロセスで新しいファイルを参照するようにする必要があります。


## Applications and Modules


### The arguments of a Module’s initialize function are now consistent with Module constructor argument order. 

Moduleのinitialize関数がcostructorの引数と一貫性を持つように引数の順番が変更されました。

initialize関数を追加したときにappのことを忘れていて、appを渡すようにした時に後方互換性から第三引数に追加した結果、initializeとconstructorで引数に一貫性が無くなってしまったので修正されました。

具体的には、引数が*options, moduleName, app*の順番だったのが、*moduleName, app, options*に変更されています。


### Application’s `initialize` triggerMethods have been renamed

"initialize:before"と"initialize:after"のイベントがなくなり、"before:start"と"start"(既存)になりました。

Backboneの世界ではオブジェクトはinitializeメソッドを持っていてインスタンス化された際に呼ばれます。なのでAplicationsでもこのようになっているのがよいと考えられていましたがApplicationsではそのように使われていませんでした。

またApplicationsはアプリケーションの開始時に呼ばれるメソッドとしてaddInitializerメソッドを持っています。この名前も好ましくないので将来、名前が変更されるかもしれません。


### The Application message system is now the global Channel

Backbone.Wreqrに追加されたChannelの仕組みを使って、EventAggregatorやCommandsやRequestResponseをglobalというチャンネルで関連付けるようになりました。


### Applications have two new triggerMethods: `before:region:add` and `before:region:remove`

regionの追加、削除の前に`before:region:add`と`before:region:remove`がtriggerMethodとして呼ばれるようになりました。


## Controllers

### `stopListening` is now called after the `destroy` triggerMethod is called.

stopLiteningがdestroyのtriggerMethodの後に呼ばれるようになりました。

Viewや他のclassではstopListeningはdestoryメソッドの中で呼ばれていて、closeした後に呼ばれているのでそれとの一貫性があるように変更されました。


## Behaviors

### `Behaviors` now support a means to group behaviors together.

複数のBehaiviorsを1つのBehaiviorsにまとめることが出来るようになりました。

modalとform validationとkeybindingの組み合わせを複数のViewで使いたいとき、それぞれのViewのbehaiviorsに指定するのではなく、3つを1つのBehaiviorsにしてそれを指定することが出来ます。

```javascript
var Input = Marionette.Behavior.extend({
  behaviors: {
    Modal: {},
    FormValidation: {},
    KeyBinding: {}
  }
});
```


## Regions

### Regions now throw an error when they are instantiated with a nonexistent el.

Regionsはelに指定されているDOMがあるときしか動作しないのでelに指定されたDOMが取得できない時は例外を投げるようになりました。

これまでもRegionsをインスタンス化する際にはチェックされていましたが、viewをshowする際には行われていなくてdebugを難しくさせていたので改善されました。

※ インスタンス化された時について書かれているように思えるけど、Reginonsがインスタンス化された際のチェックは1.8.6ではすでに入っていたのでshowするときにチェックが追加されたことだと判断しました。


### Regions now use `.innerHtml` when clearing contents as compared this `this.$el.empty()`.

Regionsの$elを空にするときに、*this.$el.empty()*が使われていたところを.innerHTMLで行うようになりました。

この変更はパフォーマンス改善が目的です。この変更でイベントリスナーのクリーンアップが行われなくなりますが、MarionetteではRegionsにイベントリスナーを設定することはなくて、Regionsの中にあるViewのイベントリスナーはView自身がクリーンアップするので問題ありません。

jQuery pluginsや独自にイベントリスナーをViewに設定している場合は、onBeforeDestroyを使ってクリーンアップを行ってください。


### `region.show` now return `this`, just like how a view’s render returns `this`

region.showがview.renderと同じようにthisを返すようになりました。

Backboneではrenderメソッドはthisを返すという決まりがあるので、region.showもそれに合わせました。


### Regions trigger two new events, `before:swap` and `swap`, when it swaps views when calling `show`.

showのtriggerMethodの他に、すでにviewが表示されている時に"before:swap"と"swap"というイベントが発行されるようになりました。


### Region’s el can now be a DOM node / DOM string selector / Jquery selector instance, just like a View’s el.

Regionsのelの指定もViewと同様にDOMノードやクエリセレクターやjqueryのインスタンスなど、柔軟に指定出来るようになりました。


### Regions now expose region.el and region.$el, just like View’s.

Regionsの生のelもViewと同様に$elで参照出来るようになりました。selectorにアクセスしたい場合はthis.$el.selectorでアクセスすることが出来ます。


### Calling show on a region with the same view that is currently shown is now a noop.

currentViewと同じViewをshowしても何もしなくなりました。

これまではrerenderされていましたが、同じViewの場合は何もしなくなります。同じViewの場合でもrerenderしたい場合はforceShow:trueオプションを渡します。

```js
this.region.show(myView, {forceShow: true});
```


### Regions now fire a new triggerMethod, `before:destroy`, triggerMethod.

他のMarionetteのClassと同様にRegionsでもviewがdestoryされるときに"before:destory"のtriggerMethodを呼ぶようになりました。


## Views


### Returning false from `onBeforeClose` no longer prevents the view from closing.

onBeforeCloseでfalseを返してもviewがcloseするのをやめることが出来なくなりました。

closeをやめることが出来るのは便利でしたが、このtriggerMethodだけ独自にこのような挙動になっていて一貫性がなかったので廃止されました。

同じようなことをしたい場合は下記のようにclose自体を呼ばないようにしてください。

```js
if (myCondition) {
  this.close();
} else {
  // I am not closing!
}
```


### rename `close` to `destroy` for views.

view.closeがview.destoryに変更されました。

これはviewを完全に削除するメソッドであり、closeメソッドだと再び表示出来そうに思われるのでdestoryという名前に変更されました。


### Removes duplicate and inconsistent `itemView` events.

itemView:*として発行されるイベントと重複している"item:before:render"や"item:rendered"といったtriggerMethodは削除されました。


### A new triggerMethod was added to collectionView: `before:child:remove`. 

collectionViewでitemViewが削除される前に"before:child:remove"のtriggerMethodが呼ばれるようになりました。


### childEvents callbacks no longer receives the event name as the first argument.

childEventsのコールバックの第一引数にイベント名が渡されなくなりました。

これはtriggersとの整合性を取るためです。


### `itemView` within a `collectionView` is now known as `childView`.

collectionViewの各要素をitemViewとして扱っていましたが、これはただのitemViewと混乱してしまうのでchildViewという名前になりました。


### `compositeView` now calls `\_onCollectionAdd` when adding a child model as compared to `addChildView`

compositeViewのcollectionにmodelが追加された時に、addchildViewではなくて_onCollectionAddが呼ばれるようになりました。

つまり非公開なAPIとなりました。これ以上childViewについてのAPIを追加するより、Collectionを直接参照してイベントを購読した方がよいというのが理由です。

その他の同様なCollectionについてのイベントも同様です。


### collectionView’s and compositeView’s method `onChildRemove` is now known as `\_onCollectionRemove`

onChildRemoveも上記と同様の理由で_onCollectionRemoveになりました。


### Collection and Composite Views now respect the collection comparator when rendering and when new views are added.

要望が多かった機能の1つでCollectionViewとCompositeViewでは自動的にソートされるようになりました。

万が一この挙動が気に入らない場合はsortオプションにfalseを渡すことで従来の挙動にすることが出来ます。

```js
this.collectionView = new MyCollectionView({
  sort: false
});
```


### `collectionView` and `compositeView` now have an optional emptyViewOptions property which allows you to customize your `emptyView`.

childViewOptionsのようにemptyViewに対してもemptyViewOptionsを渡せるようになりました。


### `renderModel` for `compositeView` is now called `\_renderRoot`.

CompositeViewのrenderModelは_renderRootと変更されてprivateになりました。

renderModelという名前はcollectionのmodelの１つのことだと混乱させるので、CompositeViewをtreeViewと見立ててrenderRootという名前にしました。


### `Layout` is now called `LayoutView`.

Layoutという名前はわかりにくいので、LayoutViewという名前に変更しました。Layoutは色々な機能を持ったただのViewなのでLayoutViewとしました。


### Layouts now facilitate overriding the default RegionManager with a custom Class through the use of the `getRegionManager` method.

getRegionManagerを使うことでカスタムしたRegionManagerを使うことが出来るようになりました。


### LayoutViews now lets you specify the `regions` hash as an option upon instantiation.

LayoutViewをinstance化する際にもregionsのハッシュを指定出来るようになりました。

今まではinstance化する際にreginonsを指定出来なかったので、それをするには新しいClassを定義する必要がありましたが、下記の様にLayoutViewからregionsを指定して直接インスタンス化することが出来るようになりました。

```js
var myLayout = new LayoutView({
  model: myModel,
  template: myTemplate,
  regions: {
    myDiv: 'div.mine'
  }
});
```

