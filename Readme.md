## 实战字典

### fileleak🔢

------

此字典为泄露文件检查 针对API与文件也存在~~应当~~会出现的备份文件

### 参数字典🔢

-----

此字典为参数控制格式为`  ?file=` 个人感觉收集的不够或者Fuzz方式不对

### 备份文件字典🔢

-----

此字典为备份文件 分为后缀名与备份文件名 .

此处个人认为后缀名比较完善 但备份文件名命中率不是很高 需要结合当前FUZZ路径与web名称进行实时修改

### 字符字典🔢

-----

此字典为字符控制 ,存在三字符 四字符 五字符 . 用于控制**缩写**存在的web路径 

例如:

```
http://zhandian.com/zhandian/druid/spring.html
http://zhandian.com/zd/druid/spring.html
```
### 手机号字典🔢

-----

此字典为手机号参数 ; 没什么好说的 测试手机号 有规律的手机号

### 用户名&密码字典🔢

-----

此字典为用户名&密码字典 成功率只能说还行 密码为常见密码,但没有很仔细的一个个检查是否为真的top密码;

后期优化


### 请求头字典🔢

-----

此字典为请求头字典,一眼能看懂

## FUZZ心得&备忘❤️

推荐工具[ffuf](https://github.com/ffuf/ffuf)  线程(协程)默认为40一般足够  嫌慢可以指定参数-t 200 但一定小心 大概率目标扛不住

### 匹配输出(MATCHER)

ffuf提供了仅获取具有特定特征的状态码、行数、响应大小、字数以及匹配正则表达式的模式进行响应输出。

|      | 匹配输出                | 过滤输出                   |
| ---- | ----------------------- | -------------------------- |
|      | -mc ：指定状态代码      | -fs ：按响应大小过滤       |
|      | -ml：指定响应行数       | -fc : 按状态码过滤         |
|      | -mr: 指定正则表达式模式 | -fr : 按正则表达式模式过滤 |
|      | -ms：指定响应大小       | -fw : 按字数过滤           |
|      | -mw：指定响应字数       | -fl ：按行数过滤           |

```ps
#Get请求进行Fuzz,将"HTTP"标志位换为字典 过滤大小为0的响应
.\ffuf.exe -w D:\字典\fileleak\H2-9000.txt:HTTP  -t 200 -H "X-Originating-Ip: 127.0.0.1" -H "X-Remote-Ip: 127.0.0.1" -H "X-Forwarded-For: 127.0.0.1" -H "X-Remote-Addr: 127.0.0.1" -H "Cf-Connecting-Ip: 127.0.0.1" -e .txt,.pub,.bak,.zip,.rar -u http://zhandian.com/HTTP -fs 0
```

这里 -fs -fc -ms -fc -fl -ml -fw -mw 都是控制匹配的参数, FFUF工具很好用但是在header上也存在无法批量的弊端 建议学习一下

递归扫描

```
.\ffuf.exe -w D:\字典\fileleak\H-fileleak-top7000.txt:HTTP -u http://zhandian.com/HTTP -recursion-depth 3
```

双字典匹配

```
.\ffuf.exe -t 200 -w D:\字典\fileleak\H-fileleak-top7000.txt:HTTP2,D:\字典\fileleak\H-fileleak-top7000.txt:HTTP1 -u http://HTTP1/HTTP2
```

POST请求

通过模糊测试POST请求，实际上可以实现暴力破解。这里需要在password.txt里导入常见的密码字典库，并过滤掉401返回值

```
.\ffuf.exe -w password.txt -X POST -d "username=admin\&password=FUZZ" -u https://zhandian.com/login.php -fc 401
```
