---
layout: post
title: 重要的配置修改
cover: cover.jpg
date:   2015-05-27 12:00:00
categories: elasticsearch
---

## 重要的配置修改Important Configuration Changes ##

Elasticsearch附带了非常好的默认值， 尤其是当它涉及到性能相关的设置和选项。如果有疑问(如果没有弄清楚)，请不要动配置。我们目睹了许多因为用了错误的配置而导致集群的毁灭，就是因为他们的管理员认为他们的修改可以使性能百倍的提升！

**注意**：请阅读本节内容！呈现所有配置都同样重要，并且不以任何特定的顺序列出。请在所有配置选项读取，并**将它们应用到你的集群**。

**1. 指定集群名称Assign Names**
Elasticseach默认的集群名称都为elasticseach，为生产环境的集群重命名是一个明智的做法，简单的做法能防止意外的发生，如某个人的笔记本加入到集群中。集群名称改成elasticsearch_production，这么一个简单的修改可以避免很多的痛心。  
在elasticsearch.yml中修改：  
`cluster.name: elasticsearch_production`




























https://www.elastic.co/guide/en/elasticsearch/guide/master/important-configuration-changes.html
