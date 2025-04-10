---
title: MVP架构
author: lm
date: 2024-12-26
category: USS
layout: post
mermaid : true
---

MVP 架构
-----------------
View 可以指代所有能够接收输入并反馈输出的概念. 在 Unity 项目中, UI 界面完美符合这个概念  
Model 是抽象出来的对象状态, 通俗来说, 就是数据  
Presenter 万能的第三者法则, 用于解耦 View 和 Model. 因为 View 和 Model 二者本身都非常复杂  

实例 SettingsScreen
-------------
本项目中, SettingsScreen 严格的按照 MVP 架构进行分层  

View 层: SettingsScreen UI Document 和 SettingsScreen 类  
Presenter 层: SettingsPresenter 类  
Model 层: SettingsSO 类  
贯穿层级的事件机制: SettingsEvents  

> ##### TIP
>
> 很多时候, 页面的显示逻辑并不复杂, 直接将 Screen 类和 Presenter 类合并, 类似于后台代码, 减少 View 与 Presenter 之间交互的复杂度.  
{: .block-tip }  


SettingsEvents  
----------------  
SettingsEvents 中大部分是 SettingsPresenter 类中使用的事件, 因为 SettingsPresenter 需要与 View 和 Model 层都进行消息互传  

无论哪一层, 只要与外界交互, 必然有输入输出, 本项目使用事件机制, 那么必然会对外输出事件, 以及订阅外部事件  
将 View 层和 Model 层视为两个独立的模块. 两个模块都提供输入和输出事件, Presenter 层作为中间层, 需要订阅模块的输出事件, 触发模块的输入事件.  

#### View To Model  
View 层的输出事件:  
View 层除了页面逻辑外, 对 Presenter 层的交互就是 SettingsEvents.SliderValueChanged  

View 层不需要关注调用什么业务逻辑, 只需要通知 Presenter 层, 自己的 UI 元素发生了改变, 这也是 Presenter 层存在的意义, 解耦页面逻辑.  

View 层的输入事件, 输入事件可以理解为 View 层向外界提供的调用接口, 例如:  
SettingsEvents.UpdateXXXVolumeSlider, Presenter 触发该事件就可以让 View 层执行 UI 处理逻辑.  

#### Model To Presenter  
因为 View 实际上就是显示数据, 因此数据发生改变也需要向上通知, 而数据只需要通知 Presenter 层, 数据发生了改变, 和 View 层相同, 解耦业务逻辑  

Model层的输出事件:  
AudioSettingsSO 对应的 SettingsEvents.ModelXXXVolumeUpdated 事件  

Model层的输入事件:  
SettingsEvents.UpdateXXXVolume, Presenter 触发该事件, 通知 Model 层调用更新逻辑   

#### Presenter
当定义好 View 层和 Model 层的输入事件, Presenter 层自然也就出来了, 订阅 View 层和 Model 层的输出事件, 触发 View 层和 Model 层的输入事件.  
Presenter 传递给 Model 的信息, 就是常说的业务逻辑操作, 业务逻辑操作再被分解为对于业务数据的增删改查操作.  
本项目的业务逻辑简单, 不需要再对业务层进行分类.   

Presneter 的输出事件:  
业务逻辑也是围绕着增删改查来的, SettingPresenter 中只有 Update 的逻辑:  
1. Presenter To View, 更新具体的 UI 元素
   1. SettingEvents.UpdateXXXVolumeSlider
2. Presenter To Model, 更新具体的业务模型
   1. SettingEvents.UpdateXXXVolume  
指向的对象不同, 名称需要进行区分. 在 Presenter 的视角中, 传递从 View 层到 Model 层的事件实际上都是同一个逻辑.   
例如, View 层的 MasterVolumeSliderChanged 传递到 Presenter 层中, 需要触发 MasterVolume 这个对象相关的逻辑事件, 需要通知 Model 层更新数据  
而 Preenter 同样会从 Model 层得到通知, 也需要通知 View 层更新数据, 现在的问题是出现了两个更新 MasterVolume 的事件  

<font color=#4cd137>这里会引申出另一个概念: ViewModel. 相对于业务逻辑层的 Model. View 层的逻辑围绕着 ViewModel 展开.</font>  
ViewModel 可以是多个 Model 组合, 也可以是 Model 的某一个部分, 很常见的就是, 一个页面中有多个逻辑业务中的数据组合  
所以, 实际上 Presenter to View 的事件实际上是: SettingEvents.UpdateViewModelXXXVolume  

Presenter 层的一个职责就是负责 ViewModel 到 Model 之间的转换: 从 Model 中获取 Logic Model, 然后将 Logic Model 转换成 View 层使用的 ViewModel  
<font color=#4cd137>这种转换还经常发生在 Data Model 到 Logic Model 中</font>  
