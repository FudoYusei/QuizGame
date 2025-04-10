---
layout: home
title: QuizGame
permalink: /
---

Make Jelly site have a GitBook look!

Demo
------------------

演示视频: [链接地址]()

项目地址: [https://jekyll-themes.com/jekyll-gitbook/](https://jekyll-themes.com/jekyll-gitbook/)

项目背景介绍
-----------------

使用 Unity UI Toolkit 创建基于UI的问答游戏, 本身就是官方的 UI Toolkit 教学项目.  
UI Toolkit 是Unity官方最新推出的UI工具链, 拥有原生的可视化编辑器支持. 并且开发流程类似于web, 元素和样式分离. 比起uGUI耦合性更低, 灵活性更强, 适用于复杂的UI逻辑.  

> #### Tip
> 
> 因为该项目是教学实例, 因此内部充斥着不同风格的代码架构, 后面会逐一进行解释  
{: .block-tip}  

How to Get Started
--------------------  
本项目使用 Unity 2023.2.12f1c1, 直接在 Asset Store 中搜索 UIToolkit, 导入项目即可  

#### ReadMe

每次在 Unity Editor 中打开项目会自动加载预设 window layout, 并且在右侧边栏显示 ReadMe 中的信息.  
阅读 ReadMe 中关于项目的介绍, 可以获得对项目的初步理解  

#### 从场景运行

首次在 Unity Editor 中首次打开项目会自动定位到 Boot 场景.  
项目默认点击 Play 按钮, 都会从 Boot 场景开始运行, 模拟实际运行环境.  

想要在 Editor 中自由的选择运行场景, 点击上方菜单栏 Quiz-> DontLoadBootOnPlay  

Key Features
--------------------  
#### 基于事件系统的架构
静态事件类, 例如 GameEvents, UIEvents. 促进系统间的交流, 易于测试和维护  

#### Design Patterns
几个核心设计模式: 包括 state pattern 和 model-view-presenter  

#### UI Toolkit
作为一个 UI 驱动的游戏, 示例中, UI Toolkit 用于所有界面, 演示实际用法. 

Demos
--------------------
独立场景展示 UI Toolkit 的特性.  

+ UXML and Visual Tree: UI Builder可视化界面中编辑的 Visual Tree, 会被转换成对应层级结构的 UXML 文件
+ Flexbox: Flexibal Box Layout Model 提供了高效的布局模型
+ UnityStyleSheets(USS): 类似web开发的css文件, 自定义样式可以重复使用, 并且可以简单的替换.
+ UQuery: 简化了在复杂层级结构中定位特定 UI Elment 的方式, 可以在 visual tree 中无缝导航特定的 UI 组件
+ Pseudo-classes: 伪类, 可以用最少的代码创建交互和动画.
+ UI Toolkit Event System: UI Toolkit 有着自身完整的事件系统
+ Manipulators: 将相关的事件抽象出来到一个单独的类中.(例如, 道具系统中的点击拖拽行为, 由mousedown mousemove mouseup三个事件组成, 并且需要维护行为产生的状态)
+ Custom Controls: 自定义 UI 控件  

QuizU Game
--------------------

命名约定
------------------
类名称后缀的命名规范, 细则如下:  

#### Manager  
这个类处理游戏级别或者全局问题. 管理对象或者服务的生命周期, 并不是特定的 UI Screen. (例如, AudioManager, SequenceManager)  

#### Controller
通常作为一个系统的驱动中心, 控制系统流程运转. 例如 GameController   

#### Presenter
这个类是 MVP 设计模式的中间层控制器, 这个隔离 data 接口(e.g LevelSelectionPresenter 调用 ScriptableObject data(Model层) 更新 levelSelectionScreen(View层))  

#### Screen
Screen 类作为一个独立 UI 的显示代码 --作为模态屏幕, 继承自 UIScreen 基类.   
因为, UI 采用栈式结构来实现菜单基础的前进后退功能, 所以每次只有一个 UIScreen 类处于激活状态.  

每次开启一个新的 UIScreen 时, 会将当前 UI 对象入栈, 触发 ScreenClosed 事件时, 会出栈栈顶 UIScreen 对象并显示.  

#### Display
有些 UIScreen 的页面逻辑比较复杂, 因此将 UIScreen 拆分成小块的 Display 逻辑.  

#### ScriptableObjects
ScriptableObject 类型的 C# 脚本使用 SO 后缀.  
ScriptableObject 类型的资源使用 _Data 后缀.  

ScriptTemplates
------------------------  
ScriptTemplates 文件夹下放置的时 MonoBehaviour 模板文件, 需要替换 Unity 内置的模板文件  

StyleGuide
----------------------  
Unity 官方推荐的 C# 编程规范  

UI Toolkit Documention
-----------------------  
UI Toolkit 是 resources features tools 的集合. Unity 开发者可以使用它来创建自定义的 UI 和 Unity Editor 扩展.  
它能够用来开发运行时调试工具以及游戏和应用程序的 UI. UI Toolkit 的灵感来自于 web 技术, 因此 web 开发者会发现核心概念很熟悉.  


[1]: https://pages.github.com
