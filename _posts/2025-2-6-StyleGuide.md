---
title: StyleGuide
author: LM
date: 2025-02-06
category: unitytechnology
layout: post
---

Clean Code  
----------------  

干净的代码最终目的是让开发具有更多的扩展性和形成一系列的产品标准:  
+ 命名规范一致性
+ 格式化代码具有易读性
+ 重组类和方法保证它们小而可读
+ 注释任何不能自我解释的代码

无论是面向手机的小游戏还是大型 MMORPG, 干净的代码都可以减少软件维护的消耗. 也可以更容易的扩展现存的软件.  

作为一个 team 来开发
-----------------

> ##### TIP 
> 傻瓜都能写出计算机能够理解的代码.
> 好的程序员写出人类能够理解的代码.
  

没有开发者是一个孤岛, 干净的代码不仅是对程序结构有帮助, 也是为团队中不同的成员提供一份指导标准.  

#### KISS
Keep it simple, stupid  

工程师或者程序员理想中的自己是无所不能的, 因此尽管计算和编程已经足够难了, 他们还想要将事情复杂化.  

没有必要重新造轮子, 如果有一个已经验证过的简单可行的能够解决问题的技术方案.  
为什么要为了使用而去使用一项新技术呢? Unity 已经包含了很多解决方案. 例如, 如果现在的 Hexaonal Tilemap 对你的策略游戏奏效, 就不要自己写一个. 最好的代码就是完全不屑代码.  

> ##### TIP 
> 人的精力是有限的, 而探索是无限的, 探索的越多, 未知的区域反而更多
> 如果有现成的方案, 先拿来使用, 在使用的过程中发现不足或者新的需求, 再去从局部了解并且优化方案
> 这也是了解一个方案或者开源项目最好的方法, 从使用中学习, 再将学习反馈到使用
> 而不是漫无目的的学习, 效率非常低.
  
#### 切中问题要害
第一步, 软件开发的第一步需要理解, 要解决什么问题. 很多程序员会在不理解实际问题发生原因的情况下, 不断修改代码直到能够工作.  
在修改代码之前, 先询问一下为什么这个问题会发生.  

#### 每天进步一点
没有人会想要写出不好的代码, 但是随着时间推进, 代码总会朝着不好的方向演变.  
代码库需要经常性的维护和保养. 计划并确保这些工作.  

#### 做好比完美重要
当代码能够达到生产标准, 就是时候提交并且继续了.  

代码的最终目的是完成一些功能. 在实现代码和完成功能之间取得平衡即可. 如果为了展望未来功能而去重构, 那就是过度重构.  

#### 保持一致需要每个人的努力
Keep Simple and stupid, 简单并不意味着容易, 团队合作需要每个人对自己负责的代码进行维护  

> ##### TIP 
> 这份指南的重点是在 Unity 开发中最常见的编码约定, 是微软框架设计指南的一个子集.
> 这些指导知识建议, 并不是硬性规定. 根据团队的偏好定制它们. 确保每个人都适应并且执行.
>
> 一致性就是王道. 如果遵循这些建议, 并且在将来需要修改代码风格时, 只需要查找和替换就可以快速潜移代码库.     
> 因此, 当团队的代码风格与本指南或者微软框架设计指南相互冲突, 优先团队的代码风格, 只要保证一致性即可.
  

#### 命名约束

##### 类
下面是其中一个标准示例:  
1. private 修饰符不要省略
2. Public 成员变量使用 pascal case
3. Private 成员变量使用 camel case 并且使用下划线作前缀
4. Local 变量以及参数使用 camel case 并且没有下划线
5. Public 和 Private 变量聚集在一起.

{% highlight csharp %}
public class Player
{
    // group public variables
    // 
    public float DamageMultiplier = 1.5f;
    public float MaxHealth;

    private bool _isDead;
    private float _currentHealth;

