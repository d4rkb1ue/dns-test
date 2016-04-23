#把 Github 用作 DNS 设置二级域名跳转

#背景

我有一个博客使用 Github Pages 托管。绑定了`drkbl.com`为CNAME。默认情况下，Github支持

- drkbl.com
- www.drkbl.com

两个域名跳转到我的个人主页 `d4rkb1ue.github.io`

我想开通subdomain指向我的不同项目，希望跳转到项目页。就像这样

```
project-name.drkbl.com

--跳转到-->

https://github.com/d4rkb1ue/project-name
```

而DNS是无法直接设置根目录以下的CNAME的。无法直接在DNS服务商那里处理这样的跳转。而且不借助其他服务器的情况下，也无法设置302跳转。
因为压根没有服务器来解析`project-name.drkbl.com`，Github的用户主页 `username.github.io` 是不提供这个的解析的。


#gh-pages 的分支跳转

Github 的项目支持使用 `gh-pages` 分支生成项目页。绑定在
```
http://d4rkblue.github.io/project-name
```

并且也支持设置CNAME。

#使用 gh-pages 分支 + CNAME 跳转任何网页

##1. 初始化库

1. 在github上建立一个用做 DNS 跳转的库，以 `dns-test` 为例

2. 复制到本地
```
git clone git@github.com:d4rkb1ue/dns-test.git
```

1. 在本地库目录里，切换到 gh-pages 分支
```
cd dns-test
git checkout --orphan gh-pages
``` 


##2. 设置跳转

1. 创建 HTML 302跳转文件 命名为 `index.html`。以跳转到百毒为例。
```html
<head>
	<meta http-equiv="refresh" content="0; url= http://www.baidu.com" />
</head>
```

2. add, commit
```
git add index.html
git commit -m "302 baidu.com"
```

3. 提交到 gh-pages 分支
```
git push origin gh-pages
```

4. 等待上传完成后，就可以测试
```
http://d4rkb1ue.github.io/dns-test
```

*如果 d4rkb1ue.github.io 本身就是用户pages的话，会跳转两次*

##3. 修改 DNS 设置

在 DNS 服务商那里修改 DNS 设置。添加 CNAME 记录。有两种方式

- 设置全局 `*.domain.com` 跳转到 `username.github.io`

- 设置具体域名 `baidu.domain.com` 跳转到 `username.github.io`

*我采取了全局 CNAME。这个域名完全绑定在 Github 上了。*

##4. 设置 CNAME 文件

1. 创建 CNAME 文件
```
echo "baidu.drkbl.com" >> CNAME
```

2. add, commit, push
```
git add CNAME
git commit -m "add CNAME"
git push origin gh-pages
```

3. 可以在项目 setting -> GitHub Pages 部分中看到
> Your site is published at http://baidu.drkbl.com. 

##5. 完成

**现在可以使用 http://baidu.drkbl.com 跳转到 http://www.baidu.com 啦！**


---
#参考
- [Github pages help](https://help.github.com/articles/using-a-custom-domain-with-github-pages/)

- [搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门 @阮一峰](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)
