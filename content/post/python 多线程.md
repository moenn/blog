---
title: python 多线程
---

在进行 IO 密集型任务时，比如编写网络爬虫、读写文件相关的脚本，使用多线程可以显著提高程序的运行速度。下面是一个线性执行任务的代码和相同功能但是引入多线程的代码，来比较它们一下性能差距。
### 原始代码:
``` python
import urllib.request
import time
import socket

def get_result(url):
	try:
		res = urllib.request.urlopen(url, timeout=3)
		page = res.read()
		print(url, len(page))
	# 捕捉连接超时异常
	except socket.timeout:
		print("time out\n")


if __name__ == "__main__":
	start = time.time()
	sites = [
	    'https://www.baidu.com',
	    'https://www.sogou.com/',
	    'https://www.taobao.com',
	    'https://www.jd.com/',
	    'https://www.mi.com/',
	    'http://www.sina.com.cn/',
	    'https://www.163.com/',
	    'https://36kr.com/',
	    'https://www.ifanr.com/',
	    'https://www.huxiu.com/'

	]

	for url in sites:
		get_result(url)

	end = time.time()
	print("cost {}".format(end - start))
```
上面代码的功能是依次访问 sites 列表中的 url, 并打印出该 url 和其页面的大小。 
执行结果为
```
https://www.baidu.com 227
https://www.sogou.com/ 24544
https://www.taobao.com 142988
https://www.jd.com/ 110767
https://www.mi.com/ 327937
http://www.sina.com.cn/ 569786
https://www.163.com/ 690008
https://36kr.com/ 217633
https://www.ifanr.com/ 114239
https://www.huxiu.com/ 120377
cost 53.14170527458191
[Finished in 54.1s]
```
在速度上，可以看到下载一共 10 个页面用时约为 50s，平均访问一个页面要 5 s 。 而且执行结果中打印 url 的顺序与 sites 列表中的 url 顺序相同。 上述脚本是阻塞的，即在完全处理完一个 url 之前不会处理另一个 url。 许多时间浪费在等待网络 IO 上。 
### 引入多线程后的代码
``` python
from concurrent.futures import ThreadPoolExecutor
import urllib.request
import socket 
import time

def get_result(url):
	try:
		res = urllib.request.urlopen(url, timeout=3)
		page = res.read()
		print(url, len(page))
	# 捕捉连接超时异常
	except socket.timeout:
		print("time out\n")

if __name__ == "__main__":
	start = time.time()
	sites = [
	    'https://www.bing.com',
	    'https://www.baidu.com',
	    'https://www.taobao.com',
	    'https://www.jd.com/',
	    'https://www.mi.com/',
	    'http://www.sina.com.cn/',
	    'https://www.163.com/',
	    'https://36kr.com/',
	    'https://www.ifanr.com/',
	    'https://www.huxiu.com/'

	]
	with ThreadPoolExecutor() as executor:
		executor.map(get_result, sites)

	end = time.time()
	print("cost {}".format(end - start))
```
仅额外添加了 3 行代码，在下文解释。
执行结果为:
```
https://www.jd.com/ 110760
https://www.baidu.com 227
https://www.mi.com/ 326409
https://www.taobao.com 143052
https://www.163.com/ 689031
https://www.bing.com 88268
https://www.ifanr.com/ 113891
https://www.huxiu.com/ 113625
https://36kr.com/ 215773
http://www.sina.com.cn/ 569323
cost 9.555423736572266
[Finished in 9.8s]
```
在速度上，现在用时约为 10s，平均 1s 就能下载一个页面，这个速度是能让人接受的。并且可以看到执行结果中打印 url 的顺序与 sites 列表中的 url 顺序不同。 因为上述代码是非阻塞的，在处理一个 url 时，如果程序在等待远程服务器响应，那么程序会去访问下一个 url。 如果有任一个 url 处理完毕，就会首先返回它的执行结果。 

``` python
from concurrent.futures import ThreadPoolExecutor
...
...
	with ThreadPoolExecutor() as executor:
		executor.map(get_result, sites)
```
多线程版本额外添加了上面 3 行代码，首先第一行导入`concurrent.futures`这个异步执行库, 第二行中的`ThreadPoolExecutor`是这个库提供的线程池执行器。使用它的 `map` 函数执行我们需要多线程的代码(即 `get_results()`函数)，然后这个执行器就会自动分配多个线程，并在多个线程间切换，这个切换过程是不用手动控制的。

总结: 使用`ThreadPoolExecutor.map(func, *iterables)` 可以非常简单地在代码中引入多线程，只需要你编写一个函数和创建一个可迭代对象。

### 相关链接
[concurrent.futures](https://docs.python.org/3/library/concurrent.futures.html)