    public void InflictDamage(float damage, bool isSpecialDamage)
    {
        // local variable
        int totalDamage = damage;

        if(isSpecialDamage)
        {
            totalDamage *= DamageMultiplier;
        }

        if(totalDamage > _currentHealth)
        {

        }
    }
}
{% endhighlight %}  

##### 变量

var 关键字虽然能够让我们偷懒的不明确函数返回值类型, 但这不是一个好的习惯.  
var 关键字应该在我们明确知道变量类型, 而只是为了省略重复才使用  

{% highlight csharp %}
// good use of var
var powerUps = new List<PowerUp>();
var dictionary = new Dictionary<string, List<GameObject>>();

// AVOID: potential ambiguity
var powerUps = PowerUpManager.GetPowerUps();
{% endhighlight %}  

##### Enum 
Enums 和类型命名规范一样, Enums 使用单数类型的名称. 但是 Flags 特性修饰的 Enums 类型名称使用复数, 因为它可以代表多个类型  

{% highlight csharp %}
public enum WeaponType
{
    Knife,
    Gun,
    RocketLauncher,
    BFG
}

[Flags]
public enum AttackModes
{
    None = 0,
    Melee = 1,
    Special = 2,

    MeleeAndSpecial = Melee | Special
}
{% endhighlight %}  


##### Methods
函数和方法在 Unity 开发中是可以互相替代的.  

1. 方法由动词开头
2. 如果方法返回 bool 类型, 需要使用疑问句: IsSuccess 或者 HasStartedTurn  

##### Events and EventHandlers

1. Event 的命名
Event 表示某个事件发生, 自然有三个状态, 事件发生前, 事件发生, 事件发生后.  
例如, OpenDoor => OpenningDoor, OpenDoor, DoorOpened  

2. 使用系统定义的 System.Action  

{% highlight csharp %}
public event Action OpeningDoor;
public event Action DoorOpened;
{% endhighlight %}  

> ##### TIP 
> event 关键字实际上是限制了委托只能作为事件使用.
> 无法被赋值, 无法当作函数直接使用, 必需 Invoke()
> 直接将委托当作事件也可以
  
3. 当 Subject 触发事件的方法使用前缀 On. 例如 OnOpeningDoor 或者 OnDoorOpened  
{% highlight csharp %}
public void OnDoorOpened()
{
    DoorOpened?.Invoke();
}
{% endhighlight %}  

4. EventHandling Methods 使用订阅事件名称和下划线作为前缀  
GameEvents_OpeningDoor 或者 GameEvents_DoorOpened  

<span style='color:#4cd137'>注意 EventHandler Delegate 代指委托类型, 用于声明 event, EventHandling Methods 是具体的方法名称</span>  

{% highlight csharp %}
public class Subject
{
    private delegate void ClickHandler;
    public event ClickHandler SubmitButtonClicked;

    // ClickHandler 等同于 Action
    public event Action SubmitButtonClicked;

    public void OnSubmitButtonClicked()
    {
        SubmitButtonClicked?.Invoke();
    }
}

public class Observer
{
    public Observer(ISubject subject)
    {
        subject.SubmitButtonClicked += SubjectEvents_ButtonClicked;
    }

    void SubjectEvents_ButtonClicked()
    {
        // do something
    }
}
{% endhighlight %}  

5. 外部声明 Events, 将逻辑相关的事件都集中到一个类中, 使用内置的委托类型 Action 代替 EventHandler Delegate  

{% highlight csharp %}
public static class GameEvents
{
    public static Action StartingGame;
    public static Action GameStarted;
}

public class GameController{
    private void OnStartingGame()
    {
        GameEvents.StartingGame?.Invoke();
    }

    private void OnGameStarted()
    {
        GameEvents.GameStarted?.Invoke();
    }
}

public class GameScreenPresenter{
    public void OnEnbale()
    {
        GameEvents.GameStarted += GameEvents_GameStarted;
    }

