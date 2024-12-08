---
layout: post
title: 赶鸭子上架LLM微调 Hello World
date: 2024-12-02 20:00 +0800
excerpt: "本教程主要是想给那些想条件有限，知识储备有限，又想尝试LLM微调的同学分享一个入门的教程，一系列的细节坑怎么绕过去."
---
你是不是也非常想微调自己的模型？手头是不是没有强大的显卡？又希望在本地训练？

如果你非常想自己fine tune 一个小模型，手头又只有低配M2, 或者一张低配显卡, 不知道如何入门，不要急。这篇文章手把手教你微调一个属于自己的LLM.

本篇文章的目的就是想方设法屏蔽一切困难，让你顺利做完模型微调的hello world, 走上人生巅峰。只要你有一台M2或者一张11G的显卡即可。

# 步骤

先上实操作，我们按照junyao的嬛嬛训练教程https://junyao.tech/posts/e45a9231.html , 一步一步地操作.

注意，按照他的教程，容易遇到显存不足的问题.另外一个问题是device map在M2上容易抛错，直接改成MPS.

1. 下载代码: `wget --no-check-certificate https://files.junyao.tech/uPic/llama3-ft.zip`
1. `mkdir llama3-ft`
1. `mv llama3-ft.zip llama3-ft`
1. `cd llama3-ft`
1. `unzip llama3-ft.zip`
2. 搭建Anaconda:
   1. `wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2024.06-1-Linux-x86_64.sh`
   2. `chmod +x Anaconda3-2024.06-1-Linux-86_64.sh`
   3. `./Anaconda3-2024.06-1-Linux-86_64.sh`
   4. `conda init`
   5. `conda activate`
3. `conda create -n llama3-ft python=3.10`
4. `conda activate llama3-ft`
5. `pip install -r requirements.txt`
6. (optional), 如果是M2, 跑的时候可能会遇到`Cannot copy out of meta tensor; no data`
   ~~~sh
    NotImplementedError:Cannot copy out of meta tensor; no data! Please use torch.nn.Module.to_empty() instead of torch.nn.Module.to() when moving module from meta to a different device.
   ~~~

   解决：强制设置 device = “mps”

   ~~~python
    # 检查CUDA是否可用，然后检查MPS是否可用，最后回退到CPU
    # device = torch.device("cuda" if torch.cuda.is_available() else "mps" if torch.backends.mps.is_available() else "cpu")
    device = "mps"
   ~~~
7. `python train.py`
   1. 代码会开始自动下载模型。
   2. 微调完成后，所有的文件会保存在 models 文件夹下面，结构如下：
      ~~~sh
      ├── models
        ├── checkpoint #【模型微调的 checkpoint】
        │ ├── LLM-Research
        │ │ └── Meta-Llama-3-8B-Instruct
        │ │     ├── checkpoint-100
        │ │     ├── checkpoint-200
        │ │     ├── checkpoint-xxx
        │ └── qwen
        │     └── Qwen1.5-4B-Chat
        │         ├── checkpoint-100
        │         ├── checkpoint-200
        │         ├── checkpoint-xxx
        ├── lora   #【模型微调的 lora 文件】
        │ ├── LLM-Research
        │ │ └── Meta-Llama-3-8B-Instruct
        │ └── qwen
        │     └── Qwen1.5-4B-Chat
        └── model  #【自动下载的基座模型】
            ├── LLM-Research
            │ └── Meta-Llama-3-8B-Instruct
            └── qwen
                └── Qwen1___5-4B-Chat

      ~~~
8. 默认的模型还可能会遇到显存不够用的问题, 解决办法, 用qwen 4B小模型，如果还太大的话还可以用Qwen 1.8B小模型: 
   ~~~python
    # https://www.modelscope.cn/models/qwen/Qwen1.5-4B-Chat/summary
    model_id = 'qwen/Qwen1.5-4B-Chat'
   ~~~
9.  训练完成以后就可以启动聊天窗口聊天了: `streamlit run chat.py`


# 训练自己的数据

有的同学会在数据准备上苦恼，认为要准备几千条几万条数据才能微调模型。其实不是的。

哪怕只有一条数据也可以进行微调。 这里有一个只用一条数据进行微调的例子[[InternLM2]XTuner 微调 internlm2-chat-1_8b【书生·浦语大模型实战营第二期第四节笔记】](https://zhuanlan.zhihu.com/p/693147811), 它的做法是把这样一条数据重复10000次，然后再扔给微调器.

下面是修改后的数据生成代码, 摘自[[InternLM2]XTuner 微调 internlm2-chat-1_8b【书生·浦语大模型实战营第二期第四节笔记】](https://zhuanlan.zhihu.com/p/693147811):
~~~python

~~~

## 关于重复数据的影响.

提两个问题:
1. 如果只用一条记录, 能微调出效果吗？
2. 如果是一条记录重复10000次，副作用是什么？

不同数据集下使用微调, [CSDN原文](https://blog.csdn.net/qq_45652492/article/details/123379156):
1. 数据量少，但数据相似度非常高。在这种情况下，我们所做的只是修改最后几层或最终的softmax图层的输出类别。
2. 数据量少，数据相似度低。在这种情况下，我们可以冻结预训练模型的初始层（比如k层），并再次训练剩余的( n-k )层。由于新数据集的相似度较低，因此根据新数据集对较高层进行重新训练具有重要意义。
3. 数据量大，数据相似度低。在这种情况下，由于我们有一个大的数据集，我们的神经网络训练将会很有效。但是，由于我们的数据与用于训练我们的预训练模型的数据相比有很大不同。使用预训练模型进行的预测不会有效。因此，最好根据你的数据从头开始训练神经网络(Training from scatch )。
4. 数据量大，数据相似度高。这是理想情况。在这种情况下，预训练模型应该是最有效的。使用模型的最好方法是保留模型的体系结构和模型的初始权重。然后，我们可以使用在预先训练的模型中的权重来重新训练该模型。