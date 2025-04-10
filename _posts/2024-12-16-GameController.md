---
title: GameScreen 与 GameController
author: lm
date: 2024-12-09
category: QuizGame
layout: post
---

GameController  
--------------
GameController 是整个游戏流程的控制者. 因此 GameController 肯定需要维护游戏状态  
因为本项目的游戏流程很简单, 就是一个问答选择题, 只需要一个 UI 界面 GameScreen, 并且只有简单的读取数据操作, 所以 GameController = GameManager + GameScreenPresenter/Controller  

### GameScreenPresenter/Controller  
GameScreen 初始化的业务逻辑很简单, 就是读取当前 Quiz 对应的所有的 Questions  
因此, GameScreen 需要订阅两个事件, 一个是关卡选择事件, GameScreen 需要在事件触发时, 更新内部变量 m_CurrentQuiz  
另一个是, 游戏开始事件, GameScreen 需要在游戏开始时, 根据 m_CurrentQuiz 初始化 GameScreen  

### ScoreManager
GameController 的另一个业务逻辑就是, 答题分数  

### Gameplay
GameController 需要控制整个游戏过程的 GamePlay  
1. 答题正确加分
2. 答题错误扣除一次生命值
3. 生命值归零游戏结束
4. 答完所有题目游戏结束
5. 游戏结束后, 显示本次答题分数
6. 关卡选择界面, 选择对应的关卡开始答题
6. 确认后返回关卡选择界面

GameScreen   
--------------
GameScreen 的界面交互比较复杂, 但是业务逻辑简单, 只是简单的读取.   

界面交互分解为四个部分:   
1. LifeBarDisplay 生命值条  
2. MessageDisplay 提交/继续以及反馈消息
3. ProgressDisplay 答题进度  
4. QuestionDisplay 题目展示  
5. ResponseDisplay 答题反馈  
6. ResponseHighlighter 答题反馈高亮  

业务逻辑简单, 因此不需要专门的 GameScreenPresenter/Controller 来处理与逻辑层的交互  

> ##### TIP
>
> MVP 结构中, 通常需要初始化的数据, 这些数据肯定不会在Model层中, 因此初始化的操作有两种处理方式, 一是专门定义一个初始化事件, 由Presenter订阅该事件, 读取初始化数据, 更新View, 二是 
> 在 Presenter 的构造函数中直接初始化, 这样管理者初始化 View 和 Presenter 实例并进行绑定时, 会执行初始化操作
{: .block-tip }

### LifeBarDisplay  

生命值, 生命值每个片段代表   

### MessageDisplay  
包含 提交 继续按钮和答案反馈信息  

### ProgressDisplay  
进度条, 表示目前答题的进度  

### QuestionDisplay
订阅 QuestionUpdated 事件, 当 QuestionUpdated 时, 更新 GameScreen 中与 Question 相关的信息  
Question 展示信息都包含在 question-scrollview中. 一个 Question 展示信息可能包含多个文本信息和一个图片信息  

### ResponseDisplay
用户点击选择答案, 由选择题的选项对应的答案按钮  

### ResponseHighlighter
高亮用户选择的答案按钮  

