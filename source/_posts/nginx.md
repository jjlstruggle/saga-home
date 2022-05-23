---
title: 关于BrowserRouter路由打包刷新页面404问题
category: nginx
tags: [study, nginx, apache]
date: 2021-7-20 16:21:46
---

## 问题描述

> 我们知道一般使用 BrowserRouter 打包之后的项目路由跳转后刷新页面会有 404 的现象,但是又不想换成丑一点 hash 路由,可以使用 nginx 或者 apache 解决这个问题

## 先简单说说 hash 路由和 history 路由

- hash 路由：监听 url 中 hash 的变化(也就是监听#后面内容的变化)，然后渲染不同的内容，这种路由不向服务器发送请求，不需要服务端的支持；

- history 路由：监听 url 中的路径变化(监听整个 url)，需要客户端和服务端共同的支持；

目前单页面应用打包后入口文件应该是根目录下的 index.html 文件,所以使用 history 路由时刷新浏览器后，是真实地向服务器发送了一个 http 的网页请求，请求默认为 xxx/index.html，此时会因为找不到该文件而 404。

## 解决方案

1.可以通过修改 Nginx 的配置文件，在 server 下添加如下内容

```nginx
server {
    ...
    location / {
    # 未找到时强行定向到根目录下的index.html文件作为入口文件即可
		try_files $uri $uri/ /index.html;
    }
    ...
}
```

2.可以通过修改 apache 的配置文件

```apache
<VirtualHost *:80>
  ServerName localhost
  DocumentRoot D:\\apache\\Apache24\\htdocs\\build

  <Directory "D:\\apache\\Apache24\\htdocs\\build">
    RewriteEngine on
    RewriteCond %{REQUEST_FILENAME} -f [OR]
    RewriteCond %{REQUEST_FILENAME} -d
    RewriteRule ^ - [L]state links
    RewriteRule ^ index.html [L]
  </Directory>
</VirtualHost>
```
