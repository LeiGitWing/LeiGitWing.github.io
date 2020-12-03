---
layout: post
title:  勾陈 | iPic中使用阿里云OSS
date:   2020-10-25 00:17:03 +0800
categories: jekyll update
---
![](http://riri191220.img-cn-shenzhen.aliyuncs.com/images/4zntf.png?x-oss-process=style/resize)

测试成功，画面能正确显示在网页上。所以，应该是开智的网站并不支持这种格式的图片，必须得用绝对路径才能找得到。

无意间看到去年自己写的一份iPic中使用阿里云OSS图床，这是第一份激情四溢写的技术文档，当时想先从身边成功的小事写起，让更多人学会如何使用ipic图床。借此机会，搬到博客上，留作纪念。

# iPic中使用阿里云OSS图床
[toc]

## 0. 起源
刚开始配置了mweb的阿里云图床，因为经常使用mweb写作，使用mweb的内置上传图片功能非常方便。但有时只是想传一张图片，虽然ipic自带的新浪图床很好用，但我还是想统一图片格式，就想在ipic中用阿里云OSS做图床。

## 1. 步骤

### step 1 购买服务器
购买阿里云OSS服务，现在阿里云正在促销，我选的是9元40G包年的服务器。[对象存储OSS_海量安全高可靠的云存储 - 阿里云](https://www.aliyun.com/)

### step 2 创建bucket
打开自己的OSS控制台页面，选择创建bucket，配置好图片样式。我用的是宽度固定，高度自适应的缩略方式，宽度固定在750。

### step 3 ipic中添加图床
![-w646](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576851479646216035569872913.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)

点击自己的头像就能看AccessKey

![-w418](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576851989483316035569872925.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)

点进之后阿里云会提示如果点击`继续使用AccessKey`可能会有安全风险，因为这个AcessKey具有该账户的所有权限，基于安全考虑，我们点击`开始使用子用户AccessKey`

![-w604](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576852304898016035569872933.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)

接着按照官网提示一步步填好信息，就能创建一个专门管理OSS权限的子用户啦。
注意在选择权限时点击`AliyunOSSFullAccess`

![-w746](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576852449567616035569872943.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)

点击开始创建后会给出这个用户的AccessKeyID和AccesssKeySecret，分别填入ipic的Access Key和Secret Key中

![-w749](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576852607046516035569872954.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)

接着就是给创建了的子用户授权--可以管理OSS，点击访问控制RAM条的策略管理

![-w194](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576852881326016035569872963.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)


点击右上角的新建授权策略

![-w1074](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576852988884016035569872968.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)


在第一步中选择AliyunOSSFullAccess

![-w750](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576853029698616035569872982.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)


在第二步中的策略内容
[ipic官方提示](https://toolinbox.net/iPic/AddAliOSS.html)要改成如下格式，**注意，其中的`ipic-test`要改成你自己的Bucket名**。如果不改，就会失败。

```
{
  "Statement": [
    {
      "Action": "oss:*",
      "Effect": "Allow",
      "Resource": [
        "acs:oss:*:*:ipic-test",
        "acs:oss:*:*:ipic-test/*"
      ]
    }
  ],
  "Version": "1"
}
```

![-w748](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576853544244716035569872993.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)

最后在自定义授权策略中能看到自己创建的策略就表示已经成功啦。

![-w1062](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576853658443716035569873002.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)

完成了新建子用户和授权策略后，最后一步就是将这个策略授权给子用户。侧边栏中找到用户管理，点击授权。找到我们刚刚创建的策略名，点击`>`，确认就完成了这一步。

![-w746](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576854056780616035569873011.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)

至此我们能用新建好的子用户安全地访问OSS了。

### step 4 填写http prefix

![-w1341](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576854639345916035569873019.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)

如果你有设置自己的图片样式，就需要把Bucket域名中的oss改成img，比如
- 一般Bucket域名：http://191220.oss-cn-shenzhen.aliyuncs.com
- 改成 http://191220.img-cn-shenzhen.aliyuncs.com

图片处理样式支持根据阿里云的域名规则，如下图的配置，网址后缀可以写成`?x-oss-process=style/resize`

![-w1024](http://riri191220.oss-cn-shenzhen.aliyuncs.com/2020/10/25/1576855026798016035569873027.jpg?x-oss-process=image//auto-orient,1/resize,m_lfit,w_750/quality,q_90)

如果你没有图片处理样式，那只要填入一般Bucket域名就可以愉快地用上阿里云OSS图床了。


## reference

[在iPic中添加阿里云OSS - Toolinbox](https://toolinbox.net/iPic/AddAliOSS.html)

## logging
2020-10-25 移动到博客

2019-12-20 done，almost 1h

2019-12-20 init