---
layout: post
title: FastAPI and Celery
date: 2024-11-10 21:08 +0800
---

1. https://testdriven.io/blog/fastapi-and-celery/
1. FastAPI有内置的[Starlette](https://github.com/encode/starlette/blob/0.26.1/starlette/background.py)
1. [Flower](https://flower.readthedocs.io/en/latest/),Flower is an open-source web application for monitoring and managing Celery clusters. It provides real-time information about the status of Celery workers and tasks.


Steps:
1. `brew install redis`
1. `brew services start redis`
1. `redis-cli keys "*"`
1. `celery -A worker.celery worker --loglevel=info --logfile=logs/celery.log --pool threads`
1. `celery --broker=redis://localhost:6379/0 flower --port=5555`
1. Go to http://0.0.0.0:5555/ to check dashboard, powered by flower.
1. 不能直接定义async celery task,[Celery不支持asyncio](https://github.com/celery/celery/issues/6552),  可以通过函数调用的方式在普通函数里面创建async任务.
    1. Mac上启动的时候一定要加`--pool threads`参数：`celery -A worker.celery worker  --loglevel=info --logfile=logs/celery.log --pool threads`, 要不然会task会莫名退出。[stackoverflow](https://github.com/celery/celery/issues/7007#issuecomment-2095237937)
    1. 用下面的代码做async封装:
       ~~~python
       @celery.task(name="html")
        def html_async(source_name, data_cls="HotUndergraduateProgramData"):
            print(celery.conf)
            print(f"当前工作目录是: {os.getcwd()}")
            
            async def asyncfunc():
                s = Scrape(source_name, data_cls, max_concurrent_tasks=50)
                await s.html()

            asyncio.run(asyncfunc())

            #loop = asyncio.get_event_loop()
            #loop.run_until_complete(asyncfunc())

            # run_coroutine_sync(asyncfunc(), 1000)
            return {"status":"Done"}
       ~~~
    
1. `attempted relative import with no known parent package`
    1. 从`worker.py`里面引用`main.py`,循环引用了。
    1. `ModuleNotFoundError: No module named 'tasks'`, 子包也引用不了，所以不是循环引用的问题.
    1. 原因找到了，把import从方法体里面抽到文件最上面就可以了。但是循环引用的问题需要另外解决。
1. `billiard.exceptions.WorkerLostError: Worker exited prematurely: signal 5`, 代码抛错了，先用命令行把抛错的问题修复，然后再跑job. 或者在job里面`try catch`.

sample task file

~~~python
# worker.py
import os
import time

from celery import Celery


celery = Celery(__name__)
celery.conf.broker_url = os.environ.get("CELERY_BROKER_URL", "redis://localhost:6379")
celery.conf.result_backend = os.environ.get("CELERY_RESULT_BACKEND", "redis://localhost:6379")


@celery.task(name="create_task")
def create_task(task_type):
    time.sleep(int(task_type) * 10)
    return True

~~~