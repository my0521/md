---
title: SpringBoot-Exception
date: 2019-05-06 20:00:48
categories: 
 - SpringBoot
tags:
 - SpringBoot
 - Exception
---

SpringBoot中统一异常处理方案

<!-- more -->


## 统一异常处理格式
1. 2个基础注解
- @ControllerAdvice 定义统一的异常处理类
- @ExceptionHandler 定义异常类型对应的处理方式
2. 代码实现
``` actionscript
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.servlet.ModelAndView;
import javax.servlet.http.HttpServletRequest;
// 异常以Json格式返回 等同 ExceptionHandler + ResponseBody 注解
// @RestControllerAdvice
@ControllerAdvice
public class HandlerException {
    /**
     * 自定义业务异常映射,返回JSON格式提示
     */
    @ExceptionHandler(value = ServiceException.class)
    @ResponseBody
    public ReturnException handler01 (HttpServletRequest request,ServiceException e){
        ReturnException returnException = new ReturnException() ;
        returnException.setCode(600);
        returnException.setMsg(e.getMessage());
        returnException.setUrl(String.valueOf(request.getRequestURL()));
        return returnException ;
    }
    /**
     * 服务异常
     */
    @ExceptionHandler(value = Exception.class)
    public ModelAndView handler02 (HttpServletRequest request,Exception e){
        ModelAndView modelAndView = new ModelAndView() ;
        modelAndView.addObject("ExeMsg", e.getMessage());
        modelAndView.addObject("ReqUrl", request.getRequestURL());
        modelAndView.setViewName("/exemsg");
        return modelAndView ;
    }
}
```


