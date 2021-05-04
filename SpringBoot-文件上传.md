---
title: SpringBoot-文件上传
date: 2019-05-06 20:00:48
categories: 
 - SpringBoot
tags:
 - SpringBoot
 - 文件上传
---
SpringBoot 上传单文件和多文件的处理方案

<!-- more -->


## 添加POM依赖
``` vbscript-html
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

## 文件配置
``` livecodeserver
spring:
  servlet:
    multipart:
      # 启用
      enabled: true
      # 上传文件单个限制
      max-file-size: 1MB
      # 总限制
      max-request-size: 6MB
```

## 表单upload.html
``` vbscript-html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<hr/>
<h3>1、单文件上传</h3>
<form method="POST" action="/upload1" enctype="multipart/form-data">
    上传人：<input type="text" name="userName" /><br/>
    文件一：<input type="file" name="file" /><br/>
    <input type="submit" value="Submit" />
</form>
<hr/>
<h3>2、多文件上传</h3>
<form method="POST" action="/upload2" enctype="multipart/form-data">
    上传人：<input type="text" name="userName" /><br/>
    文件一：<input type="file" name="file" /><br/>
    文件二：<input type="file" name="file" /><br/><br/>
    <input type="submit" value="Submit" />
</form>
</body>
</html>
<hr/>
```

## 添加controller
处理"/"请求，跳转到文件上传页面，映射后台upload.html文件
``` java
@Controller
public class FilePageController {
    @GetMapping("/")
    public String uploadFilePage() {
        return "upload";
    }
}
```

## 处理文件上传逻辑
``` gradle
@RestController
public class FileController {
	private static final Logger LOGGER = LoggerFactory.getLogger(FileController.class);

	/**
	 * 如果单个文件大小超出1MB，抛出异常 FileSizeLimitExceededException: The field file exceeds its
	 * maximum permitted size of 1048576 bytes.
	 */
	@RequestMapping("/upload1")
	public String uploadFile(HttpServletRequest request, @RequestParam("file") MultipartFile file) {
		Map<String, String[]> paramMap = request.getParameterMap();
		if (!paramMap.isEmpty()) {
			LOGGER.info("paramMap:" + paramMap);
		}
		try {
			if (!file.isEmpty()) {
				// 打印文件基本信息
				LOGGER.info("Name：" + file.getName());
				LOGGER.info("OriginalFilename == >>{}", file.getOriginalFilename());
				LOGGER.info("ContentType == >>{}", file.getContentType());
				LOGGER.info("Size == >>{}", file.getSize());
				// 文件输出地址
				String filePath = "D:/logs/file/";
				new File(filePath).mkdirs();
				File writeFile = new File(filePath, file.getOriginalFilename().substring(file.getOriginalFilename().lastIndexOf("\\") + 1));
				file.transferTo(writeFile);
			}
			return "success";
		} catch (IOException e) {
			e.printStackTrace();
			return "系统异常！";
		}
	}

	/**
	 * 如果上传文件总大小超过6MB，抛出异常 SizeLimitExceededException: the request was rejected
	 * because its size (9052616) exceeds the configured maximum (6291456)
	 */
	@RequestMapping("/upload2")
	public String upload2(HttpServletRequest request, @RequestParam("file") MultipartFile[] fileList) {
		Map<String, String[]> paramMap = request.getParameterMap();
		if (!paramMap.isEmpty()) {
			LOGGER.info("paramMap == >>{}", paramMap);
		}
		try {
			if (fileList.length > 0) {
				for (MultipartFile file : fileList) {
					// 打印文件基础信息
					LOGGER.info("Name == >>{}", file.getName());
					LOGGER.info("OriginalFilename == >>{}", file.getOriginalFilename());
					LOGGER.info("ContentType == >>{}", file.getContentType());
					LOGGER.info("Size == >>{}", file.getSize());
					// 文件输出地址
					String filePath = "D:/logs/file/";
					new File(filePath).mkdirs();
					File writeFile = new File(filePath,
							file.getOriginalFilename().substring(file.getOriginalFilename().lastIndexOf("\\") + 1));
					file.transferTo(writeFile);
				}
			}
			return "success";
		} catch (Exception e) {
			e.printStackTrace();
			return "fail";
		}
	}

}
```


