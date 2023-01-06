---
title: python爬虫
date: 2022-09-19 14:41:40
cover: https://freeimg.eu.org/i/2023/01/iwkv0a.png
tags: 
  - spider
  - Python
categories: python
description: 简单记录一下学习爬虫的知识。
---







## urllib库使用

​	urllib.request.urlopen() 模拟浏览器向服务器发送请求，在这里他返回的是一个HTTPResponse类型的
​	response 服务器返回的数据
​		response的数据类型是HttpResponse
​		字节‐‐>字符串
​				解码decode
​		字符串‐‐>字节
​				编码encode
​		read() 字节形式读取二进制 扩展：rede(5)返回前几个字节
​		readline() 读取一行
​		readlines() 一行一行读取 直至结束
​		getcode() 获取状态码		#如果是200，说明是对的
​		geturl() 获取url
​		getheaders() 获取headers
​	urllib.request.urlretrieve(url,filename)	#用来下载的
​	参数：url代表的是下载的路径，filename是文件的名字

#### 2.请求对象的定制

​	UA介绍：UserAgent中文名为用户代理，简称UA，它是一个特殊字符串头，使得服务器能够识别客户使用的操作系统及版本、CPU类型、浏览器及版本。浏览器内核、浏览器渲染引擎、浏览器语言、浏览器插件等
​	语法：request = urllib.request.Request()

#### 3.编解码

​	①get请求方式：urllib.parse.quote（）	他是把汉字变为unicode编码
​	如
​		import urllib.request
​		import urllib.parse
​		url = 'https://www.baidu.com/s?wd='
​		headers = {
​		'User‐Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like
​		Gecko) Chrome/74.0.3729.169 Safari/537.36'
​		}
​		url = url + urllib.parse.quote('小野')
​		request = urllib.request.Request(url=url,headers=headers)
​		response = urllib.request.urlopen(request)
​		print(response.read().decode('utf‐8'))

	②get请求方式：urllib.parse.urlencode（）
	他是把字典中的各个键值对转换为Unicode编码，并且中间用与连接
		import urllib.request
		import urllib.parse
		url = 'http://www.baidu.com/s?'
		data = {
		'name':'小刚',
		'sex':'男',
		}
		data = urllib.parse.urlencode(data)
		url = url + data
		print(url)
		headers = {
		'User‐Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like
		Gecko) Chrome/74.0.3729.169 Safari/537.36'
		}
		request = urllib.request.Request(url=url,headers=headers)
		response = urllib.request.urlopen(request)
		print(response.read().decode('utf‐8'))
	
	③post请求
	如
		eg:百度翻译
		import urllib.request
		import urllib.parse
		url = 'https://fanyi.baidu.com/sug'
		headers = {
			'user‐agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like
		Gecko) Chrome/74.0.3729.169 Safari/537.36'
		}
		keyword = input('请输入您要查询的单词')
		data = {
			'kw':keyword
		}
		data = urllib.parse.urlencode(data).encode('utf‐8')
		request = urllib.request.Request(url=url,headers=headers,data=data)
		response = urllib.request.urlopen(request)
		print(response.read().decode('utf‐8'))


	post和get区别？
	1：get请求方式的参数必须编码，参数是拼接到url后面，编码之后不需要调用encode方法
	2：post请求方式的参数必须编码，参数是放在请求对象定制的方法中，编码之后需要调用encode方法(即在urlencode后面加上encode)
	
	注意：将字符串转换为json对象，json.loads(str)
	
	详细请求百度翻译：在headers中，最重要的是cookie
		import urllib.request
		import urllib.parse
		url = 'https://fanyi.baidu.com/v2transapi'
		headers = {
			留个cookie就行}
		data = {
			'from': 'en',
			'to': 'zh',
			'query': 'you',
			'transtype': 'realtime',
			'simple_means_flag': '3',
			'sign': '269482.65435',
			'token': '2e0f1cb44414248f3a2b49fbad28bbd5',
			}
		#参数的编码
		data = urllib.parse.urlencode(data).encode('utf‐8')
		# 请求对象的定制
		request = urllib.request.Request(url=url,headers=headers,data=data)
		response = urllib.request.urlopen(request)
		# 请求之后返回的所有的数据
		content = response.read().decode('utf‐8')
		import json
		# loads将字符串转换为python对象
		obj = json.loads(content)
		# python对象转换为json字符串 ensure_ascii=False 忽略字符集编码
		s = json.dumps(obj,ensure_ascii=False)
		print(s)


		ajax的get请求
		案例：豆瓣电影
			import urllib.request
			import urllib.parse
			# 下载前10页数据
			# 下载的步骤：1.请求对象的定制 2.获取响应的数据 3.下载
			# 每执行一次返回一个request对象
			def create_request(page):
				base_url = 'https://movie.douban.com/j/chart/top_list?type=20&interval_id=100%3A90&action=&'
				headers = {'User‐Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML,like Gecko) Chrome/76.0.3809.100 Safari/537.36'
			}
				data={
					# 1 2 3 4
					# 0 20 40 60
					'start':(page‐1)*20,
					'limit':20
				}
				# data编码
				data = urllib.parse.urlencode(data)
				url = base_url + data
				request = urllib.request.Request(url=url,headers=headers)
				return request
			# 获取网页源码
			def get_content(request):
				response = urllib.request.urlopen(request)
				content = response.read().decode('utf‐8')
				return content
			def down_load(page,content):
				# with open（文件的名字，模式，编码）as fp:
				# fp.write(内容)
				with open('douban_'+str(page)+'.json','w',encoding='utf‐8')as fp:
					fp.write(content)
			if __name__ == '__main__':
				start_page = int(input('请输入起始页码'))
				end_page = int(input('请输入结束页码'))
				for page in range(start_page,end_page+1):
					request = create_request(page)
					content = get_content(request)
					down_load(page,content)


	下载文件时：可以使用with open() as 参数：
	他会自动关闭 

