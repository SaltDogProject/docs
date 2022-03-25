# PDFVIEW 相关

## 获取与创建
### saltdog.getCurrentPDFView(args,callback)
获取当前活动的PDFView。
#### 参数说明
-   args: 请留空填写{}
-   callback: 回调函数
#### 直接返回
无直接返回值
#### 回调返回
- `null` 当当前没有正在浏览的页面或页面不为PDF
- `PDFView` 当前正在展示的PDF代理，具体使用方法详见下文
### saltdog.createPDFView(args,callback)
用磁盘上的文件创建PDFView。
#### 参数说明
| 参数名 | 说明 | 类型 | 可选值 | 默认值 |
|----|----|----|----|----|
|`args.title`|Tab选项卡标题|String|-|-|
|`args.pdfPath`|PDF文件在本地磁盘的位置|String|-|-|
|`callback`|创建后的回调函数|Function|-|-|
#### 直接返回
无直接返回值
#### 回调返回
-   `PDFView` 创建成功后的PDF对象，具体使用方法见下文

## DOM事件监听
SaltDog允许对基本的原始DOM事件进行监听，但禁止对事件进行响应和DOM操作。

因为SaltDog的每个PDF Tab都是独立的Webview，不和插件主机运行在同一个上下文，因此事件参数会被序列化传输，部分引用将不可用。

下文中的PDFView均代表由上述API`saltdog.genCurrentPDFView`以及`saltdog.createPDFView`所返回的`PDFView`对象。

### PDFView.content.addEventListener(selector,event,callback,useCapture)
监听来自PDFView的原生DOM事件。除第一个参数外，其余与原生`addEventListener`相同
#### 参数说明
| 参数名 | 说明 | 类型 | 可选值 | 默认值 |
|----|----|----|----|----|
|`selector`|监听目标，目标用`querySelector`时传入的选择器参数|String|同`CSS Selector`|"document"|
|`event`|DOM事件名称|String|同JS`addEventListener`的事件|-|
|`callback`|事件触发后的回调函数|Function|-|-|
|`useCapture`|指定事件是否在捕获或冒泡阶段执行|Boolean|true:事件在捕获阶段执行/false：事件在冒泡阶段执行|false|
#### 直接返回
- `CustomEventID`，String, 成功创建后返回的ID，可用于`removeEventListener`取消监听。
- `null` 当监听出错。具体信息请在控制台查看。
#### 回调返回
原生DOM回调事件参数经过序列化后的结果

### PDFView.content.removeEventListener(CustomEventID)
取消`PDFView.content.addEventListener`注册的监听函数
#### 参数说明
| 参数名 | 说明 | 类型 | 可选值 | 默认值 |
|----|----|----|----|----|
|`CustomEventID`|`PDFView.content.addEventListener`所返回的事件ID|String|-|-|
#### 直接返回
-   `true`当取消成功
-   `false`当取消失败，具体原因请查看控制台
