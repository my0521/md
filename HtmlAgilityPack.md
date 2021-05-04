---
title: HtmlAgilityPack 
date: 2018-01-01 20:00:48
categories: 
- C#
tags:
- C#
---

HtmlAgilityPack是一个基于.Net的、第三方免费开源的微型类库，主要用于在服务器端解析html文档

<!-- more -->

## 下载
官网[https://html-agility-pack.net/][1]
Github[https://github.com/linezero/HtmlAgilityPack][2]


## 百度翻译官网示例
``` cs
static void Main(string[] args)
{
    // 原文
    string q = "apple";
    // 源语言
    string from = "en";
    // 目标语言
    string to = "zh";
    // 改成您的APP ID
    string appId = "20200416000421957";
    Random rd = new Random();
    string salt = rd.Next(100000).ToString();
    // 改成您的密钥
    string secretKey = "n_Mf3virajBdg0JY37os";
    string sign = EncryptString(appId + q + salt + secretKey);
    string url = "http://api.fanyi.baidu.com/api/trans/vip/translate?";
    url += "q=" + HttpUtility.UrlEncode(q);
    url += "&from=" + from;
    url += "&to=" + to;
    url += "&appid=" + appId;
    url += "&salt=" + salt;
    url += "&sign=" + sign;
    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
    request.Method = "GET";
    request.ContentType = "text/html;charset=UTF-8";
    request.UserAgent = null;
    request.Timeout = 6000;
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();
    Stream myResponseStream = response.GetResponseStream();
    StreamReader myStreamReader = new StreamReader(myResponseStream, Encoding.GetEncoding("utf-8"));
    string retString = myStreamReader.ReadToEnd();
    myStreamReader.Close();
    myResponseStream.Close();
    Console.WriteLine(retString);
    Console.ReadLine();
}
// 计算MD5值
public static string EncryptString(string str)
{
    MD5 md5 = MD5.Create();
    // 将字符串转换成字节数组
    byte[] byteOld = Encoding.UTF8.GetBytes(str);
    // 调用加密方法
    byte[] byteNew = md5.ComputeHash(byteOld);
    // 将加密结果转换为字符串
    StringBuilder sb = new StringBuilder();
    foreach (byte b in byteNew)
    {
        // 将字节转换成16进制表示的字符串，
        sb.Append(b.ToString("x2"));
    }
    // 返回加密的字符串
    return sb.ToString();
}
```

## 翻译html文件
1. TransResult
``` vala
    class TransResult
    {
        public string src { get; set; }
        public string dst { get; set; }
    }
```
2. TransObj
``` cs
    class TransObj
    {
        public string from { get; set; }
        public string to { get; set; }
        public List trans_result { get; set; }
    }
```


3. 加载HTML文件并替换查找的节点
``` gradle
 HtmlAgilityPack.HtmlDocument doc = new HtmlAgilityPack.HtmlDocument();//webClient.Load(Common.FileName);
 doc.Load(fileName,Encoding.UTF8);
 //查找所有的叶子节点文本
 HtmlNodeCollection htmlNodes = doc.DocumentNode.SelectNodes("//body//text()[(normalize-space(.) != '') and not(parent::script) and not(*)]");
 HtmlNode node = null;
 for (int j = 0; j < htmlNodes.Count; j++)
 {
     node = htmlNodes[j];
     string InnerText = node.InnerText.Trim();
     if (-1 != InnerText.IndexOf("$(function()"))
     {
          continue;
     }
     Console.WriteLine(InnerText);
     string retString = Fanyi(InnerText.Replace("\n", "").Replace("\r", ""));
     StringReader sr = new StringReader(retString);
     if (-1 != retString.IndexOf("error_code") && -1 != retString.IndexOf("error_msg"))
     {
          txtMSG.Text += (InnerText + "## ERROR:" + retString + "\r\n");
          System.Threading.Thread.Sleep(1000);
          continue;
     }
     JsonTextReader jsonReader = new JsonTextReader(sr);
     JsonSerializer serializer = new JsonSerializer();
     TransObj response = serializer.Deserialize(jsonReader);
     Application.DoEvents();
     Console.WriteLine(response.trans_result[0].dst);
     //node.InnerHtml = response.trans_result[0].dst;
     node.ParentNode.ReplaceChild(HtmlTextNode.CreateNode(response.trans_result[0].dst), node);
 }
```


 


## 注意事项
1. 加载html时注意编码问题，方式加载后出现乱码

  [1]: https://html-agility-pack.net/
  [2]: https://github.com/linezero/HtmlAgilityPack
