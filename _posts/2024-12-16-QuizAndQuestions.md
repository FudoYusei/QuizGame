---
title: Quiz And Questions
author: lm
date: 2024-12-09
category: QuizGame
layout: post
---

Quiz  
--------------
Quiz 等同于一套试卷, 一套试卷包含多个 Questions, 本项目的 Questions 都是选择题包含多选题  

Quiz 的基本信息是:    

Question
-----------------
就和创建对象实例一样, 有分配内存的操作, 就一定需要回收内存的操作, 例如 CSharp 中的 new 和 GC, C++ 中的 new/delete  
  
在 Unity Engine 中符合事件订阅的两个生命周期函数是: OnEnable() 和 OnDisable() 方法  
在 OnEnable() 方法中订阅事件, 这些事件全是当前对象接收的外部事件, 将外部的依赖关系解耦  
在 OnDisable() 方法中注销这些事件, 不然会有不可预知的行为以及内存泄漏  

那么没有与 Unity Engine 生命周期直接绑定的类怎么办?  
在构造函数中订阅所有外部事件, 并且提供一个 Disable() 函数, 注销所有订阅事件  
在 Unity Engine 中使用的类, 不直接绑定 Engine 的生命周期, 也会由绑定了 Engine 生命周期的类进行管理, 只需要管理类在它的 OnDisable() 方法中调用即可  

实例
-----------  
LevelSelectionPresenter 类本身没有绑定 Unity Engine 的生命周期, 但是由 GameController 进行管理  

由 GameController 类控制它的生命周期, 在接收外部事件创建 LevelPresenter, 在 OnDisable 方法中调用 LevelSelectionPresenter 类中的 Disable() 方法.   

整体流程如下:  
UIManager 管理所有 Screen 实例, 初始化时, 触发 LevelSelectionEvents.Initialized 事件, levelSelectionScreen 实例作为参数  
GameController 在 OnEnable 中订阅了 LevelSelectionEvents.Initialized 事件, 接收参数, 并且 new LevelSelectionPresenter(levelSelectionScreen)   
LevelSelectionPresenter类的构造函数中订阅了外部事件, 并且提供 Disable() 方法注销外部事件  
GameController 在OnDisable()方法中, 调用 levelSelectionPresenter.Disable()方法   

这样 LevelSelectionPresenter 类实现了在 Unity Engine 生命周期中合理的订阅/注销事件  
