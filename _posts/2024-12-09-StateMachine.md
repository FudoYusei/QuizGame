---
title: State Machine
author: lm
date: 2024-12-09
category: QuizGame
layout: post
---

SequenceManager
-----------------

SequenceManager 通过状态机控制应用程序的整体流程, 与 Unity 引擎的生命周期绑定, 以此来进行资源和场景的初始化  

![SequenceManager简单流程](/assets/gitbook/images/2024-12-09-StateMachine/状态机流程.png)  

SequenceManager 与 PreloadedAssets
----------------

SequenceManager 作为启动入口, 同时需要加载  

SequenceManager 与 StateMachine
----------------



Unity 中状态机实现
----------------

Unity 的协程完美的符合状态机的机制, 实际上 Unity 协程本身就是一个状态机, 也说明了协程正好适用于这个场景  

### State
State 作为一个场景的抽象, 等于场景的状态, 以及通用的场景行为: Enter Execute Exit ValidateLink  


### Link
State 通过 Link 流转到下一个 State, State 内部持有 Link 列表, 在 State.Execute 执行完毕之后, 对 Link 进行验证, 只要有一个验证通过, 就会退出当前状态并转移到该 Link 对应的状态  

### StateMachine 与协程 
使用协程将 StateMachine 与 Unity 的生命周期绑定, 每一帧让 State 的 Execute 方法 和 StateMachine 的 Loop 迭代前进一次.  

{% highlight csharp %}
public abstract class State : IState{
    public abstract IEnumerator Execute();
}

public class StateMachine{
    public class IState CurrentState;

    public void Run(IState state)
    {
        SetCurrentState(state);
        Run();
    }

    public void Run()
    {
        Coroutines.StartCoroutine(Loop());
    }

    public IEnumerator Loop()
    {
        // 判断CurrentState是否执行完毕
        if(CurrentState.Execute is done)
        {
            // 验证 State 的 links 中是否有满足条件的
            if(CurrentState.ValidateLinks(out var nextState))
            {
                CurrentState.DisableLinks();
                SetCurrentState(nextState);
                CurrentState.EnableLinks();
            }
        }
    }

    public void SetCurrentState(IState state)
    {
        // 如果CurrentState != null
        if(CurrentState 生命周期没有结束)
        {
            Skip();
        }

        // 设置新的CurrentState
        CurrentState = state;

        // StateMachine 继续播放
        Coroutines.StartCoroutine(Play());
    }
}
{% endhighlight %}

