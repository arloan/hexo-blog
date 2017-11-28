title: NativeScript 的坑
date: 2016-01-20 20:01:52
categories:
  - 技术
  - 软件
tags:
  - NativeScript
---

[NativeScript](https://www.nativescript.org/) 这个东西应该说是跨平台原生移动开发方案中比较优的一个，不过目前仍然还不够成熟，在使用它开发小工具应用的时候遇到了几个问题。

一个是其CSS语法，不支持 `element-name.class-name` 这种形式，也就是说无法将特定CSS类别的使用范围限定到特定元素类型上，不过这倒也不是什么大问题。

另一个遇到的问题是数据绑定。
在界面描述XML文件中，将一个`ListView`元素的数据源绑定到一个`ObservableArray`对象，然后在`ListView.itemTemplate`节点展现数据时，很可能需要用到数据绑定的表达式。对于稍微复杂的展现而言，很可能需要在表达式中使用函数调用、过滤器函数，以及数据对象转换函数（Convertor）等。然而对于`ListView.itemTemplate`的情况，如果这些函数定义为`ListView`的`bindingContext`对象的方法的话，应用在运行期间渲染`ListView`时会报告找不到函数。

例如界面定义类似以下片段（`main-page.xml`）：
```XML
<ListView items="{{ Tokens }}">
  <ListView.itemTemplate>
    <GridLayout columns="100,*" height="35">
      <Label col="0" text="{{ Token }}" class="cell-title" />
      <Label col="1" text="{{ $value, FormatToken(), false }}" class="cell-detail" />
    </GridLayout>
  </ListView.itemTemplate>
</ListView>
```

数据源`ViewModel`定义（`main-view-model.js`）：
```javascript
var MainViewModel = (function(_super) {
  __extends(MainViewModel, _super);
  
  function MainViewModel() {
    _super.call(this);
    this.Tokens = new ObservableArrayModule.ObservableArray([
      { Token: 'SAMPLE', CreateTime: '2016/01/20 20:44:31', DeviceCount: 10 }
    ]);
  }
  MainViewModel.prototype.FormatToken = function(token) {
    return token.DeviceCount + ':' + token.CreateTime;
  }
  
  return MainViewModel;
})(observable.Observable);

exports.mainViewModel = new MainViewModel();
```

在业务逻辑中为`ListView`绑定数据源（`main-page.js`）：
```javascript
function pageLoaded(args) {
    var page = args.object;
    page.bindingContext = vmModule.mainViewModel;
}
```

运行时则会报告找不到函数`FormatToken`。

解决办法是把`FormatToken`函数放到 application level resource 当中去：
```javascript
var app = require('application');
app.resources.FormatToken = function(token) {
    return token.DeviceCount + ':' + token.CreateTime;
};

function pageLoaded(args) {
    var page = args.object;
    page.bindingContext = vmModule.mainViewModel;
}
```
