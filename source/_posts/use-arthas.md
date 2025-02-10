---
title: Use Arthas to get codes of generated class
date: 2025-02-10 19:00:31
tags:
---

# 下载Arthas
curl -O https://arthas.aliyun.com/arthas-boot.jar

# 启动Arthas
```bash
java -jar arthas-boot.jar
# 输入想要查看的Java进程ID, arthas会attach到对应进程
```

# 查询包含"Generated"关键字的Class
```bash
sc *Generated*
```

# 反编译指定已加载类的源码
```bash
jad a.b.c.TargetGeneratedClass
```


