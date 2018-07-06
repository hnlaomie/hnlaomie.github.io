Title: python concurrency
Date: 2018-04-26 10:30
Category: programming
Tags: 201804, python 
Author: laomie
Summary: python concurrency 

python多线程多进程
================
因为GIL的原因，多线程只能并发的跑，不能并行，多进程可多核并行
```python
import threading
threads = []
for i in range(4):
    thread = threading.Thread(target=task, args=(param,))
    thread.start()
    threads.append(thread)
for thread in threads:
    thread.join()
```
线程（进程）池
```python
from concurrent.futures import ThreadPoolExecutor
# from concurrent.futures import ProcessPoolExecutor

with ThreadPoolExecutor(max_workers=2) as executor:
    #task1 = executor.submit(someTask, (param1, param2))
    tasks = executor.map(someTask, params)
    results = [task for task in tasks]
```
using as_completed
```python
from concurrent.futures import as_completed
...
tasks = [executor.submit(checkStatus, (url)) for url in urls]
for result in as_completed(tasks)
    print(result)
```
chaining callbacks
```python
future = executor.submit(task, (param))
future.add_done_callback(task_done)
future.add_done_callback(second_task_done)
```
future object
```python
future_obj.result(timeout=None)
future_obj.running()
future_obj.cancel()
future_obj.exception()
future_obj.done()
```

Multiprocessing
====================
Multiprocessing pools 比 ProcessPoolExecutor 更强大
```python
from multiprocessing import Pool
with Pool(processes=2, maxtasksperchild=2) as p:
    p.apply(...)
    #p.apply_async(...)
    #p.map(...)
    #p.map_async(...)
    # return iterable
    #p.imap(...)
    # allow to submit a list of tuples(multi variables)
    #p.starmap(...)
    #p.starmap_async(...)
```