    public void GameEvents_GameStarted()
    {
        // do something
    }
}
{% endhighlight %}  

##### Namespaces

Namespaces 用于确保自己定义的类, 方法, 接口, Enums 等等不会与系统内置或者第三方库中的命名冲突.  


Formatting  
----------------  

如果想要代码写的容易, 先要让代码读起来容易  

代码格式化帮助减少功能的不确定性以及提高代码清晰度. 通过遵守一个标准化的风格指南, 代码审查可以减少代码风格的检查而更多专注于代码功能.  

根据团队风格来指定个性化的风格指南, 当建立个性化 Unity dev style guide 时, 根据团队需求, 可以省略 扩展或者修改示例规则  

#### Properties
属性提供了 read write 或者对类成员进行计算的扩展功能.  
属性虽然表现的像 public 成员变量, 但本质上是被称为访问器的方法.  
每个属性都有 get set 方法和一个能够访问到的数据成员 private field  

1. 对于只读的属性, 使用 => 运算符
2. 对于其他的属性, 使用 get set, 让编译器自动生成属性
3. 使用 private 可以控制 get set 的权限.  

{% highlight csharp %}
public class MyClass : MonoBehaviour
{
    // 类内部可以修改
    private int _hp;
    // 外部只能访问
    public int HP => _hp;

    // 编辑器中可以编辑
    [SeriailizeField]
    private int _level;
    // 外部只能访问, 无法修改
    public int Level => _level;

    // 编辑器中可以编辑
    // 外部可以访问和修改
    // public memeber variables
    public int MaxHealth;

    // 编辑器无法修改, 外部可以访问
    // 因为本质上是一个内部 private member field
    // public property
    public int MaxProperty{get;set;}
}
{% endhighlight %}  

这两者是不同的, 在 Unity 中, 可以看出明显的区别.  
Unity Editor 可以在 Inspector 中可视化 public member variables  
但是对于 public property 毫无反应, 因为 MaxProperty 本质上对于 _maxProperty_field 的访问访问, 编译器会根据情况自动生成属性对应的 private member field  

#### Serialization

Unity 提供了强大序列化功能, 配合编辑器可视化可以方便的修改成员数据.  

1. 使用 SerializeField 特性修饰 field, 而不要使用 public member variables:  属性是为了更好的控制内部成员的访问权限
2. Range 特性修饰最大最小值
3. 将序列化的数据成员封装到 class 或者 struct 中, 保持 Inspector 的整洁

#### 大括号和缩进样式
大括号的两种格式  
1. 新的一行
2. 紧跟着开始的地方
无论使用什么样式, 保证团队一致性. 或者考虑代码格式化工具?  

缩进通常使用 tab, 但是到底是制表符还是空格?  
VisualStudio 可以在设置中将 tab 从制表符更改为空格.  
因为不同系统对制表符的支持程度, 还是选用多个空格代替制表符比较好.  

3. 不要省略大括号, 特别是流程控制代码
4. 更不要移除多层嵌套流程代码中的大括号

如果团队使用的是 VisualStudio, VisualSudio 提供了 EditorConfig, 使用同样的 EditorConfig 可以保证编辑器的代码一致性.  

#### 水平的空格
1. 添加空格减少代码密度: 额外的空格可以提供同一行各部分的分离感
2. 在函数参数的空格之后使用单个空格
3. 不要在圆括号和参数后面添加空格  


Classes  
--------------  

计算机历史上没有人能够写出完美的软件, 你也不太可能是第一个.  

#### 类的内部结构
Unity 开发 Class 内部结构规范是一个从上往下的结构, 符合阅读习惯  

1. Fields
2. Properties
3. Events/Delegates
4. Monobehaviour (Awake, Start, OnEnable, OnDisable, OnDestroy 等)
5. public Methods
6. Private Methods

