---
title: C# 方法设置同步超时
date: 2019-05-07 20:00:48
categories: 
 - C#
tags:
 - Timeout
 - C#
---

本例主要是介绍在C#中，如何为一个方法设置超时。

<!-- more -->

	 TaskAction为设置超时的方法
	 TimeoutSeconds为超时的秒(s)数
``` c#
public static T RunTaskWithTimeout<T>(Func<T> TaskAction, int TimeoutSeconds)
{
    System.Threading.Tasks.Task<T> backgroundTask;
    try
    {
        backgroundTask = System.Threading.Tasks.Task.Factory.StartNew(TaskAction);
        backgroundTask.Wait(new TimeSpan(0, 0, TimeoutSeconds));
    }
    catch (AggregateException ex)
    {
        // task failed
        var failMessage = ex.Flatten().InnerException.Message;
        return default(T);
    }
    catch (Exception ex)
    {
        // task failed
        var failMessage = ex.Message;
        return default(T);
    }

    if (!backgroundTask.IsCompleted)
    {
        // task timed out
        return default(T);
    }

    // task succeeded
    return backgroundTask.Result;
}
```

- 调用格式
 var response = RunTaskWithTimeout((Func)delegate { return SomeMethod(); }, 3);
- 说明：为SomeMethod()设置3S的超时
- 参数：
  1. response  为方法SomeMethod()返回值,  这里接收一个bool类型的返回值       

``` vala
 var response = Global.RunTaskWithTimeout(
            (Func)delegate
            {
                return SomeMethod();
            }, 3);            
```