#### 4.异常

​	URLError\HTTPError
​	简介:1.HTTPError类是URLError类的子类
​	2.导入的包urllib.error.HTTPError urllib.error.URLError
​	3.http错误：http错误是针对浏览器无法连接到服务器而增加出来的错误提示。引导并告诉浏览者该页是哪里出
​	了问题。
​	4.通过urllib发送请求的时候，有可能会发送失败，这个时候如果想让你的代码更加的健壮，可以通过try‐except进行捕获异常，异常有两类，URLError\HTTPError

#### 5.cookie

​	请求数据里面的：referer：他是判断当前路径是不是由上一个路径进来的

#### 6.Handler处理器

​	为什么要学习handler？
​	urllib.request.urlopen(url)
​	不能定制请求头
​	urllib.request.Request(url,headers,data)
​	可以定制请求头

	Handler
	定制更高级的请求头（随着业务逻辑的复杂 请求对象的定制已经满足不了我们的需求（动态cookie和代理不能使用请求对象的定制）
	
	格式：
	#请求对象定制
	①request = urllib.request.Request(url=url,headers=headers)
	#获取hanlder对象
	②handler = urllib.request.ProxyHandler(proxies)
	#获取opener对象
	③opener = urllib.request.build_opener(handler)
	#调用open方法
	④response = opener.open(request)
	这个时候就可以去找代理ip来使用了
	
	eg:
	import urllib.request
	url = 'http://www.baidu.com'
	headers = {
	'User ‐ Agent': 'Mozilla / 5.0(Windows NT 10.0;Win64;x64) AppleWebKit / 537.36(KHTML,
	likeGecko) Chrome / 74.0.3729.169Safari / 537.36'
	}
	request = urllib.request.Request(url=url,headers=headers)
	handler = urllib.request.HTTPHandler()
	opener = urllib.request.build_opener(handler)
	response = opener.open(request)
	print(response.read().decode('utf‐8'))

#### 7.代理服务器

​	代码配置代理
​	①创建Reuqest对象
​	在创建ProxyHandler对象之前要写入proxies={'http':'ip地址:端口'}代理
​	②创建ProxyHandler对象
​	③用handler对象创建opener对象
​	④使用opener.open函数发送请求

	代理池
	可以写一个代理池proxies_pool=[{}，{}]
	用proxies = random.choice(proxies_pool)进行随机选择
	
	eg:
import urllib.request
url = 'http://www.baidu.com/s?wd=ip'
headers = {
'User ‐ Agent': 'Mozilla / 5.0(Windows NT 10.0;Win64;x64) AppleWebKit / 537.36(KHTML,
likeGecko) Chrome / 74.0.3729.169Safari / 537.36'
}
request = urllib.request.Request(url=url,headers=headers)
proxies = {'http':'117.141.155.244:53281'}
handler = urllib.request.ProxyHandler(proxies=proxies)
opener = urllib.request.build_opener(handler)
response = opener.open(request)
content = response.read().decode('utf‐8')
with open('daili.html','w',encoding='utf‐8')as fp:
fp.write(content)

