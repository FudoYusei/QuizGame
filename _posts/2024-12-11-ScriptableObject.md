---
title: Prefab 和 ScriptableObject
author: lm
date: 2024-12-12
category: QuizGame
layout: post
---

Prefab
-----------
预制体, 相当于一个模板类, 在 Scene 中代表的是一个从模板创建出的实例, 但是可以叠加自身的修改信息, 从模板类中创建出来的实例之间并无关联, 每个实例都可以有自身的修改信息.  

在 Inspector 中, MonoBehaviour 引用 Prefab时, 这些 Prefab 不像 asset 会直接实例化, 还需要在脚本中, Instantiate(prefab)  

ScriptableObject 
-----------
[ScriptableObject 进阶](https://blog.csdn.net/qq_46044366/article/details/124310241)  

ScriptableObject 需要理解为在 Unity Editor 中创建的 Asset   
首先需要创建一个 ScriptableObject 的类, 然后在 Editor 中创建这个类的资源  
想要使用这个资源, 
1. 像引用 其他 Asset 例如 textures material 这样, 在 MonoBehaviour 中引用  
2. 使用代码 Resources.Load() 加载预先创建好并放置在 Resources 文件夹中的资源文件  
3. ScriptableObject.CreateInstance<T>() 在内存中创建实例  



在Inspector中不同的表现
--------------
在 Inspector 的 MonoBehaviour 中引用 Prefab 和 ScriptableObject, 两者会有不同的表现.   

Prefab 依然需要脚本调用 Instantiate(prefab) 来实例化一个 Prefab 的副本, 而 ScriptableObject 和其他资源文件一样, 直接就可以当作已经实例化的单例来使用  
观察两者在 Asset 文件夹中的文件可以看到: Prefab 保存的后缀是 .prefab, 而 ScriptableObject 保存的文件后缀是 .asset (注意, 这里是从 ScriptableObject 中创建出来的资源文件, 并不是脚本本身)  
显然, Unity 对于两者在脚本的实例化做了不同的处理, 同时对于  
