---
title: 重载new和delete检测程序内存泄露
date: 2019-05-06 20:00:48
categories: 
 - Cpp
tags:
 - Cpp
---

c++通过重载new和delete检测程序内存泄露方法

<!-- more -->

## 原理
通过c++中重载new和delete运算符，通过重载new记录文件中分配内存的位置，并将其分配的地址作为key值存放在map容器，通过重载delete删除容器中释放的内存，最后在程序关闭后，还存放在map中的内存地址即为泄露内存

## new 与 delete 重载
```c++
void *operator new (
	size_t size,
	const char* file,
	long line
	);

void operator delete(void *p);
void operator delete[](void *p);
```

## 代码
1. 头文件
```c++
#ifndef  _TRACER_H_
#define  _TRACER_H_
#include <map>
//全局操作符重载
void *operator new (
	size_t size,
	const char* file,
	long line
	);
void operator delete(void *p);
void operator delete[](void *p);
class Tracer
{
private:
	class Entry{
	public:
		Entry(const char* file = nullptr, long line = 0) :_file(file), _line(line){

		}
		inline const char* File() const
		{
			return _file;
		}
		inline long Line() const
		{
			return _line;
		}
	private:
		const char* _file;
		long _line;
	};
public:
	Tracer(void);
	virtual ~Tracer(void);
	void Add(void* pointer, const char* file, long line);
	void Remove(void * pointer);
	void Dump();
	static bool Ready;
	std::map<void*, Entry> pointer_map_;
};
extern Tracer tracer;
#endif _TRACER_H_
```

2. 源文件
```c++
#include "StdAfx.h"
#include "Tracer.h"
#include <cstdlib>
#include <iostream>
Tracer tracer;
bool Tracer::Ready = false;
void *operator new (
	size_t size,
	const char* file,
	long line
	){
	void *p = malloc(size);
	if (Tracer::Ready)
		tracer.Add(p, file, line);
	return p;
}
void operator delete(void *p){
	if (Tracer::Ready)
		tracer.Remove(p);
	free(p);
}
void operator delete[](void *p){
	if (Tracer::Ready)
		tracer.Remove(p);
	free(p);
}
Tracer::Tracer(void)
{
	Ready = true;
}
Tracer::~Tracer(void)
{
	Dump();
	Ready = false;
}
void Tracer::Add(void * pointer, const char* file, long line){
	pointer_map_[pointer] = Entry(file, line);
}
void Tracer::Remove(void * pointer){
	auto iter = pointer_map_.find(pointer);
	if (iter != pointer_map_.end()){
		pointer_map_.erase(iter);
	}
}
void Tracer::Dump()
{
	if (!pointer_map_.empty()){
		for(auto iter = pointer_map_.begin();
			iter != pointer_map_.end();
			++iter){
			const char *file = iter->second.File();
			long line = iter->second.Line();
			std::cout << file <<"\t\t" << line << std::endl;
		}		
	}
}
```

3. 定义宏
```c++
#ifndef DebugTracer_h_
#define DebugTracer_h_
#include "Tracer.h"
#define new new(__FILE__,__LINE__)
#endif
```

4. 使用
全局引用定义宏的头文件

