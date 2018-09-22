---
layout: post
title:  "浅谈协程"
date:   2018-09-09 23:00 +0800
---

# 前言

在 Unity 中，对协程的使用度非常高；面试的时候，也多数会被问到。自己都没有系统的整理下协程相关的知识。借此机会，梳理下自己对协程理解，以便更好的理解协程。

## 什么是协程？

协程（Coroutines）通过字面意思是协助程序。

      A coroutine is like a function that has the ability to pause execution and return control to Unity but then to continue where it left off on the following frame。

 这是 [Unity 官网文档](https://docs.unity3d.com/Manual/Coroutines.html) 中对协程的解释.

## 协程的原理

    协程是一个部分执行，遇到条件（yield return）会挂起，知道条件满足才会被唤醒继续执行后面代码的一种函数。

    Unity 在每一帧（Frame）都会去处理对象上的协程。Unity 主要是在 Update 后去处理协程（检查协程的条件是否满足）

Coroutines 不是多线程，不是异步技术，协程都在 MainThread 中执行，而且每个时刻只有一个 Coroutine 在执行。 Coroutine 是一个 function，可以部分执行，当条件满足时，未来会被再次执行直到整个函数执行完毕。

## 协程在 unity 脚本执行顺序的位置

![执行顺序](https://docs.unity3d.com/uploads/Main/monobehaviour_flowchart.svg)

## 测试

根据协程的原理和协程在 Unity momo 脚本执行顺序做一个小测试来验证其原理。

    public class Test : MonoBehaviour
    {
       void Start ()
       {
          Debug.Log("A");
          StartCoroutine(Test_Yield());
          Debug.Log("B");
	    }
        IEnumerator Test_Yield()
        {
          Debug.Log("C");
          yield return StartCoroutine(TODO());
          Debug.Log("D");
        }
        IEnumerator TODO()
        {
          Debug.Log("E");
          yield return null;
          Debug.Log("F");
        }
    }

打印结果：
![结果](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/CoroutineTestLog.png?raw=true)

对于协程的执行顺序多做几次测试，就会多一分理解。

## 猜想

协程中能否可以使用 try catch 捕获异常

测试 1：

    IEnumerator TestCoroutine()
    {
        try
        {
            yield return new WaitForEndOfFrame();
        }
        catch (Exception e)
        {
            Debug.Log("e："+e);
        }
    }

![结果](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/CoroutineTryCatch.png?raw=true)

结论：Coroutine 的 yield return 语句无法在包含 catch 子句的 Try 块体中生成值

测试 2：

    public class Test2 : MonoBehaviour
    {
       void Start ()
       {
         StartCoroutine(TestCoroutine());
       }
    IEnumerator TestCoroutine()
    {
        yield return new WaitForEndOfFrame();
        try
        {
            Debug.Log(obj.name);
        }
        catch (Exception e)
        {
            Debug.Log("e："+e);
        }
    }
    private GameObject obj = null;
    }

打印结果：

    e：System.NullReferenceException: Object reference not set to an instance of an object
    at Test2+<TestCoroutine>c__Iterator0.MoveNext () [0x0004d] in D:\ruanjian\wrok\unity_project\CoroutineSamply\Assets\Scenes\Test2.cs:17
    UnityEngine.Debug:Log(Object)
    <TestCoroutine>c__Iterator0:MoveNext() (at Assets/Scenes/Test2.cs:21)
结论：Coroutine 的普通语句可以使用 try catch 子句。

## 使用 Coroutinue 实现 WaitForMilliSeconds

     public class Test : MonoBehaviour
     {
        void Start()
        {
            StartCoroutine(WaitForMilliSecondsCoroutine(2));
        }

        public IEnumerator WaitForMilliSecondsCoroutine(double milliseconds)
        {
             yield return StartRealtime(milliseconds);
        }

        private IEnumerator StartRealtime(double milliseconds)
        {
             double startTime = DateTime.Now.TimeOfDay.TotalMilliseconds;
             Debug.Log("StartTime：" + startTime);
             double totalTime = startTime + milliseconds;

             while (DateTime.Now.TimeOfDay.TotalMilliseconds < totalTime)
             {
                Debug.Log("endTime：" + DateTime.Now.TimeOfDay.TotalMilliseconds);
                yield return null;
             }
        }
     }

结果打印：
![结果](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/_v_images/CoroutineTest.png?raw=true)
