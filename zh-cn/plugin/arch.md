# 插件结构

## 概述
SaltDog插件一定程度上模仿了VSCode和Chrome的插件机制，但更加简单。从本质上来说，一个SaltDog插件是由两部分组成。

- （必须）一个运行在受限Node.js环境中的插件宿主（插件主体逻辑）
- （可选）若干个独立的WebView（插件GUI交互）

每个插件宿主将独立运行在自己的受限进程中，接收来自插件GUI的消息请求或监听来自SaltDog的事件，并运行开发者自定义的插件逻辑。我们进行这样的设计，一方面是可以减少插件逻辑对主渲染进程的阻塞，另一方面，受限的Node.js环境可以一定程度上保护用户的隐私。

但同样的，这种做法引入了一些问题：大部分SaltDog提供的插件API都将会是异步的，需要通过回调进行调用，您也会在后面的文档中反复看到。

## 插件结构
最简单的插件结构仅需要包含两个文件：

- manifest.json 插件声明
- main.js 插件逻辑主入口文件

其中，manifest.json不可重命名，主入口文件名可以在manifest.json的main字段中更改。

当SaltDog启动时，主进程会读取所有已安装的插件，并在插件根目录下寻找manifest.json进行插件的初始化，没有该文件或该文件配置出错的插件将不会被加载。

### Manifest
Manifest文件是整个插件组织逻辑的核心，如果你有Chrome/VSCode插件开发的经验，那你将会很容易理解这种设计结构。

Manifest记录了插件名、作者、版本信息、入口文件、视图声明、插件指令头、插件响应函数名等多种信息，具体的写法详见[Manifest声明](zh-cn/plugin/manifest.md)

### 主入口文件
主入口文件暴露了两个方法：activate和deactivate。其中，activate方法会在SaltDog插件被激活时（目前是开启程序默认激活所有插件）调用，deactivate会在插件被销毁（用户手动销毁或程序关闭）时调用。函数默认携带的参数是SaltDog API对象，以供插件进行调用。综上，您的主入口函数基本框架应该类似于以下：

```javascript
function activate(saltdog){
    // activate logic
    // You can use saltdog.xxxx to call SaltDog Api
}
function deactivate(saltdog){
    // deactivate logic
}
module.exports = {
    activate,
    deactivate
}
```
## 结语
很好！现在您已经对SaltDog基本的插件结构有了一些了解，欢迎阅读接下来的章节来更加具体的了解SaltDog插件的强大功能以及编写方法！