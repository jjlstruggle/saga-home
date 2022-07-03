---
title: dependencies与devDependencies的重新认识
category: package.json
tags:
  - study
  - package.json
keywords: dependencies与devDependencies的区别，dependencies与devDependencies的误区
---

> 近期练习项目时发现一个一直理解错误的问题

我们都知道package.json里的devDependencies对应开发环境的依赖,dependencies对应生产环境时的依赖，故我们安装webpack babel等开发环境使用的工具时是
```bash
npm i webpack webpack-cli --save-dev
```
而antd axios等生产环境也要用到的工具时则是
```bash
npm i antd axios --save
```

## 背景

然后我在使用create-react-app的时候产生了一个疑惑,当我eject暴露出crp的所有配置文件之后，发现在cra的package.json中所有依赖竟然都在dependencies之中，包括webpack等脚手架开发时使用到的依赖也一并放在了dependencies里，就觉得特别奇怪，这样难道不会导致打包之后进入最终的产品中吗，然后我就先直接npm run build之后，将开发环境用到的plugin，loader等全部移到devDependencies里然后再build一次，惊奇的发现包的大小一点没变。
![](http://121.40.19.111:7002/cdn/image/20220703/1656863863502.png)

## crp的解答

于是我开始了快乐的百度时光==  （不会没有人有跟我一样的疑惑吧
于是在create-react-app中发现这样一个issues:
![](http://121.40.19.111:7002/cdn/image/20220704/1656864712011.png)
这回答的哥们是谁呀，一看就不懂react(雾)

简单翻译一下就是,丹（react的开发者）说：你想放哪就放哪，dependencies与devDependencies的区别对于web端是没有意义的，所有依赖都会打包入bundle中，而作为nodejs应用服务时则有了区别，因为他们实际是作为runtime部署的，这个时候则不需要devDependencies。而web端是生产出静态的bundle，所以我们将所有依赖都当做dependencies。

## 简单总结
* 在我们普通的web项目中，devDependencies和dependencies是没啥区别的，想放哪就放哪。
* 在node端或者提供第三方包给人家使用时则需要注意devDependencies和dependencies的问题了，因为你不想将devDendencies也提供给人家使用。