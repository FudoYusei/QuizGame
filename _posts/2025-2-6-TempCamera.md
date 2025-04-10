---
title: TempCamera
author: Tao He
date: 2025-02-06
category: unitytechnology
layout: post
---

TempCamera  
-------------------  

TempCamera 实现的功能是当前场景作为 Active Scene 加载时, 不会被销毁, 可以作为 Camera 正常渲染屏幕内容.  
但是在别的场景作为 Active Scene 时, 通过 LoadAdditively API 加载时, 自动销毁, 因为 Active Scene 中不能存在两个 MainCamera  

#### 代码解析

{% highlight csharp %}  
        [Tooltip("当指定场景为活动状态时, 当前GameObject不会被销毁")]
        [SerializeField] string m_ActiveWithinScene;
        [Tooltip("为空时,默认当前GameObject")]
        [SerializeField] GameObject m_ObjectToDestroy;

        // Start is called before the first frame update
        void Start()
        {
            if(SceneManager.GetActiveScene().name != m_ActiveWithinScene)
            {
                // 如果 m_ActiveWithinScene 为空字符串, 那么一旦脚本运行, 这个GameObject必然被销毁
                // 会形成一个可以在 Edit 模式下看到的GameObject, 进入 Play 模式就销毁

                if(m_ObjectToDestroy == null )
                {
                    m_ObjectToDestroy = gameObject;
                }

                Destroy(m_ObjectToDestroy);


            }
        }
{% endhighlight %}  

代码很小巧, 唯一的逻辑就是在 active scene 不是我们定义的变量值时, 销毁定义的 GameObject 或者指定的 GameObject  
