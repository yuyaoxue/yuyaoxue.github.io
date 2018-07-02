---
layout: post
title:  "GitHub 自定义域名"
date:   2018-05-29 23:40 +0800
---
GitHub 网站建起来有一周了，GitHub 支持自定义域名，于是就想着买个自己域名作为网站门面

# 购买域名

通过同事的推荐下，在 [namesilo](www.namesilo.com) 购买的域名。

## 在GitHub配置

我是第一次配置，所以想做个总结记录下。

官网手册里写的比较详细，链接
[GitHub 官网配置文档](https://help.github.com/articles/using-a-custom-domain-with-github-pages/)。

在 GitHub 配置，需要以下几个步骤：

1.需要在你的 GitHubPages 的根目录下创建 CNAME 文件，里面只写上你的域名，如    xueyuyao.com

2.进入你的 GitHub Pages 里，选择 Settings。
![Settings](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/domain/image2.png?raw=true)
下面有一个 GitHub Pages 的标签里面的 Custom domain 下面写下你的域名 ，如果你配置的是 www 的，域名前要加 www。

![set Domain](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/domain/image3.png?raw=true)

3.在 namesilo 里配置

登录 [namesilo](www.namesilo.com)，在右上角点击
[Manage My Domains](https://www.namesilo.com/account_domains.php)。

![ManageDomains](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/domain/image1.png?raw=true)

点击右边蓝色的按钮

![Managers DNS](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/domain/image5.png?raw=true)

在框内填写你的对应的信息，最后点击 SUBMIT

![CNAME](https://github.com/yuyaoxue/yuyaoxue.github.io/blob/master/assets/domain/image7.png?raw=true)

提交之后 DNS 配置有延迟，可以通过 nslookup 命令 可以查到 DNS 记录的生存时间还可以指定使用哪个 DNS 服务器进行解释。

通过下面代码测试 DNS 记录配置是否成功。

    dig docs.example.com +nostats +nocomments +nocmd