#### 方法的顺序
例如, 当 ThrowBall 方法内部调用另一个方法 SetInitialVelocity, 那么 ThrowBall 方法需要在 SetInitialVelocity 方法之上.  
越上方的方法越接近主要逻辑  

#### 单一职责
类是状态和行为的抽象化.  
从系统的顶层开始抽象化, 会得到一个多职责的类.  
例如 ping-pong 游戏中, 对玩家控制的 Paddle 可以抽象出一个具体的类 Paddle  
Paddle 的职责如下:  
1. 存储基础数据, 例如移动速度
2. 确认键盘输入信息
3. 移动 Paddle 的方法
4. 当与 ball 碰撞时发出声音

当然可以在一个 Paddle 类中实现所有的方法.  
所谓的高内聚, 但是问题是想要修改其中一个功能, 需要阅读 Paddle 中所有的代码.  
所以需要解除 Paddle 类与这些具体代码的耦合, 每个功能再次封装成对应的类, Paddle 类持有对这些功能类的引用.  
功能类负责具体功能实现, Paddle 类负责聚合这些功能类. 整体实现了高内聚, 低耦合.  

> ##### TIP 
> 高内聚, 低耦合是为了提供代码的可维护性和可阅读性 
> 解耦更多就是将实现代码转换成逻辑上对于功能类的依赖
> 逻辑关系并不会消失, 而是转移
> 很多时候过度的解耦, 连逻辑关系也一并解耦掉, 没有逻辑关系, 类就成了孤岛, 降低了维护性和阅读性, 反而与优化的本来目的相反
  
Methods  
---------------  
方法和类一样需要保证很小, 只有一个职责. 每个方法需要描述一个动作或者回答一个问题.  
好的方法的名称可以体现方法方法在做什么.  

1. 使用较少的参数: 参数会增加方法的复杂度. 减少参数数量让方法易于阅读和测试
2. 避免过度重载: 可以实现无穷尽的重载方法, 如果实在需要重载方法, 保证方法签名拥有不同数量的参数
3. 避免副作用: 方法只需要实现名称显示的操作. 避免修改任何在方法作用域外的东西, 这被称为副作用. 尽可能使用值传递  
4. 使用新的方法, 而不是 flag 参数: 不要使用 flag 参数来控制方法的运行模式, 而是使用两个不同的方法名称, 例如 GetAngle 方法分解成 GetAngleInDegree 和 GetAngelInRaidans

#### 扩展方法
对于同一个对象的扩展方法, 集中在一个静态类中. 类名称为扩展对象名称+Extensions后缀  

{% highlight csharp %}
public static TransformExtensions
{
    public static void ResetTransformation(this Transform transform)
    {

    }
}
{% endhighlight %}  

注释  
------------  

代码就像讲笑话, 最差的笑话就是去解释这个笑话  

常见的陷阱  
------------  

Clean Code 并不是偶然的, 而是每个个体努力像团队一个思考与编码的结果   
并不是每件事都能像计划一样顺利. Unclean Code 不可避免的会产生, 不管过程有多难, 都需要一直努力的消灭它.  

代码的气味是一个迹象, 表明可能隐藏了不好的代码. 虽然以下症状发生时, 不一定指向了隐藏的问题, 但值得调查:  

1. 神秘的命名: 每个人都喜欢具有神秘感的命名, 但不能在编码标准中
2. 不必要的复杂性: 当你想要预测一个类所有可能扩展需求, 那就是过度工程化. 这经常出现于过长的方法或者过多功能的类中.
3. 缺乏灵活性: 一个小的需求改动, 需要在别的地方多处修改. 仔细检查是否违反了单一职责.
4. 脆弱: 一个小的改变, 导致其他全都停止工作.
5. 不可移动性: 经常编写在不同上下文中可重用的代码. 如果它需要在其他地方部署许多依赖项, 那么需要解耦逻辑的工作方式.
6. 重复代码: DRY原则
7. 过多的注释: 检查命名规则, 切分单一原则  

结语  
--------------  
