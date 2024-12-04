---
layout: post
title: openai api截断json数据的问题
date: 2024-12-04 11:04 +0800
excerpt: "如果openai推理出来的json太长，而客户端设置的token数量又太短，openai会截断json字符串，导致客户端没法正常解析返回结果。解决办法就是加大max token length."
---

# 问题描述
扔给openai一段文本，让它抽取特定的信息，然后以json格式返回。客户端收到数据后用pydantic模型解析json数据。

笔者遇到的问题是大部分时候能正常工作，有些情况下会遇到下面的错误:

~~~sh
    output = output_cls.parse_raw(function_call.arguments)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "pydantic/main.py", line 548, in pydantic.main.BaseModel.parse_raw
pydantic.error_wrappers.ValidationError: 1 validation error for ApplicationDeadlines
__root__
  Unterminated string starting at: line 1 column 12402 (char 12401) (type=value_error.jsondecode; msg=Unterminated string starting at; doc={"
~~~


# 原因

每次调用openai api的时候会设置一个参数 `max_tokens`, 指定最多返回多少token:

~~~python

    return AzureOpenAI( model=llm_config.model,
                        engine=llm_config.engine,
                        api_key=api_key,
                        api_version=llm_config.api_version,
                        azure_endpoint=llm_config.api_base,
                        temperature=llm_config.temperature if llm_config.temperature is not None else DEFAULT_TEMPERATURE,
                        max_tokens=llm_config.max_tokens, # 改这个参数, 增大这个值。 这个参数用于控制生成文本的长度。它指定了生成的文本序列中最多可以包含的令牌（tokens）数量。令牌可以是单词、标点符号、数字等文本单元的数字化表示。
                        **kwargs) 
~~~

# 解决方法
项目的代码这个参数设置为`1000`的时候比较容易触发json截断的问题，改成`3000`以后大部分json截断的问题消失了。


~~~python

    return AzureOpenAI( model=llm_config.model,
                        engine=llm_config.engine,
                        api_key=api_key,
                        api_version=llm_config.api_version,
                        azure_endpoint=llm_config.api_base,
                        temperature=llm_config.temperature if llm_config.temperature is not None else DEFAULT_TEMPERATURE,
                        max_tokens=3000, # 改这个参数, 增大这个值。 这个参数用于控制生成文本的长度。它指定了生成的文本序列中最多可以包含的令牌（tokens）数量。令牌可以是单词、标点符号、数字等文本单元的数字化表示。
                        **kwargs) 
~~~

