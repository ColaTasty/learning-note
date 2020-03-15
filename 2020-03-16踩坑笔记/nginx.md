# Nginx 踩坑笔记
## 拒绝网站的其他访问方式
今天看到 `nginx` 的日志文件 `access.log` 时，发现了如下的请求：

```shell
5.101.0.209 - - [15/Mar/2020:05:30:12 +0800] "GET /home HTTP/1.1" 302 382 "http://49.xxx.xxx.229:80/index.php?s=/Index/\x5Cthink\x5Capp/invokefunction&function=call_user_func_array&vars[0]=md5&vars[1][]=HelloThinkPHP" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.108 Safari/537.36" "-"
5.101.0.209 - - [15/Mar/2020:05:30:12 +0800] "GET /auth/login HTTP/1.1" 200 36376 "http://49.xxx.xxx.229:80/home" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.108 Safari/537.36" "-"
182.254.52.17 - - [15/Mar/2020:05:46:11 +0800] "GET http://xxx.com/auth/login HTTP/1.1" 200 36383 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:57.0) Gecko/20100101 Firefox/57.0" "-"
182.254.52.17 - - [15/Mar/2020:05:50:14 +0800] "GET http://49.xxx.xxx.229/evox/about HTTP/1.1" 404 46351 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:57.0) Gecko/20100101 Firefox/57.0" "-"
182.254.52.17 - - [15/Mar/2020:05:52:05 +0800] "GET http://49.xxx.xxx.229/home HTTP/1.1" 302 382 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:57.0) Gecko/20100101 Firefox/57.0" "-"
182.254.52.17 - - [15/Mar/2020:05:52:05 +0800] "GET http://49.xxx.xxx.229/auth/login HTTP/1.1" 200 38710 "http://49.xxx.xxx.229/home" "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:57.0) Gecko/20100101 Firefox/57.0" "-"
14.215.156.57 - - [15/Mar/2020:06:02:41 +0800] "GET /static/img/988.cd3b1e7.jpg HTTP/1.1" 200 43477 "-" "-" "-"
182.254.52.17 - - [15/Mar/2020:06:04:50 +0800] "GET http://49.xxx.xxx.229/auth/login HTTP/1.1" 200 36387 "-" "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:57.0) Gecko/20100101 Firefox/57.0" "-"
```
其中划重点的是这个几个请求  
* `http://xxx.com/auth/login`
* `http://49.xxx.xxx.229/evox/about`
* `http://49.xxx.xxx.229/home`  

看到这个几个请求，出于好奇，我自己用浏览器访问了一下，结果让我大吃一惊！  
### **访问到的页面是我配置的另一个php网站，在nginx的配置文件里面并没有配置这个域名或者IP访问！**  
为此我借助很久以前从一位师兄那里学到的操作
```smartyconf
location ~ /.* {
    deny all;
}
```  
把本不该被访问的域名请求配置了一遍，这才解决问题，很难想象，如果我这是在部署重要网站，造成的损失可能巨大！
所以今天必填此坑，写下这个笔记提醒自己，以绝后患。