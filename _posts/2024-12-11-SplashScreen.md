---
title: SplashScreen
author: lm
date: 2024-12-09
category: QuizGame
layout: post
---

SplashScreen
-----------
游戏展示画面, 明明有加载进度画面, 为什么还需要 SplashScreen. SplashScreen 展示厂商和游戏信息, 实际上在背后偷偷的加载必要的资源, 加载完毕之后就会跳转到 StartScreen  
经常能看到游戏启动后先播放cg动画, 等一段时间后, 出现长按 space 跳过  

PreloadAsset 
-----------
进度条画面异步加载资源, 但是进度条画面资源是怎么加载的, 这就是一个鸡生蛋, 蛋生鸡的问题.   
实际就是需要 PreloadAsset, 相当于启动器, 在启动前必需要加载好的资源称为 PreloadAsset  

PreloadAsset 基本都是启动资源: 各个 Managers 和 UI资源   