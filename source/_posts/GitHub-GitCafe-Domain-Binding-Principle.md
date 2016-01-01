---
title: GitHub/GitCafe Domain Binding Principle
date: 2016-01-01 17:21:02
categories:
	- Summarize
tags:
	- Git
	- Domain
---
根据[Rainux Luo, CTO@GitCafe](https://www.zhihu.com/question/26609475)在知乎的描述和自己的理解做出描述：

1. GitHub/GitCafe 会为每个包含 gh-pages/gitcafe-pages 分支的项目创建包含该分支内容的、符合特定命名规则的目录 wwwroot。
2. GitHub/GitCafe 虚拟了一个服务器，它被配置成使用 Name-based Virtual Server  方式来发布该分支下的目录内容，这个被虚拟服务器的域名就是 `{username}.gitxxx.io` 或者是用户绑定的域名，那么自然可以访问静态页面。
3. 那么域名是如何绑定到这个虚拟服务器上的呢？首先要知道 `A` 和 `CNAME` 记录的概念。
<!-- more -->
  * `A` 主要用来指定主机或者域名对应的 IP 地址，告诉 DNS 网站所在服务器的地址，这样用户输入域名后 DNS 才能找到网站。
  * `CNAME` 别名记录，这种记录允许您将多个名字映射到同一台计算机。

  <div class="tip">我们的服务器的地址可能会随时发生变动，并不是固定不变的，这时候就要用到 CNAME 记录，它会指定一个 DNS 服务商提供的二级域名，这样 DNS 通过这个二级域名也能找到服务器的 IP 地址。使用CNAME的好处是，即使服务器的IP地址发生变更，也不用更改解析记录，DNS会自动更新解析，相比A记录少了一些改动的麻烦。</div>

4. Pages 用户将要绑定的域名 A 记录或 CNAME 记录指向该虚拟服务器所在主机，所有人就可以像访问普通静态网站一样访问 Pages 站点。
5. 当我们绑定了域名后，`{username}.gitxxx.io` 就和我们绑定的域名一样能够访问到项目分支所在的虚拟主机，由于 `{username}.gitxxx.io` 是 GitHub/GitCafe 下的子域名，为了告诉访问者我们的域名是哪个，因此要在项目分支下添加一个 CNAME 文件。
