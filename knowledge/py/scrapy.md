---
title: scrapy
date: 2022-08-02 11:23:51
categories: python #文章分类
tags: [python] #文章标签，可以一次添加多个标签
---

scrapy base...

<!-- more -->

### 爬虫入门知识

爬虫的过程，就是模仿浏览器的行为，往目标站点发送请求，接收服务器的响应数据，提取需要的信息，并进行保存的过程。
urllib 自带原生网络请求库
Python 为爬虫的实现提供了工具:requests、bs4
对于较小的爬虫需求，用 Requests 库，对于不太小的爬虫需求，用 Scrapy 框架，对于定制程度很高的需求，建议使用 Requests 库
如果是用 requests 或者 scrapy 爬虫发现有反爬措施，可以尝试 selenium

中文词云:wordcloud 和 jieba

#### requests

1. get 请求

```py
import requests

#参数
kw = {'wd':'美女'}

#请求头
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"
}

# params 接收一个字典或者字符串的查询参数，
# 字典类型自动转换为url编码，不需要urlencode()
response = requests.get(
    "http://www.baidu.com/s?",
    params = kw,
    headers = headers
)

# 查看响应内容，response.text 返回的是Unicode格式的数据
print (response.text)

# 查看响应内容，response.content返回的字节流数据
print (respones.content)

# 查看完整url地址
print (response.url)

# 查看响应头部字符编码
print (response.encoding)

# 查看响应码
print (response.status_code)
```

返回 xml 指定编码

```py
import  requests
response = requests.get("http://www.sina.com")
print(response.request.headers)
print(response.content.decode('utf-8'))
```

2. post 请求

```py
import requests

#测试请求地址
req_url = "https://httpbin.org/post"

#表单数据
formdata = {
    'username': 'test',
    'password': '123456',
}

#添加请求头
req_header = {
    'User-Agent':'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36',
}
#发起请求
response = requests.post(
    req_url,
    data = formdata,
    headers = req_header
)

print (response.text)
# 如果是json文件可以直接显示
#print (response.json())
```

#### BeautifulSoup

bs4
解析器要另外安装:html5lib,lxml
Beautiful Soup 支持 Python 标准库中的 HTML 解析器，还支持一些第三方的解析器，如果我们不安装它，则 Python 会使用 Python 默认的解析器，lxml 解析器更加强大，速度更快，推荐安装。

```py
BeautifulSoup(markup, “html.parser”) # 内置标准库
BeautifulSoup(markup, “lxml”) # html
BeautifulSoup(markup, [“lxml”, “xml”])BeautifulSoup(markup, “xml”) #xml
BeautifulSoup(markup, “html5lib”)
```

(官方文档)[https://beautifulsoup.readthedocs.io/zh_CN/latest/]

### scrapy 基本结构

- Scrapy Engine(引擎)：负责网络和数据调度
- Scheduler(调度器)：线程调度
- Downloader（下载器）：网络请求
- Spider（爬虫）：负责获取结构化的数据
- Pipeline(管道)：处理爬虫获取到的结构化的数据

#### 定义爬取的数据

继承 scrapy.Item

#### 编写提取数据的 Spider

#### 执行 Spider 获取数据

在继承 scrapy.Spider 的 parse 中对 scrapy.Item 的继承进行赋值

```py
import scrapy

from tutorial.items import DmozItem

class DmozSpider(scrapy.Spider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
    ]

    def parse(self, response):
        for sel in response.xpath('//ul/li'):
            item = DmozItem()
            item['title'] = sel.xpath('a/text()').extract()
            item['link'] = sel.xpath('a/@href').extract()
            item['desc'] = sel.xpath('text()').extract()
            yield item
```

#### 保存爬取到的数据

最简单存储爬取的数据的方式是使用 Feed exports:
采用 JSON 格式对爬取的数据进行序列化，生成 items.json 文件

```py
scrapy crawl dmoz -o items.json
```

如果需要对爬取到的 item 做更多更为复杂的操作，可以编写 Item Pipeline
类似于我们在创建项目时对 Item 做的，用于您编写自己的 tutorial/pipelines.py 也被创建
如果仅仅想要保存 item，您不需要实现任何的 pipeline

#### 初始化安装

```shell
py --version
py -m venv project-name #项目配置虚拟环境
project-name\Scripts\activate.bat #激活环境
py -m pip install Scrapy #下载安装Scrapy
scrapy startproject tutorial #建应用
scrapy crawl spider.name #运行爬虫
scrapy genspider mydomain mydomain.com #创建一个新的spider
scrapy -h #查看所有可用命令
```

#### 工具命令

1. 全局命令：

- startproject
- settings
- runspider
- shell
- fetch
- view
- version

2. 项目(Project-only)命令：

- crawl
- check
- list
- edit
- parse
- genspider
- deploy
- bench

#### 提取 Item

Selectors 选择器
xpath 参数

- /html/head/title: 选择 HTML 文档中&lt;\head>标签内的&lt;\title>元素
- /html/head/title/text(): 选择上面提到的&lt;\title>元素的文字
- //td: 选择所有的&lt;\td>元素
- //div[@class="mine"]: 选择所有具有 class="mine"属性的 div 元素
  Selector 基本方法
- xpath(): 传入 xpath 表达式，返回该表达式所对应的所有节点的 selector list 列表 。
- css(): 传入 CSS 表达式，返回该表达式所对应的所有节点的 selector list 列表.
- extract(): 序列化该节点为 unicode 字符串并返回 list。
- re(): 根据传入的正则表达式对数据进行提取，返回 unicode 字符串 list 列表。

#### question
