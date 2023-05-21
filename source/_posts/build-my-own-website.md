---
title: Build My Own Website
date: 2023-05-07 19:00:31
tags:
---

本文介绍如何基于Hexo + Github搭建自己的博客，以及如何配置Hexo NEXT主题以及相关插件。本文博客对应代码位于[GitHub - KenGeng/KenGeng.github.io](https://github.com/KenGeng/KenGeng.github.io)

# 配置GitHub

可以参考Hexo官网提供的[文档](https://hexo.io/zh-cn/docs/github-pages.html#%E4%B8%80%E9%94%AE%E9%83%A8%E7%BD%B2)完成配置，

# 本地初始化Hexo项目

1. 安装Hexo
   
   ```shell
   npm install -g hexo-cli
   echo 'PATH="$PATH:/usr/local/lib/node_modules/hexo-cli/bin"' >> ~/.zshrc
   # npm的默认安装路径可能有变化，可以参考https://zhangxiaoshang.github.io/2018/02/09/Mac-OS-%E4%B8%ADnpm%E5%85%A8%E5%B1%80%E5%AE%89%E8%A3%85%E7%9A%84%E5%8C%85%E7%9A%84%E5%AD%98%E5%82%A8%E8%B7%AF%E5%BE%84/
   source ~/.zshrc
   ```

2. 初始化项目
   
   ```shell
   hexo init <hexo-site>
   cd <hexo-site>
   npm install
   ```
   
   细节内容可以参考[建站 | Hexo](https://hexo.io/zh-cn/docs/setup)
   另外部分默认目录其实不需要使用，可以参考本项目的目录结构进行组织。

3. 本地调试
   
   ```shell
   hexo clean
   hexo generate
   hexo deploy
   
   ```
   
   deploy之后，打开terminal里提示的本地页面进行查看即可。

# 切换到NEXT主题

Hexo有很多精美的主题，我这里使用的是NEXT主题的[Gemini样式](https://github.com/next-theme/hexo-theme-next)，详细内容可以参考[官方文档](https://theme-next.js.org/docs/getting-started/)

```shell
cd <hexo-site>
npm install hexo-theme-next@latest
```

Hexo与NEXT主题都提供了很多配置项，可以参考[Configuration | Hexo](https://hexo.io/docs/configuration.html)和对一些基础信息进行修改，例如名字、各类超链接等。

# 配置插件

NEXT支持丰富的插件，来提供一些小功能例如字数统计等。本博客使用了以下插件：

1. [字数统计 - Hexo-NexT](https://hexo-next.readthedocs.io/zh_CN/latest/next/advanced/%E5%AD%97%E6%95%B0%E7%BB%9F%E8%AE%A1/)

2. [搜索服务 - Hexo-NexT](https://hexo-next.readthedocs.io/zh_CN/latest/next/advanced/%E6%90%9C%E7%B4%A2%E6%9C%8D%E5%8A%A1/)

3. [RSS支持 - Hexo-NexT](https://hexo-next.readthedocs.io/zh_CN/latest/next/advanced/RSS%E6%94%AF%E6%8C%81/)

4. [[不蒜子]文章阅读次数 - Hexo-NexT](https://hexo-next.readthedocs.io/zh_CN/latest/next/advanced/%E4%B8%8D%E8%92%9C%E5%AD%90-%E6%96%87%E7%AB%A0%E9%98%85%E8%AF%BB%E6%AC%A1%E6%95%B0/)

配置方式基本都是npm install对应插件后，修改NexT _config.yml添加相应开关即可。

# 设置Avatar

我们可以设置博客在浏览器的Avatar，这里记录一些使用的工具：

1. 设计自己的Avatar图片：[KFavicon.ico图标生成器 | 一键免费制作ico图标 - 标小智LOGO神器](https://www.logosc.cn/logo/favicon?s=K) 生成之后可以按参考文章4进行设置。

2. 一些常见Icon：[Zhihu Icon | Font Awesome](https://fontawesome.com/icons/zhihu?f=brands&s=solid)

3. 将PNG转成SVG用于作为博客头像：[PNG转SVG - 在线转换图像文件](https://www.aconvert.com/cn/image/png-to-svg/)

# 参考文章

1. [Github + Hexo 搭建个人博客 - 渣渣的夏天](https://zz2summer.github.io/github-hexo-%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/#%E9%81%87%E5%88%B0%E7%9A%84%E9%97%AE%E9%A2%98)

2. [【Hexo】NexT 主题的配置使用记录 - ywang_wnlo - 博客园](https://www.cnblogs.com/ywang-wnlo/p/16110411.html#mermaid)

3. [搭建个人博客 — Hexo+Markdown+Github Pages - 知乎](https://zhuanlan.zhihu.com/p/514982831)

4. [更换Hexo的网页图标/小图片Hexo change page favicon | Yu's Notes](https://wangxiaoyu-go.github.io/2018/11/18/change-theme-favicon/)