## 解析

#### 方法一：xpath插件

​	使用：ctrl + shift + x
​	使用步骤
​	1.安装lxml库
​	pip install lxml ‐i https://pypi.douban.com/simple
​	2.导入lxml.etree
​	from lxml import etree
​	3.etree.parse() 解析本地文件
​	html_tree = etree.parse('XX.html')
​	4.etree.HTML() 服务器响应文件
​	html_tree = etree.HTML(response.read().decode('utf‐8')
​	4.html_tree.xpath(xpath路径)
​	注意：xpath的返回值是列表
​		同时xpath得到的对象是<Element html at 0x52e5c10>等，要将element转成能看懂的html内容，需要进行		先tostring，然后decode编码，代码如下：
​		from html.parser import HTMLParser
​		from lxml import etree, html
​			text = html_tree.xpath("//div[@class='pl2']")[0]
​			res1 = html.tostring(text)
​			res2 = HTMLParser().unescape(res1.decode('utf-8'))
​			print(res2)
​		等价于：
​			tree3 = html.tostring(html_tree[0],encoding='utf-8').decode('utf-8')
​			print(tree3)


	简单使用
		from lxml import etree
		# 第⼀种⽅式，直接在python代码中解析html字符串
		text = """
		<div>
		<ul>
		<li class="item-0"><a href="link1.html">first item</a></li>
		....
		</ul>
		</div>
		"""
		resp_html = etree.HTML(text)
	
		#第⼆种⽅式，读取⼀个html⽂件并解析
		html = etree.parse('./test.html', etree.HTMLParser())
		result = etree.tostring(html)
		print(result.decode('utf-8'))


	xpath基本语法：
	1.路径查询
		//：查找所有子孙节点，不考虑层级关系
		/ ：找直接子节点
	2.谓词查询
		div是前面的路径
		//div[@id]
		//div[@id="maincontent"]
	3.属性查询
		//@class	#查找名叫class的所有属性
		如//title[@lang]：选取所有拥有名为 lang 的属性的 title 元素。
		//title[@lang='eng']：选取所有 title 元素，且这些元素拥有值为 eng 的 lang 属性。
	4.模糊查询
		//div[contains(@id, "he")]
		//div[starts‐with(@id, "he")]
	5.内容查询
		//div/h1/text()
	6.逻辑运算
		//div[@id="head" and @class="s_down"]
		//title | //price
	
	方法二：jsonpath
		只能解析本地文件，不能解析服务器文件
		jsonpath的安装及使用方式：
	pip安装：
	pip install jsonpath
	jsonpath的使用：
	obj = json.load(open('json文件', 'r', encoding='utf‐8'))
	ret = jsonpath.jsonpath(obj, 'jsonpath语法')
	教程连接（http://blog.csdn.net/luxideyao/article/details/77802389）


	方法三：BeautifulSoup
	1.BeautifulSoup简称：
	bs4
	2.什么是BeatifulSoup？
	BeautifulSoup，和lxml一样，是一个html的解析器，主要功能也是解析和提取数据
	3.优缺点？
	缺点：效率没有lxml的效率高
	优点：接口设计人性化，使用方便
	
	安装以及创建
	1.安装
	pip install bs4 -i https://pypi.douban.com/simple
	2.导入
	from bs4 import BeautifulSoup
	3.创建对象
	服务器响应的文件生成对象
	soup = BeautifulSoup(response.read().decode(), 'lxml')
	本地文件生成对象
	soup = BeautifulSoup(open('1.html'), 'lxml')
	注意：默认打开文件的编码格式gbk所以需要指定打开编码格式
	
	节点定位
		1.根据标签名查找节点
			soup.a 【注】只能找到第一个a
				soup.a.name
				soup.a.attrs
		2.函数
			(1).find(返回一个对象)
				find('a')：只找到第一个a标签

## Selenium

​	1.什么是selenium？
​	（1）Selenium是一个用于Web应用程序测试的工具。
​	（2）Selenium 测试直接运行在浏览器中，就像真正的用户在操作一样。
​	（3）支持通过各种driver（FirfoxDriver，IternetExplorerDriver，OperaDriver，ChromeDriver）驱动
​	真实浏览器完成测试。
​	（4）selenium也是支持无界面浏览器操作的。

	2.为什么使用selenium？
	模拟浏览器功能，自动执行网页中的js代码，实现动态加载
	
	3.如何安装selenium？
	（1）操作谷歌浏览器驱动下载地址
	http://chromedriver.storage.googleapis.com/index.html
	（2）谷歌驱动和谷歌浏览器版本之间的映射表
	http://blog.csdn.net/huilan_same/article/details/51896672
	（3）查看谷歌浏览器版本
	谷歌浏览器右上角‐‐>帮助‐‐>关于
	（4）pip install selenium
	
	4.selenium的使用步骤
	在这里使用的是3.6.0版本
	（1）导入：from selenium import webdriver
	（2）创建谷歌浏览器操作对象：
	path = 谷歌浏览器驱动文件路径
	browser = webdriver.Chrome(path)
	（3）访问网址
	url = 要访问的网址
	browser.get(url)
	
	5.元素定位，获取对象
	# 根据xpath语句来获取对象
	browser.find_elements_by_xpath('//input[@id="su"]')返回的是一个对象
	# 根据id来找到对象
	browser.find_element_by_id('id')
	
	6.访问元素信息，先获取对象
	获取元素属性
	.get_attribute('class')
	获取元素文本
	.text
	获取标签名
	.tag_name
	
	7.交互
	点击:click()
	输入:send_keys()
	右键:context_click()
	双击:double_click()
	后退操作:browser.back()
	前进操作:browser.forword()
	模拟JS滚动:
	js='document.documentElement.scrollTop=100000'
	browser.execute_script(js) 执行js代码
	获取网页代码：page_source
	退出：browser.quit()
	
	js语法
		选择器定位法
		1.定位
		  格式：js = 'return document.querySelector("selector")'
		  web.execute_script(js)
		返回值：定位到的浏览器元素(注：是js程序的返回值)
	
		2.获取属性值
		  格式：js = 'return document.querySelector("#kw").className'
		  web.execute_script(js)
		返回值：定位到的属性值(注：是js程序的返回值)
	
		3.获取文本
		  格式：js = 'return document.querySelector("#kw").innerText'
		  web.execute_script(js)
		返回值：定位到的文本(注：是js程序的返回值)
	
		4.执行事件
		格式：js = 'return document.querySelector("#kw").click()'
		返回值：None
		
		方法定位法
		id属性定位    return document.getElementById("kw")
		标签名定位    return document.getElementsByTagName("input")
	
		class属性定位    return document.getElementsByClassName("wd")
	
	模拟鼠标滚动页面
		1.滚动到页面底部
		格式：js = 'window.scrollTo(0,document.body.scrollHeight)'
		web.execute_script(js)
		返回值：None
	
		document表示浏览器窗口中的HTML文档
		window表示浏览器中打开的窗口
	
		2.滚动到指定元素位置
		格式：location = web.find_element_by_xpath('')
		      web.execute_script("arguments[0].scrollIntoView();", location)
		返回值None


​	
​	切换选型卡
​		1.查看选项卡句柄
​			web.current_window_handle：获取当前选型卡句柄
​			web.window_handle：获取所有选型卡句柄
​		2.切换选项卡
​			web.switch_to.window(web.window_handles[-1])
​		注意：这里面的索引是按照选项卡的打开顺序定义的，而不是根据选项卡的前后位置进行定义的
​	
​	iframe页面的处理
​		概述：
​			iframe是HTML中的标签，用于在网页中内嵌另一个网页，每个iframe里各自维护自己的全局window对象。
​			如果网页中嵌套的有iframe标签，则我们一开始创建的web对象无法操作iframe中的内容，因此我们必须对iframe页面进行单独的处理。
​		步骤：
​			先定位到需要操作的iframe元素位置；
​			进入需要操作的iframe框架中；
​			操作完成之后，需要退出。
​			web.switch_to.default_content()

#### 10.Phantomjs(已经倒闭了)

​	帮助selenium用的，提高效率
​	1.什么是Phantomjs？
​	（1）是一个无界面的浏览器
​	（2）支持页面元素查找，js的执行等
​	（3）由于不进行css和gui渲染，运行效率要比真实的浏览器要快很多

	2.如何使用Phantomjs？
	（1）获取PhantomJS.exe文件路径path
	（2）browser = webdriver.PhantomJS(path)
	（3）browser.get(url)
	扩展：保存屏幕快照:browser.save_screenshot('baidu.png')

#### 11.Chrome handless

​	帮助selenium用的，提高效率
​	Chrome-headless 模式， Google 针对 Chrome 浏览器 59版 新增加的一种模式，可以让你不打开UI界面的情况下
​	使用 Chrome 浏览器，所以运行效果与 Chrome 保持完美一致。

	2.配置：（写死的不用动）
	from selenium import webdriver
	from selenium.webdriver.chrome.options import Options
	chrome_options = Options()
	chrome_options.add_argument('‐‐headless')
	chrome_options.add_argument('‐‐disable‐gpu')
	# path是你自己的chrome浏览器的文件路径
	path = r'C:\Program Files (x86)\Google\Chrome\Application\chrome.exe'
	chrome_options.binary_location = path
	browser = webdriver.Chrome(chrome_options=chrome_options)
	browser.get('http://www.baidu.com/')

## requests

	s = requests.session
	
	requests通过with open的形式以二进制进行保存视频，图片等
	当遇到大文件的时候可以通过采用分块存入即可
	with open(r"c:\test.jpg", "wb") as f:
	for chunk in req.iter_content(chunk_size=1024):  # 每次加载1024字节
	    f.write(chunk)
	
	1.response的属性以及类型
		类型 ：models.Response
		r.text : 获取网站源码
		r.encoding ：访问或定制编码方式
		r.url ：获取请求的url
		r.content ：响应的字节类型
		r.status_code ：响应的状态码
		r.headers ：响应的头信息
	
	get请求
	requests.get(url,params=data,headers=headers)
	定制参数
	参数使用params传递
	参数无需urlencode编码
	不需要请求对象的定制
	请求资源路径中？可加可不加
	
	post请求
	requests.post(url = post_url,headers=headers,data=data)
	# 总结：
	# （1）post请求 是不需要编解码
	# （2）post请求的参数是data
	# （3）不需要请求对象的定制
	
	get和post区别？
		1： get请求的参数名字是params post请求的参数的名字是data
		2： 请求资源路径后面可以不加?
		3： 不需要手动编解码
		4： 不需要做请求对象的定制


	代理
		proxy定制
			在请求中设置proxies参数
			参数类型是一个字典类型
		eg:
			import requests
			url = 'http://www.baidu.com/s?'
			headers = {
				'user‐agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML,like Gecko) Chrome/65.0.3325.181 Safari/537.36'
			}
			data = {
				'wd':'ip'
			}
			proxy = {
				'http':'219.149.59.250:9797'
			}
			r = requests.get(url=url,params=data,headers=headers,proxies=proxy)
			with open('proxy.html','w',encoding='utf‐8') as fp:
				fp.write(r.text)

## scrapy

​	scrapy是什么？
​	Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架。 可以应用在包括数据挖掘，信息处理或存储历史数据等一系列的程序中。

	安装scrapy：pip install scrapy


	scrapy项目的创建以及运行
		创建scrapy项目：
			终端输入 scrapy startproject 项目名称
	
	项目组成：
		spiders
		__init__.py
		自定义的爬虫文件.py 			‐‐‐》由我们自己创建，是实现爬虫核心功能的文件
		__init__.py
		items.py 					‐‐‐》定义数据结构的地方，是一个继承自scrapy.Item的类
		middlewares.py 				‐‐‐》中间件 代理
		pipelines.py 				‐‐‐》管道文件，里面只有一个类，用于处理下载数据的后续处理，默认是300优先级，值越小优先级越高（1‐1000）
		settings.py 				‐‐‐》配置文件 比如：是否遵守robots协议，User‐Agent定义等
	
	3.创建爬虫文件：
		（1）跳转到spiders文件夹 cd 目录名字/目录名字/spiders
		（2）scrapy genspider 爬虫名字 网页的域名
	爬虫文件的基本组成：
			继承scrapy.Spider类
				name = 'baidu' 			‐‐‐》 运行爬虫文件时使用的名字
				allowed_domains 		‐‐‐》 爬虫允许的域名，在爬取的时候，如果不是此域名之下的url，会被过滤掉
	start_urls 							‐‐‐》 声明了爬虫的起始地址，可以写多个url，一般是一个
	parse(self, response) 				‐‐‐》解析数据的回调函数
		response.text 						‐‐‐》响应的是字符串
		response.body 						‐‐‐》响应的是二进制文件
		response.xpath()					‐》xpath方法的返回值类型是selector列表
		extract() 							‐‐‐》提取的是selector对象的是data
		extract_first() 					‐‐‐》提取的是selector列表中的第一个数据
	
	4.运行爬虫文件：
		scrapy crawl 爬虫名称
		注意：应在spiders文件夹内执行
	
	5. scrapy项目的结构
	项目名字
	    项目名字
	        spiders文件夹 （存储的是爬虫文件）
	            init
	            自定义的爬虫文件    核心功能文件  ****************
	        init
	        items        定义数据结构的地方 爬取的数据都包含哪些
	        middleware   中间件    代理
	        pipelines    管道   用来处理下载的数据
	        settings     配置文件    robots协议  ua定义等
	
	6. response的属性和方法
	    response.text   获取的是响应的字符串
	    response.body   获取的是二进制数据
	    response.xpath  可以直接是xpath方法来解析response中的内容
	    response.extract()   提取seletor对象的data属性值
	    response.extract_first() 提取的seletor列表的第一个数据


14.scrapy shell
	一个调试工具
	1.什么是scrapy shell？
	Scrapy终端，是一个交互终端，供您在未启动spider的情况下尝试及调试您的爬取代码。 其本意是用来测试提取
	数据的代码，不过您可以将其作为正常的Python终端，在上面测试任何的Python代码。
	该终端是用来测试XPath或CSS表达式，查看他们的工作方式及从爬取的网页中提取的数据。 在编写您的spider时，该
	终端提供了交互性测试您的表达式代码的功能，免去了每次修改后运行spider的麻烦。
	一旦熟悉了Scrapy终端后，您会发现其在开发和调试spider时发挥的巨大作用。

	使用：# 进入到scrapy shell的终端  直接在window的终端中输入scrapy shell 域名
	# 如果想看到一些高亮 或者 自动补全  那么可以安装ipython  pip install ipython
	# scrapy shell www.baidu.com
	
	yield(会交给管道)
	简要理解：yield就是 return 返回一个值，并且记住这个返回的位置，下次迭代就从这个位置后(下一行)开始
	
	使用：
	在框架里面的items进行传入参数
	如果使用管道下载，需要先去setting里面是开启管道，同时也可以开启多个管道进行下载，在pipelines中再创建一个类，同时去开通管道即可


15.数据存入数据库
	（1）settings配置参数：
	DB_HOST = '192.168.231.128'
	DB_PORT = 3306
	DB_USER = 'root'
	DB_PASSWORD = '1234'
	DB_NAME = 'test'
	DB_CHARSET = 'utf8'


	（2）管道配置
	from scrapy.utils.project import get_project_settings
	import pymysql
	class MysqlPipeline(object):
	#__init__方法和open_spider的作用是一样的
	#init是获取settings中的连接参数
	def __init__(self):
	settings = get_project_settings()
	self.host = settings['DB_HOST']
	self.port = settings['DB_PORT']
	self.user = settings['DB_USER']
	self.pwd = settings['DB_PWD']
	self.name = settings['DB_NAME']
	self.charset = settings['DB_CHARSET']
	self.connect()
	# 连接数据库并且获取cursor对象
	def connect(self):
	self.conn = pymysql.connect(host=self.host,
	port=self.port,
	user=self.user,
	password=self.pwd,
	db=self.name,
	charset=self.charset)
	self.cursor = self.conn.cursor()
	def process_item(self, item, spider):
	sql = 'insert into book(image_url, book_name, author, info) values("%s",
	"%s", "%s", "%s")' % (item['image_url'], item['book_name'], item['author'], item['info'])
	sql = 'insert into book(image_url,book_name,author,info) values
	("{}","{}","{}","{}")'.format(item['image_url'], item['book_name'], item['author'],
	item['info'])
	# 执行sql语句
	self.cursor.execute(sql)
	self.conn.commit()
	return item
	def close_spider(self, spider):
	self.conn.close()
	self.cursor.close()


16.日志信息和日志等级
	在配置文件中 settings.py
	LOG_FILE : 将屏幕显示的信息全部记录到文件中，屏幕不再显示，注意文件后缀一定是.log
	LOG_LEVEL : 设置日志显示的等级，就是显示哪些，不显示哪些















​	
