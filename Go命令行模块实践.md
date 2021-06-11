---
title: Go命令行模块实践
date: 2021-03-02 21:00:48
categories: 
-  Go
tags:
-  Go
---

Go 使用标准模块flag开发命令行程序
<!-- more -->

# 案例

 -  greet  --hello zhangsan
  

``` javascript
package main

import (
	"flag"
	"fmt"
)

func main()  {
	wordPtr := flag.String("hello","Mr my","a string")

	var svar string
	flag.StringVar(&svar,"svar","bar","a string var")

	flag.Parse()
	fmt.Println("hello",*wordPtr)
}
```
- 输出

``` javascript
>greet.exe --hello zhangsan
hello zhangsan

>greet.exe
hello Mr my

```

 ## 子命令flag
 
 - 以下代码为计算加减乘除实例

``` javascript
package main

import (
	"flag"
	"fmt"
	"os"
)

func main()  {
	addstr := flag.NewFlagSet("add",flag.ExitOnError)
	substr := flag.NewFlagSet("sub",flag.ExitOnError)
	mulstr := flag.NewFlagSet("mul",flag.ExitOnError)
	divstr := flag.NewFlagSet("div",flag.ExitOnError)
	add1 := addstr.Int("a",0,"第一个数值")
	add2 := addstr.Int("b",0,"第二个数值")
	sub1 := substr.Int("a",0,"第一个数值")
	sub2 := substr.Int("b",0,"第二个数值")
	mul1 := mulstr.Int("a",0,"第一个数值")
	mul2 := mulstr.Int("b",0,"第二个数值")
	div1 := divstr.Int("a",0,"第一个数值")
	div2 := divstr.Int("b",0,"第二个数值")



	if len(os.Args) < 1 {
		fmt.Println("expected 'clac'  subcommands" )
		os.Exit(1)
	}

	switch os.Args[1] {
	case "add":
		addstr.Parse(os.Args[2:])
		fmt.Println("a", *add1)
		fmt.Println("b", *add2)
		fmt.Println(*add1 + *add2)
	case "sub":
		substr.Parse(os.Args[2:])
		fmt.Println("a", *sub1)
		fmt.Println("b", *sub2)
		fmt.Println(*sub1 - *sub2)
	case "mul":
		mulstr.Parse(os.Args[2:])
		fmt.Println("a", *mul1)
		fmt.Println("b", *mul2)
		fmt.Println(*mul1 * *mul2)
	case "div":
		divstr.Parse(os.Args[2:])
		fmt.Println("a", *div1)
		fmt.Println("b", *div2)
		fmt.Println(*div1 / *div2)
	default:
		fmt.Println("expected 'calc'  subcommands" )
		os.Exit(1)
	}

}
```
 
 - calc add  -a 4 -b 2
 - calc sub  -a 4 -b 2
 - calc mul  -a 4 -b 2
 - calc div   -a 4 -b 2
   结果
   

``` javascript
>greet.exe add -a 4 -b 2
a 4
b 2
6
>greet.exe  -a 4 -b 2
expected 'calc'  subcommands

>greet.exe sub -a 4 -b 2
a 4
b 2
2

>greet.exe mul -a 4 -b 2
a 4
b 2
8

>greet.exe div -a 4 -b 2
a 4
b 2
2
```

  


