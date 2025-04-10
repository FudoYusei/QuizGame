---
title: LevelSelectionPresenter
author: lm
date: 2024-12-09
category: QuizGame
layout: post
---

LevelSelection  
-----------------

关卡选择界面功能  
1. 加载预定义关卡信息
2. 点击关卡按钮切换显示对应的关卡
3. 点击Play按钮进入对应的关卡

LevelSelection 不是简单的 UI 界面功能, 涉及到从 Model 层获取信息  

更详细一点, 分解出页面功能和业务逻辑  

页面功能:  
1. 根据关卡数量生成相同数量的关卡按钮
2. 加载当前选中(激活)的关卡, 通知 Presenter 层加载当前选中的关卡信息
3. 显示从 Presenter 层返回的关卡信息
4. 高亮当前选中(激活)的关卡

Presenter 逻辑:  
1. 加载所有关卡信息, 向 LevelSelectionScreen(View层) 触发
2. 接收 LevelSelectionScreen 的选中关卡消息, 加载对应的关卡信息
3. 加载关卡信息完毕后, 向 LevelSelectionScreen 发送加载完毕消息

业务逻辑:  
只有读取关卡信息  



Unity 中实现 MVP 结构 二
----------------
要想在 Unity Engine 的 UI 层实现 MVP 结构, 很自然的需要 UI 的执行逻辑符合 Unity Engine 的生命周期. 简单来说一定是有一个继承了 MonoBehaviour 的 Setup 类, 来建立 Screen 实例和 Presenter 实例之间的依赖关系  

以 DemoSelectionSetup 类举例, 它的主要职责就是, 在 Unity Engine 某个生命周期(通常是 OnEnable)中实例化并初始化 Screen 实例和 Presenter 实例, 并且建立它们两的依赖关系  
{% highlight csharp %}
public class DemoSelectionSetup : MonoBehaviour
{
    private void OnEnable()
    {
        // 实例化并初始化 Screen 对象和 Presenter 对象
        // 因为这两个对象已经在 Unity Engine 的场景中实例化了, 所以通过 Editor 获取场景中已经实例化的对象
        if(m_Document == null)
        {
            m_Document = GetComponent<UIDocument>();
        }

        if(m_DemoSelectionPresenter == null)
        {
            m_DemoSelectionPresenter = GetComponent<m_DemoSelectionPresenter>();
        }

        // 实例化并初始化 Screen
        SetupDemoSelectionScreen();
    }
}
{% endhighlight %}  

这里的初始化借助了 Scene  

Unity 中实现 MVP 结构 一
----------------
关卡选择界面功能很简单, 读取所有关卡信息并展示在界面上, 玩家点击选择相应的关卡进入关卡    

LevelSelectionScreen类, 使用消息系统解耦了与Presenter类的依赖, 订阅了 QuizDataLoaded 事件, 也就是加载关卡信息完毕后, 显示关卡信息在界面上  
LevelSelectionPresenter 类, 需要从 Model 层读取所有关卡信息传递给 View 层, 并且订阅 View 层的点击事件, 将选中的关卡传递给 Model 层  
因为这里交互逻辑很简单, 所以并没有抽象出对应的 IView 和 IPresenter 接口  

GameController 继承 MonoBehaviour, GameController 会在 Unity Engine 对应的生命周期方法内实例化并建立 LevelSelectionScreen 和 LevelSelectionPresenter 之间依赖关系  