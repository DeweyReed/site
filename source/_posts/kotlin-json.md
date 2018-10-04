---
layout: post
title: Kotlin + JSON 默认参数适配问题
date: 2018/5/18
categories: Android
---

2018年5月18日更新：Moshi 1.6增加了Codegen的选项，可以取代Kotshi。原方案依旧可行。

之前用Gson序列化Kotlin的data class一直都没问题，但一天当data class中出现了默认参数时，问题就来了。

如果JSON中没有对应的Field，按道理Gson应该生成一个使用了默认参数的data class，但它没有，而且会无视Kotlin的不可null，直接生成一个null。

那换一个支持Kotlin的JSON库呗。这时Moshi就很厉害了，自带一个Kotlin的Adapter，用起来很舒服。但是在最后生成Release版本时，才发现安装包大了600+KB。原因是Moshi对Kotlin的支持使用了Kotlin的Reflection，这个就很占空间，虽然也有Kotlin Relection Lite这个轻量级版本，但目前并不支持。

所以，我在

<!--more-->

在Gson的Issue[讨论](https://github.com/google/gson/issues/1148)中，还有Moshi的讨论中，找到了[Kotshi](https://github.com/ansman/kotshi)，问题才最终解决。

Kotlin + Moshi（不带Kotlin插件）+ Kotshi就是最终解决方案。当然AutoValue之类的也可以，不过没深入研究。

Kotshi没有采用一劳永逸的方案，而是让每个Kotlin data class类都加一个annotation，再根据它生成一个adapter，
在序列化和反序列化时手动处理。

不算很方便的方案，但也算比[通过Backing field处理](https://medium.com/@MaxMello/most-elegant-way-of-using-gson-kotlin-with-default-values-and-null-safety-b6216ac5328c)来的舒服。