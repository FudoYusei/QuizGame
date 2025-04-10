---
title: Unity Property
author: lm
date: 2024-12-26
category: USS
layout: post
mermaid : true
---

Unity Property
-----------------  

Unity 中有两个基础核心系统, 一个是 Serialization, 另一个是 Unity Property. 这两个系统都是不需要开发者特意设置, 但是与 Unity 中各个功能息息相关的底层系统  

Serialization 系统支持 Unity Editor 的所见即所得, 任何在 Editor 中的修改都会持续化存储.  

Unity Property 系统, 一个高性能的属性遍历系统, 使用 visitor pattern 架构, 可以实现 Serialization, data migration, deep data comparison, 和 data binding 各种基于实例属性的功能  

[Unity官方链接-Properties](https://docs.unity3d.com/Manual/properties.html)  

Property 和 PropertyBag
-------------
PropertyBags 是类型的伴生对象, 用于高性能的遍历对应类型的实例的属性, unity 默认使用反射生成 PropertyBag, 自然也有反射冷启动慢的缺点.  
为了提升性能, 可以使用特性标签 [Unity.Properties.GeneratePropertyBag] 和 [assembly.Unity.Properties.GeneratePropertyBagsForAssembly] 来进行代码生成  

Unity 自动为以下情况生成 Property Bags:  
+ Public fields
+ Private or internal fields with [SerializeField] [SerializeReference] [CreateProperty]
+ Public, private, or internal properties tagged with [Unity.Properties.CreateProperty]

> ##### TIP
>
> 不知道为什么, 同时存在 Property 和 PropertyBags 两套API?  
> 个人理解, PropertyBag API 更加底层, 因此性能更好, 自由度更高.  
> Property API 属于高层封装, 使用方便  
{: .block-tip }  

[PropertyBags作为类型伴生对象](https://discussions.unity.com/t/unity-properties-is-it-possible-to-visit-a-type-without-the-need-of-creating-an-instance/941821/8)  


### 两者的关系 
PropertyBags 是类型的伴生对象, 记录了该类型的所有 Properties 信息, 因此 visitor 可以访问某个实例, 根据实例类型找到实例的伴生 PropertyBags, 遍历 Bags 中的 Property,  visitor 访问 Property  

Property 接受 visitor 的访问, Property 类负责在 container 中值的操作, 因此最终想要进行 Property 的操作, 需要实现对于 Property 的 Visit 方法  

{% highlight csharp %}
void IPropertyBagVisitor.Visit<TContainer>(IPropertyBag<TContainer> properties, ref TContainer container)
{
    foreach (var property in propertyBag.GetProperties(ref container))
    {
        property.Accept(this, ref container);
    }
}
        
void IPropertyVisitor.Visit<TContainer, TValue>(Property<TContainer, TValue> property, ref TContainer container)
{
    var value = property.GetValue(ref container);
    // Code goes here.
}
{% endhighlight %}  



Property Paths
--------------
使用字符串形式描述 Property 在 Container 中的位置  

#### Property Container 和 value 之间的关系.  
Container 就是某个类型的实例, Property 就是这个实例的属性, value 就是属性的值.  

为了找到 Container 中的某个属性, 就需要 PropertyPath 定位  

> ##### TIP
>
> 从接触 Property 相关代码开始, 就会看到很多 TContainer 和 TValue 的泛型  
> TContainer 表示实例类型, TValue表示值类型, Property这个类就是描述 value 和 container 的关系, 也就是属性
> 使用泛型的优点是, 不会产生装箱拆箱的开销以及减少代码重复, 缺点就是 JIT 需要生成对应类型的代码, 冷启动慢    
{: .block-tip }  

PropertyVisitor 
----------------- 
Visitor Pattern 实际上还挺形象的: PropertyContainer 作为主人邀请 Visitor 访问自身包含的所有 Properties  

Unity 这里和经典 visitor 设计模式不同, 经典设计模式中 visitor 中的 Visit方法需要严格符合 PropertyContainer 中的属性类型, 受限于属性类型, 实用度较低.  
例如, Container 中有三个类型的属性 int float 和 string, 那么 visitor 需要提供三种 Visit方法, 对应这三个类型  

Unity 的 visitor pattern 进一步抽象出了 Property 类型, Property 类型重要功能就是 set/get value , 而 visitor 实际上也是调用 set/get value 来实现对于 Property 的操作  
同时, Unity 会自动对类型生成 PropertyBags, 相当于元数据, 也就是为 container 中的 properties 创建了目录索引, 让 visitor 可以轻松的遍历 container 中所有的属性, 并基于属性进行操作   

DataBinding 与 Properties  
----------------  
前面介绍了一堆 Unity Property 的相关内容, 实际上我们最常接触到的部分是 PropertyPath, 也就是使用字符串定位实例中的属性.  

动态数据绑定意味着需要在运行时解析绑定规则, 然后遍历实例对象的属性和值, 并且进行对应的绑定操作.  
因此绑定规则中, 需要基本的三个要素:  

1. 数据源实例, Data Source
2. 查找的属性, Data Source Path
3. 值, value
4. 绑定的实例, Target
5. 绑定的属性, BindingProperty

在 UI Toolkit 中, UI 元素就是 Target, 数据实例就是 Data Source.   
但是绑定的方向并不是固定的, 可以是 Source to Target, 也可以是 Target to Source, 或者双向绑定 Two-Way   

#### 数据绑定
数据绑定可以用于在 UI 系统中实现 MVVM 模式, ViewModel 代替了 Controller/Presenter, MVVM 通常使用起来非常方便, 但是引入了额外的 Binding System.  
相当于将复杂度转移到绑定系统中, 使用的时候只需要设置 UI 元素与 ViewModel 中绑定规则即可, 绑定规则中的 source 和 target 本质上是绑定的两端, 哪一端视为 source 和 target 其实只在于添加绑定规则时, 谁是主体.  
因为 Unity 好用的可视化设计, 我们可以直接在 Unity Toolkit 的界面进行绑定规则设置, 此时, 主体视角就是 UI 元素, 那么数据源 Data Source 就是运行时实例对象, target 就代指 UI 元素  



常见的绑定规则:  
1. UI 元素作为 Target
2. UI 元素的属性 
3. Data Source 就是数据源
4. 数据源的属性, PropertyPath

#### NotifyChange  
当Data Source 发生改变时, 需要通知 Binding Target  
但是, 如何实现这个方式呢, 首先, 肯定是没办法在原先的  

{% highlight csharp %}
public int MyProperty
{
    get{
        return m_MyProperty;
    }
    set{
        if(value != m_MyProperty)
        {
            m_MyProperty = value;
            NotifyPropertyChange();
        }
    }
}
{% endhighlight %}  

当属性值改变的时候, Binding System 得到通知调用. 问题在于在于如何监听属性值改变, 运行时绑定需要依靠绑定系统生成, 绑定系统每帧轮循所有注册的绑定规则.   
问题在于, 在不使用数据绑定时, 只有在 UI 元素发生改变时, 才会调用逻辑业务处理函数, 只需要两个函数调用.  
而使用了数据绑定后, 每一帧都需要通过 PropertyBag 和 PropertyPath 检测数据源是否发生变化, 然后再通过相同的方式更新 Binding Target, ViewModel 更新后会调用对应的逻辑业务处理函数   

可以看到, BindingSystem 需要每帧检查所有 Binding 的数据源, 并且通过 PropertyBag 来进行属性上的操作, 无形中都增加了性能开销, 并且增加了理解上的难度, 如果是性能要求高, 可能会踩不少坑.  
BindingSystem 的优点是, 配合 UI Toolkit 的可视化界面, 使用起来简单  


#### Binding object
[Create custom binding types](https://docs.unity.cn/2023.3/Documentation/Manual/UIE-runtime-binding-custom-types.html)  

Binding Object 定义三个要点:  
1. data source 和 data source path, 这个属性实际上被视为私有属性, 注意不是 virtual, 实现了这个接口的类表示有自身私有属性, 未实现这个接口的类使用从父级结构继承来的属性
2. UpdateTriggers, 默认每帧都会更新
3. Update(in BindingContext context), binding object 更新的主体方法


> ##### TIP
>
> 私有属性这个说法挺有意思, 也就是说, 只要定义了这个属性, 就会覆盖掉从父级结构继承来的同名属性, 但是修改这个属性只会影响自身   
> 数据源本身就是独立的
{: .block-tip }  

TypeTraits  
---------------
TypeTraits 类型正如字面意思上, 类型特征, 查看它的代码, 内部都是类型特征相关的属性和方法  

{% highlight csharp %}  
//  
// Summary:
//     Helper class to avoid paying the cost of runtime type lookups. This is also used
//     to abstract underlying type info in the runtime (e.g. RuntimeTypeHandle vs StaticTypeReg)
public static class TypeTraits<T>
{
   // Properties
    static TypeTraits()
    {
        Type typeFromHandle = typeof(T);
        IsValueType = typeFromHandle.IsValueType;
        IsPrimitive = typeFromHandle.IsPrimitive;
        IsInterface = typeFromHandle.IsInterface;
        IsAbstract = typeFromHandle.IsAbstract;
        IsArray = typeFromHandle.IsArray;
        IsEnum = typeFromHandle.IsEnum;
        IsEnumFlags = IsEnum && typeFromHandle.GetCustomAttribute<FlagsAttribute>() != null;
        IsNullable = Nullable.GetUnderlyingType(typeof(T)) != null;
        IsMultidimensionalArray = IsArray && typeof(T).GetArrayRank() != 1;
        IsObject = typeFromHandle == typeof(object);
        IsString = typeFromHandle == typeof(string);
        IsContainer = TypeTraits.IsContainer(typeFromHandle);
        CanBeNull = !IsValueType;
        IsPrimitiveOrString = IsPrimitive || IsString;
        IsAbstractOrInterface = IsAbstract || IsInterface;
        CanBeNull |= IsNullable;
        IsLazyLoadReference = typeFromHandle.IsGenericType && typeFromHandle.GetGenericTypeDefinition() == typeof(LazyLoadReference<>);
        IsUnityObject = typeof(UnityEngine.Object).IsAssignableFrom(typeFromHandle);
    }
}
{% endhighlight %}  

TypeTraits 广泛运用于各种编程语言中, 用于在编译期间得到类型的相关信息.   

#### typeof GetType is  
这三个都是和类型相关的操作符, 三者之间的差别很大   

[具体回答](https://stackoverflow.com/questions/983030/type-checking-typeof-gettype-or-is)  
