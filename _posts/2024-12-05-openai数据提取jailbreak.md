---
layout: post
title: openai数据提取jailbreak
date: 2024-12-05 19:03 +0800
excerpt: "利用openai提取数据的时候遇到jailbreak的问题,通过openai后台放宽限制解决了。"
---

在利用Azure OpenAI 提取信息的时候遇到jail break的错误：

~~~sh
openai.BadRequestError: Error code: 400 - {'error': {'message': "The response was filtered due to the prompt triggering Azure OpenAI's content management policy. Please modify your prompt and retry. To learn more about our content filtering policies please read our documentation: https://go.microsoft.com/fwlink/?linkid=2198766", 'type': None, 'param': 'prompt', 'code': 'content_filter', 'status': 400, 'innererror': {'code': 'ResponsibleAIPolicyViolation', 'content_filter_result': {'hate': {'filtered': False, 'severity': 'safe'}, 'jailbreak': {'filtered': True, 'detected': True}, 'self_harm': {'filtered': False, 'severity': 'safe'}, 'sexual': {'filtered': False, 'severity': 'safe'}, 'violence': {'filtered': False, 'severity': 'safe'}}}}}
~~~

这段明确定义了是jailbreak.

~~~
'jailbreak': {'filtered': True, 'detected': True}
~~~


下面是触发jail break的prompt:

~~~sh
kwargs:{'content': '------\n\nContact Information\n\n**Admissions Committee**\n- email: No description available\n- phone: No description available\n\n**Department of International Studies**\n- email: No description available\n- phone: No description available\n\n**address_or_location**\nNo description available', 'instruction': ('- Identify key contacts within the program, including admissions officers, program coordinators, or faculty members.',), 'category': 'undergraduate program', 'category_tag': 'UNDERGRADUATE_PROGRAM', 'university': 'Texas A&M University', 'degree': 'International Studies'}

~~~

一个比较明显的特征是phone, email里面的内容为"no description available"的时候就会触发这个jail break, 是azure openai的一个误判。


解决办法，在azure openai后台修改内容过滤设置，问题消失。