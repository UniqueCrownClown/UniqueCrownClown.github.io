---
title: django
date: 2022-08-02 11:23:51
categories: python #文章分类
tags: [python, django] #文章标签，可以一次添加多个标签
---

django base...

<!-- more -->

### 初始化脚本

```shell
py --version
py -m venv project-name #项目配置虚拟环境
project-name\Scripts\activate.bat #激活环境
py -m pip install Django #下载安装Django
django-admin --version # 验证安装的Django
django-admin startproject mysite # django项目实例需要的设置项集合，包括数据库配置，django配置和应用程序配置
cd mysite
python manage.py runserver # 测试启动开发服务器，项目环境配置
python manage.py startapp polls # 创建应用
python manage.py migrate # 数据迁移,项目初始化建表
# 注册应用，修改模型后
python manage.py makemigrations polls # makemigrations 检测你对模型文件的修改，并且把修改的部分储存为一次 迁移。
python manage.py sqlmigrate polls 0001 # sqlmigrate 命令接收一个迁移的名称，然后返回对应的 SQL，光看不动
python manage.py migrate # 重新执行创建新模型的表
python manage.py shell # 交互式 Python 命令行，尝试一下 Django 为你创建的各种 API，包括数据库API
py manage.py createsuperuser # 创建管理员账号
py manage.py check --deploy
```

### 创建目录分析

- 最外层的 mysite/ 根目录只是你项目的容器， 根目录名称对 Django 没有影响，你可以将它重命名为任何你喜欢的名称。
- manage.py: 一个让你用各种方式管理 Django 项目的命令行工具。你可以阅读 django-admin 和 manage.py 获取所有 manage.py 的细节。
  里面一层的 mysite/ 目录包含你的项目，它是一个纯 Python 包。它的名字就是当你引用它内部任何东西时需要用到的 Python 包名。 (比如 mysite.urls).
- mysite/\_\_init\_\_.py：一个空文件，告诉 Python 这个目录应该被认为是一个 Python 包。如果你是 Python 初学者，阅读官方文档中的 更多关于包的知识。
- mysite/settings.py：Django 项目的配置文件。如果你想知道这个文件是如何工作的，请查看 Django 配置 了解细节。
- mysite/urls.py：Django 项目的 URL 声明，就像你网站的“目录”。阅读 URL 调度器 文档来获取更多关于 URL 的内容。
- mysite/asgi.py：作为你的项目的运行在 ASGI 兼容的 Web 服务器上的入口。阅读 如何使用 ASGI 来部署 了解更多细节。
- mysite/wsgi.py：作为你的项目的运行在 WSGI 兼容的 Web 服务器上的入口。阅读 如何使用 WSGI 进行部署 了解更多细节。

### 开发步骤

1. 初始化项目
2. 创建应用
3. 数据库配置
4. 创建模型
5. 创建管理员账号
6. 向管理页面加入应用模型实体
7. 添加视图
8. 模板系统
9. 添加测试
10. 静态文件分发
11. 自定义后台页

### 拓展

1. asgi 和 wsgi
   - WSGI 是用于 Web 服务器、框架和应用程序之间兼容性的长期存在的 Python 标准。
   - Web 服务器网关接口（Python Web Server Gateway Interface，缩写为 WSGI）是为 Python 语言定义的 Web 服务器和 Web 应用程序或框架之间的一种简单而通用的接口。
   - Python 提供了一个内置的模块 wsgiref，该模块是 wsgi 规范的实现。
   - ASGI 被设计为 WSGI 的超集，即 ASGI 是兼容 WSGI 的。 ASGI 定义了两者之间的转换方式，允许 WSGI 应用程序通过转换包装器（在 asgiref 库中提供）在 ASGI 服务器内运行。
   - 关于 WSGI 和 ASGI 的区别是:WSGI 是基于 http 协议模式开发的,不支持 websocket,而 ASGI 的诞生解决了 python 中的 WSGI 不支持当前的 web 开发中的一些新的协议标准,同时 ASGI 支持原有模式和 Websocket 的扩展, 即 ASGI 是 WSGI 的扩展.
2. 项目和应用
   项目则是一个网站使用的配置和应用的集合。项目可以包含很多个应用。应用可以被很多个项目使用。
   在 Django 中，每一个应用都是一个 Python 包，并且遵循着相同的约定。Django 自带一个工具，可以帮你生成应用的基础目录结构，这样你就能专心写代码，而不是创建目录了。

3. settings.py
   文件头部的 INSTALLED_APPS 设置项。这里包括了会在你项目中启用的所有 Django 应用

- django.contrib.admin -- 管理员站点， 你很快就会使用它。
- django.contrib.auth -- 认证授权系统。
- django.contrib.contenttypes -- 内容类型框架。
- django.contrib.sessions -- 会话框架。
- django.contrib.messages -- 消息框架。
- django.contrib.staticfiles -- 管理静态文件的框架。
  这些应用被默认启用是为了给常规项目提供方便。

默认开启的某些应用需要至少一个数据表，所以，在使用他们之前需要在数据库中创建一些表。

