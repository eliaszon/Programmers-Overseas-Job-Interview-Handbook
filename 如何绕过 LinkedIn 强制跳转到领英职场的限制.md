# **如何绕过 LinkedIn 强制跳转到领英职场的限制**

本教程默认你会科学上网。

有好几个方法，我会着重讲如何用 Clash 自定义规则，来一劳永逸地解决这个问题。

## 全局模式
简单来说就是分三步：
1.科学上网工具设置为全局模式，并且节点为国外 ip
还需要检测一下是否你真的是全局模式了，最简单的方法就是进入这个网址 http://ip111.cn/ 如果三个地点都为你设置的全局国家时，说明你是真正的全局模式，如果三个地点中有不是你设置的国家时，说明没有做到真正的全局模式，请再检查并设置你的科学上网工具。

2.浏览器语言设置为英文
3.清除浏览器 cookies
就能正常登陆 linkedin.com 了

有没有发现这个方法很麻烦？步骤繁琐，而且万一我同时要用国内网站和 LinkedIn 呢？有些国内网站 block 了国外 ip，所以这只是一个权宜之计，我们不如直接一劳永逸解决这个问题。

## Clash 使用 Parser 添加自定义规则并防止更新覆盖
首先说下什么是 Rules，就是你订阅的服务商给你写好的规则，不同的网站匹配不同的节点，简要来说就是，如果是国内的网站就直连，如果是国外的网站就匹配境外ip。

怎么看自己订阅的服务的规则？

### 点开 Clash 客户端的 Profiles
![image04](https://github.com/eliaszon/Programmers-Overseas-Job-Interview-Handbook/blob/main/images/LinkedIn%20Tutorial/04.png)


### 右键你订阅的 Profile，选择 Rules

![image05](https://github.com/eliaszon/Programmers-Overseas-Job-Interview-Handbook/blob/main/images/LinkedIn%20Tutorial/05.png)
在这里你可以看到自己的规则组，可以搜索一下 linkedIn，如果 linkedIn 匹配的不是国外流量而是直连，那么就需要更改。

![image06](https://github.com/eliaszon/Programmers-Overseas-Job-Interview-Handbook/blob/main/images/LinkedIn%20Tutorial/06.png)

但是注意，如果你在规则页面直接添加规则是没用的，因为 Profiles 会定期更新，更新之后就会把你本地的规则覆盖。所以这里我们需要使用 Parser 来添加自定义规则并防止更新覆盖。



---

以下部分来自 Kuro 的博客，由我做了部分具体代码上的修改，我就不重复造轮子了
原文：https://chenjuefei.com:444/117.html

## 具体方法
**Note:** 使用该方法需要Clash版本高于0.11.10，如果低于该版本，请先上github下载安装包进行更新。

1.  打开`Setting`（设置），找到`Profiles`（配置文件下）的`Parsers`。  
    ![image01](https://github.com/eliaszon/Programmers-Overseas-Job-Interview-Handbook/blob/main/images/LinkedIn%20Tutorial/01.png)
2.  点击右侧的`Edit`，打开编辑界面。  
    ![image02](https://github.com/eliaszon/Programmers-Overseas-Job-Interview-Handbook/blob/main/images/LinkedIn%20Tutorial/02.png)
    
3.  输入以下代码（以下为示例代码，需要根据自身情况进行改动）：

```
  - url: https://subcon.dlj.tf/*********/*****
    yaml:
      prepend-rules:
        - DOMAIN-SUFFIX,linkedin.com,🔰国外流量
        - DOMAIN-KEYWORD,linkedin,🔰国外流量
```

其中需要改动的有 **url** 和 **prepend-rules** 。

-   url：改成本人的订阅地址，如果不知道，可以直接在`Profiles`下点击当前订阅的`settings`，复制其中的URL即可。
-   prepen-rules：此处添加你的自定义规则，每一条占一行，按照（规则类型，值，代理策略）排列，使用逗号分隔。注意的是，每一行都需要以一个短横线加空格的开头，同时 **注意对齐**。具体的自定义规则如何书写在本文的后半部分有说明。

4.  编辑完成后，点击右下角保存。到`Profiles`里更新订阅，这样就可以将新的规则添加上了。可以点击`edit rules`查看规则进行验证。之后打开相应网页看是否生效。  
    ![image03](https://github.com/eliaszon/Programmers-Overseas-Job-Interview-Handbook/blob/main/images/LinkedIn%20Tutorial/03.png)

## 自定义规则的书写方式

目前Clash支持的规则类型如下:

-   DOMAIN-SUFFIX：域名后缀匹配
-   DOMAIN：域名匹配
-   DOMAIN-KEYWORD：域名关键字匹配
-   IP-CIDR：IP段匹配
-   SRC-IP-CIDR：源IP段匹配
-   GEOIP：GEOIP数据库（国家代码）匹配
-   DST-PORT：目标端口匹配
-   SRC-PORT：源端口匹配
-   PROCESS-NAME：源进程名匹配
-   RULE-SET：Rule Provider规则匹配
-   MATCH：全匹配

其中我们使用最多的就是`DOMAIN-SUFFIX`域名后缀了。其余规则类型根据说明大家也可以自行探索。

**Note:**

-   使用`PROCESS-NAME`需要 Clash for Windows v0.11.5 及以上版本；规则需要匹配完整的进程名（包括可执行文件后缀）方可生效；该规则不适用于TAP流量。
-   用户界面暂不支持添加`RULE-SET`规则，请手动编辑配置文件。
-   `MATCH`需要位于规则列表末尾。

## 代理策略的选择

一般情况我们使用`DIRECT`，`GLOBAL`，`REJECT`。`DIRECT`表示不走代理，即不通过代理节点直接连接。`GLOBAL`则是走全局代理节点。`REJECT`则表示禁止连接，使用`REJECT`后，将会屏蔽对应网站。

有一些订阅还提供了很多自制的策略，可以自行尝试。使用时直接输入名字即可，中文也是支持的哦。有人可能会问，**前面有奇奇怪怪的符号怎么办？**

点击`General`，点击`Home Directory`下的`Open Folder`，打开配置文件夹，点击`Profiles`文件夹，找到对应的`yml`文件，用`记事本`等软件打开，直接复制粘贴就可以啦。

## 原理介绍

为什么这种方法能够防止覆盖呢？原因是Parser功能是配置文件进行预处理，因此，每一次更新都会执行一次Parser，因此自定义规则会永远存在，只要你不删除Parser。

参考：
https://chenjuefei.com:444/117.html
https://www.jamesdailylife.com/clash_2022





---

那么手机端呢？手机端的话就直接开全局模式进去就行。