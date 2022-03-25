# WEBVIEW相关

## 获取与创建
在SaltDog中，考虑到安全原因，只允许插件操作自己创造的Webview，因此没有提供相应的API来获取当前显示的Webview对象。但是插件仍然可以获取Saltdog当前存在的Tab的基本信息（但无法操作）。
### saltdog.createWebview(args,callback)
创建属于插件的Webview，创建后仅有插件自身可操作。
#### 参数说明
| 参数名 | 说明 | 类型 | 可选值 | 默认值 |
|----|----|----|----|----|
|args.title|在Tab顶部所显示的名称|String|-|-|
|args.url|Webview需要连接到的URL的地址|-|-|
#### 直接返回
无直接返回值
#### 回调返回
-   `null` 当创建失败
-   `Webview` Webview对象，具体使用方法见下

## Webview方法
SaltDog给予了插件自主创建的Webview极高的自由权，插件主机可以调用几乎所有的[Electron Webview API](https://www.electronjs.org/zh/docs/latest/api/webview-tag)和所有的[Electron Webview事件](https://www.electronjs.org/zh/docs/latest/api/webview-tag).唯一不同的是，为了统一API接口格式，面对多参数的Electron API，需要将规定的API顺序组成一个list，放入第一个参数。如下：
```
saltdog.createWebview({
    title:"test",
    "url":"http://example.com"
},(webview)=>{
    // 例如，Electron原生的滑动窗口位置API
    let result = <webview>.goToOffset(offset);
    console.log('This is result',result);
    // 在SaltDog插件主机调用方法是
    webview.goToOffset([offset],function(result)=>{
        console.log('This is result',result);
    })
})
```
对于大部分没有参数的API，可以直接调用
```
saltdog.createWebview({
    title:"test",
    "url":"http://example.com"
},(webview)=>{
    // 返回
    webview.goBack();
})
```
对于Electron Webview的事件，也可以使用`webview.on(event,callback)`来监听。