如果你不需要某个或某些应用，你可以在运行 migrate 前毫无顾虑地从 INSTALLED_APPS 里注释或者删除掉它们。 migrate 命令只会为在 INSTALLED_APPS 里声明了的应用进行数据库迁移。 4. django 应用是可插拔的，需要注册到项目上，在文件 mysite/settings.py 中 INSTALLED_APPS 子项添加点式路径后('polls.apps.PollsConfig')

5. 一个实现自增的请求同时触发导致少算次数的问题[使用 F()避免竞争条件](https://docs.djangoproject.com/zh-hans/4.1/ref/models/expressions/#avoiding-race-conditions-using-f)

6. Django 的主要部署平台是 WSGI，它是 Web 服务器和 Web 应用的 Python 标准。
   [wsgi 部署](https://docs.djangoproject.com/zh-hans/4.1/howto/deployment/wsgi/)

- 如何使用 Gunicorn 托管 Django
- 如何用 uWSGI 托管 Django
- 如何使用 Apache 和 mod_wsgi 托管 Django
- 如何从 Apache 对 Django 的用户数据库进行认证

7. 打包工具 [setuptools](https://pypi.org/project/setuptools/)

8. 将你的包发布至公共仓库，比如 [the Python Package Index (PyPI)](https://pypi.org/)

9. 前后端分离模块 rest_framework(drf)

- 调用链 urls.py-->views.py--models + serializer
- APIView 组件 from rest_framework.views import APIView

  ```py
  class ArticleDetail(APIView):
    """文章详情视图"""

    def get_object(self, pk):
        """获取单个文章对象"""
        try:
            # pk 即主键，默认状态下就是 id
            return Article.objects.get(pk=pk)
        except:
            raise Http404

    def get(self, request, pk):
        article = self.get_object(pk)
        serializer = ArticleDetailSerializer(article)
        # 返回 Json 数据
        return Response(serializer.data)
  ```

- Request from rest_framework.request import Request
- serializers from rest_framework import serializers
- ModelSerializer serializers.ModelSerializer

  ```py
  class ArticleDetailSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = '__all__'
  ```

10. QuerySet 对象
    通常做查询操作的时候都是通过"模型名字.objects"的方式进行操作的

- QuerySet 对象方法，可链式调用
- filter(condition)
- get(pk=1)
- all()
- values(\*tableColumnName)
- join()反向查询
- values_list() 类似于 values()方法，只不过返回的 QuerySet 对象中存储的不是字典而是元组
- exclude()
- annotate()
- order_by()
- defer()
- only()

11. class Meta:
    在模型中添加 class Meta 是完全可选的，所有选项都不是必须的。

- abstract--抽象类
- db_table--重写数据表名称
- managed--管理表的创建、删除
- ordering--排序
- unique_together--添加 unique
- index_together--添加索引
- verbose_name--模型类的单数名称
- verbose_name_plural--模型类的复数名称

12. django.contrib.auth 模块

- authenticate()，用户认证。如果认证信息有效，会返回一个 User 对象。无效返回 None

```py
from django.contrib.auth import authenticate
user = authenticate(username='john', password='secret')
if user is not None:
    # A backend authenticated the credentials
else:
    # No backend authenticated the credentials
```

限制非登录用户访问页面

```py
from django.conf import settings
from django.shortcuts import redirect

def my_view(request):
    if not request.user.is_authenticated:
        return redirect('%s?next=%s' % (settings.LOGIN_URL, request.path))
    # ...
```

- user.set_password(new_password)，修改用户密码
- django.contrib.auth.login(HttpRequest, user)，结合 django.contrib.sessions 模块，给已认证的用户附加上 session id 等信息。
- django.contrib.auth.logout(request)，注销用户
- @login_required(redirect_field_name='next', login_url=None)修饰器,会先通过 session key 检查是否登录, 已登录用户可以正常的执行操作, 未登录用户将被重定向到 login_url 指定的位置。若未指定 login_url 参数, 则重定向到 settings.LOGIN_URL。
  user.has_perm, 检查用户是否拥有操作某个模型的权限:

```py
user.has_perm('blog.add_article')
user.has_perm('blog.change_article')
user.has_perm('blog.delete_article')
```

- django.contrib.auth.middleware.AuthenticationMiddleware，添加 currently-logged-in user 到 HttpRequest 对象的 user 属性。
- django.contrib.auth.context_processors.auth 将 auth 添加到 template 的上下文中(context)
- django.contrib.auth.password_validation 中提供了 一系列的密码强度校验。
  注意：这个检验只针对命令行操作 createsuperuser、changepassword 等 ，对 model 的操作，比如 User.objects.create_user()、create_superuser()等无效（因为这些是开发者的操作，而不是用户的操作）

13. django--通用视图系统
14. django 测试模块
    [django.test.Client](https://docs.djangoproject.com/zh-hans/4.1/topics/testing/tools/#django.test.Client)

- 对于每个模型和视图都建立单独的 TestClass
- 每个测试方法只测试一个功能
- 给每个测试方法起个能描述其功能的名字

15. django 用户认证
