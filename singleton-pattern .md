---
title: 单例模式
date: 2020-05-05 20:00:48
categories: 
- 设计模式
tags:
- Java
- 设计模式
---
记录单例模式的应用场景，3种实现方式及其优缺点
<!-- more -->

### 应用场景

1. 需要频繁实例化然后销毁的对象
2. 创建对象时耗时过多或者耗资源过多，但又经常用到的对象
3. 有状态的工具类对象
4. 频繁访问数据库或文件的对象

### 实现思路
1. 构造函数私有化(不含有其他构造函数)
2. 私有静态引用指向自身单例类
3. 返回私有静态引用的公有静态方法

#### 饿汉式
在类装载时初始化

1. 代码实现
``` java

    public class SingleClass {	
    
    	private static SingleClass instance = new SingleClass();
    	
    	private  SingleClass() {		
    	}	
    
    	public static SingleClass GetInstance(){
    		return instance;		
    	}
    }
```

2. 优缺点
- 优点
	1. 线程安全
	2. 在类加载的同时已经创建好一个静态对象，调用时反应速度快
- 缺点
	1. 资源利用率不高，可能GetInstance()永远不会被调用，但执行该类的其他静态方法或者加载该类	class.forName(SingleClass)时，实例仍然初始化
	
#### 懒汉式
在第一次获取实例时初始化

1. 代码实现
``` java
public class SingleClass {	
	private static SingleClass instance = null;
	
	private  SingleClass() {		
	}
	
	public static SingleClass GetInstance(){
		if(null == instance){
			instance = new SingleClass();
		}		
		return instance;		
	}
}
``` 
2. 优缺点
- 优点
	1. 避免了饿汉式中资源利用率不高的缺点
- 缺点
	1. 第一次加载时反应不快，由于java内存模型的一些原因，偶尔失败
	2. 非线程安全，在多线程环境下使用需要加锁处理，不推荐使用

#### 静态内部类 (推荐)
1. 代码实现
``` java
public class SingleClass {	
	private  SingleClass() {		
	}
	
	private static class SingleHelper{		
		private static SingleClass instance = new SingleClass();
	}	
	
	public static  SingleClass GetInstance(){		
		return SingleHelper.instance;		
	}
}
``` 
2. 优缺点
- 优点
	1. 资源利用率高，不执行GetInstance()时，可以执行该类的其他静态方法
- 缺点
	1. 第一次加载时反应稍慢
