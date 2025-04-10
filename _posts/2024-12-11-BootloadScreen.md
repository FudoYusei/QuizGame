---
title: BootLoadScreen
author: lm
date: 2024-12-11
category: QuizGame
layout: post
---

BootLoadScreen  
-----------------

BootLoadScreen 可以作为使用 UI Toolkit 的一个简单实例  

### Uxml - BootloadScreen.uxml
右键 Create -> UI Toolkit -> UI Document  
UI Document 就是我们可编辑的界面, 在界面中添加我们想要使用的 UI Element, 使用 uss 控制 UI Element 的显示效果  
然后使用 Scripts 对界面进行编程, 与后端进行交互. 还真是和 web 开发流程一模一样 html + css + scripts  

### USS - BootloadScreen.uss
双击生成的 BootloadScreen.uxml 即可进入 UI Builder  
点击左上角加号 - Add StyleSheets -> 生成 uss  

uss 文件的好处就是可以共用常见的样式  

### UI Elements 和 布局
所有 UI Elements 的基础属性就是布局, UI Elements 使用 Flex box Mode  

UI Elements
-----------

在 BootloadScreen.uxml 中添加我们需要的 UI Elments  

### menu__container  
因为我们制作的都是平面UI, 所以背景需要覆盖整个屏幕  
因此底层使用一个覆盖全屏的 Visual Elements, 设置全屏覆盖, 并设置背景色  

> ##### TIP
>
> UI Document 和 uxml 让我想起微软 Office 文件处理以及 web 开发,  DOM 配合 style sheet 的模式更加适合工程化
{: .block-tip }

### menu__start_button  
虽然名字是 button , 但只是拥有 button 的外形, 按钮最常见的外在表现就是, 悬浮变色变大  

通过 USS 的伪类实现  

Scripts 
-----------
由于只是一个启动画面, 只有一个简单的交互.  
根据 Screen 的宽高比(横屏还是竖屏), 显示不同的提示消息  

如何渲染到屏幕
-----------

### 加入场景
Unity Engine 按照场景来进行渲染, 所以 UI Document 想要被渲染必然需要添加到场景中.  
新建一个 GameObject, 并且附加 UI Document 组件, 选择 BootLoadScreen.uxml  

### 新建脚本
为了实现根据 Screen 比例来显示不同的 message , 需要新建一个 BootLoadScreen 的脚本. 并且把  

脚本的核心流程  
1. 建立 UIElements 对应的 DOM 对象, 通常是基于外部传进来的一个根节点, 通过调用 Query 找到想要的 DOM 元素. 例如, 在 Start 方法中使用 SetVisualElements()
2. 订阅 DOM 元素的各种事件, 例如 button 元素的 click 事件. 例如, 在 Start 方法中使用 RegisterElementEvents()
3. 业务逻辑变化, 更新 DOM 元素  

总结
-------------

BootLoadScreen 就是 UI Toolkit 开发 UI 的基本流程  
1. 创建好 UI 界面, 包括 UI Documents 和 USS
2. 创建 Scripts , Scripts 属于 UI Documents 和 Unity Engine 的结合点.
   1. 传进 RootElement
   2. 获取 DOM 元素
   3. 订阅 DOM 元素的事件  
   4. 反过来更新 DOM 元素