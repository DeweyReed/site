---
layout: post
title: [WIP] Java Design Patterns for Android
date: 2019/1/9
categories: Android
---

整理自[Design patterns implemented in Java](https://java-design-patterns.com/)的笔记。主要想找一找有什么能拿来用的宝贝。主要针对Android，其余一笔带过。

<!--more-->

- Abstract Document

    管理一个类的类型。可以动态添加类型（还有其对应的值）。以无类型来保存，以有类型来使用。

    Wiki的伪代码更好懂一些：[Abstract Document Pattern](https://en.wikipedia.org/wiki/Abstract_Document_Pattern)

- Abstract Factory

   一个聚合了其他工厂类和其具体实现的工厂类，这样就不用每次都声明需要哪个工厂类。“抽象”（应该）来自于这个类并不包含具体的实现，只提供了接口。

   对应不同的工厂实现，可以方便地即插即用。

- Acyclic Visitor

## 感觉不怎么会用得到

- Aggregator Microservices, API Gateway

    Spring相关。统一管理请求。