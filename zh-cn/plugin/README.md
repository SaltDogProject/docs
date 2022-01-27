# 欢迎使用 SaltDog 插件

> SaltDog 插件扩展了 SaltDog 的各种功能，感谢您为 SaltDog 功能的创新贡献力量！

## 基础概念

SaltDog插件总体而言可以分为三部分：

* manifest.json 插件说明文件
* main.js 在背景运行的js文件，可以访问saltdog的全部方法
* html视图文件 用于在SaltDog的工作空间创造一个用户界面，除chrome规定的标准js接口外，只能访问on和send方法，向背景js发送或接收消息。

插件加载时，会读取manifest文件，运行插件的activate方法，并传入saltdog的实例。
