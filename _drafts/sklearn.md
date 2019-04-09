---
layout: post
title: sklearn
---

1. 训练样本用fit_transform，而测试样本用transform
	
	```
	仔细阅读官方文档发现，fit方法是用于从一个训练集中学习模型参数，其中就包括	了归一化时用到的均值，标准偏差。transform方法就是用于将模型用于位置数	据，fit_transform就很高效的将模型训练和转化合并到一起，训练样本先做	fit，得到mean，standard deviation，然后将这些参数用于transform（归	一化训练数据），使得到的训练数据是归一化的，而测试数据只需要在原先得到的	mean，std上来做归一化就行了，所以用transform就行了。
	```
	
2. CountVectorizer

	~~~
	from sklearn.feature_extraction.text import CountVectorizer

	# 下面是一个文本文档的列表

	text = ["The quick brown fox jumped over the lazy dog."]

	# 实例化 CountVectorizer 类

	vectorizer = CountVectorizer()

	# 标记并建立索引

	vectorizer.fit(text)

	# 查看结果

	print(vectorizer.vocabulary_)

	# 编码文档

	vector = vectorizer.transform(text)

	# 查看编码后的向量

	print(vector.shape)

	print(type(vector))

	print(vector.toarray())
	~~~

1. 