---
title: Quiz And Questions
author: lm
date: 2024-12-09
category: QuizGame
layout: post
---

ScriptableObject  
--------------
ScriptableObject 可以方便的在编辑器中编辑数据, 类似于美术在 Blender 等模型编辑器中编辑模型, Unity Editor 提供的ScriptableObject 就是数据资产   

SCriptableObject 使用流程如下:  
1. 创建一个 ScriptableObject 类型, 例如 TestSO
2. 在 Unity Editor 中创建 TestSO 类型的 ScriptableObject 资产 TestData, 可以看到 TestData 在项目文件夹下后缀就是 .asset
3. 现在可以在 Inspector 或者 Editor 中编辑 TestData 中的各项数据

### ScriptableObject 的特殊点一: 唯一性
ScriptableObject 作为 Asset 在 Unity Editor 中使用, 看起来和 MonoBehaviour 的表现形式差不多, 但是它拥有独特的特性: 唯一性  

使用 MonoBehaviour 存储数据  
1. 使用一个 MonoBehaviour 存储数据, 例如创建一个继承自 MonoBehaviour 的 DataScript 
2. 并且在 Editor 中需要使用这个数据的 GameObject 中添加该脚本作为组件 

由于Unity Editor 的规则是: 每个组件都会跟随 GameObject 实例化,DataScript 实际表现形式是: 每个使用 DataScript 的 GameObject 都会实例化这个 DataScript, 显然这些数据不仅重复的实例化, 而且相互之间是独立的  

1. 创建一个 ScriptableObject 类型存储数据, 例如 DataSO   
2. 在 Editor 中创建 DataSO 类型的资产, 例如 TestData
3. 在 GameObject 中无法直接添加 DataSO 组件, 需要先创建一个 MonoBehaviour 脚本
4. 脚本中添加 DataSO 类型成员, 然后在 Editor 中添加 TestData 到脚本的引用中
5. 重复上述方法, 在多个GameObject中引用 TestData
6. 无论修改其中一个 TestData 的值, 其他地方也会跟着改变
  
这个唯一性的来源是 Asset 在 Editor 中自身的属性, 我们实际上使用的都是同一个 Asset 的引用, 自然会具有唯一性  

静态类, 实际上也具有这个效果.  
1. 创建一个静态类 StaticData
2. 多个脚本中使用 StaticData  
3. 其中一个地方修改 StaticData 的值, 其他地方同样也会跟着改变
静态类同样具有唯一性  

### ScriptableObject 特殊点二: 可编辑  
静态类也可以承担唯一性数据的职责, 但是无法像 ScriptableObject 一样具有可编译性.  
但是静态类不具备可编辑性, ScriptableObject 可以在 Editor 中进行编辑, 并且序列化, 这些过程由 Editor 自动完成, 非常方便, 所以 ScriptableObject 最常见的用途是配置文件, 开发者可以实时的修改数据  
ScriptableObject 在 Editor Play Mode 中都可以被永久修改, 可以将 Unity Editor 看成是 ScriptableObject 的编辑器, 可以手动修改也可以使用脚本修改  

> ##### WARNING
>
> ScriptableObject 很像游戏中的存档文件, ScriptableObject 的存档性质只在 Editor Runtime 的时候有用, 所以它只会用于编辑时配置文件
> 在打包项目之后, ScriptableObject 并不会在运行时被更改, 个人感觉, ScriptableObject 被称为配置文件并不准确, 配置文件通常都是可以修改的
{: .block-warning }

Resources.Load 
---------------
ScriptableObject 除了直接在 MonoBehaviour 中引用 asset, 直接在 Inspector 将 SO Asset 拖入  

还可以在脚本中, 使用 var data = Resources.Load<TestSO>($"{path}")   
这里会出现一个问题: 使用 Resources.Load<TestSO>($"{path}") 得到的 data 具有唯一性吗?  
结论是, 无论多少次使用 Resources.Load<TestSO>($"{path}"), 只要加载的是同一个路径的 ScriptableObject, 那么都是对于同一个 ScriptableObject 的引用, 与在 Editor Inspector 中是一样的效果  

经常可以看见 GameObject go = Resources.Load<GameObejct>($"{path}") 和 Instantiate(go) 的组合. 从这里可以得出的结论是, Resources.Load() 将 Asset 加载到内存中(反序列化), 此时 gameobject 并未加入场景, 也就没有实质上的进入 Unity Engine 的生命周期.  
Instantiate() 方法将内存中的 GO 拷贝一份, 并且加入场景  

ScriptableObject 持久化存储
----------------
ScriptableObject 只能在 Editor 中持久化存储, 当然 ScriptableObject 也不需要在 Runtime 持久化存储, 因为 ScriptableObject 的特殊性都是作用于 Editor 的. Runtime 直接静态类就可以了.  
将 ScriptableObject 对象序列化为别的格式, 一是可以使用不同的方式编辑数据, 例如转换为 xml 格式, 在表格中编辑数据, 二是可以拷贝到不同的项目中.  

### Json 转换 ScriptableObject  
步骤:  
1. 读取 Json 文件
2. 反序列化成 SO  
3. 将 SO 序列化为 asset 文件并保存 

### ScriptableObject 转换 Json
步骤:  
1. 读取 ScriptableObject asset, 反序列化为 SO 实例
2. 将 SO 实例序列化为 Json
3. 保存Json 到项目中的 Json 文件

#### 问题一 路径错误
AssetDatabase.LoadAssetPath<QuestionSO>(path) 这个API 只认项目相对路径, 不能使用绝对路径  

FileUtil.GetProjectRelativePath(absolutePath) 这个 API 需要将路径中的反斜杠转换为正斜杠才能正常使用  

#### 问题二 写入文件报错
在 File.WriteAllText(jsonPath); 返回 shared violation  
File.Create() 会默认打开文件流, 而因为文件流没有关闭, 而再次打开发生的错误  

{% highlight csharp %}
                    if(!File.Exists(jsonPath))
                    {                      
                        // 记得关闭
                        File.Create(jsonPath).Close();
                        
                        Debug.Log($"Create json File: {jsonPath}");
                    }
                    else
                    {
                        File.Delete(jsonPath);
                    }

                    string jsonData = JsonUtility.ToJson(data);
                    File.WriteAllText(jsonPath, jsonData);
{% endhighlight %}

#### 反序列化 SCriptableObject
直接 Json 反序列化会报错  

{% highlight csharp %}
        // 创建一个 ScriptableObject
        QuestionSO questionSO = ScriptableObject.CreateInstance<QuestionSO>();

        // 无法直接反序列化到 ScriptableObject 对象
        // 因为 UnityObject 的对象实际上是有本地
        JsonUtility.FromJsonOverwrite(jsonData, questionSO);
{% endhighlight %}  

