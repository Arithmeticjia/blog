---
title: Django个人博客搭建教程---REST风格API
date: 2019-11-24 16:03:17
tags: [django, restframework]
category: Django
---
Django Rest Framework 是一个强大且灵活的工具包，用以构建Web API
Django REST Framework可以在Django的基础上迅速实现API，并且自身还带有WEB的测试页面，可以方便的测试自己的API
<!--more-->
### 一、安装依赖
`pip3 install djangorestframework`
### 二、在settings.py中添加应用
```python
INSTALLED_APPS = [
    ....
 
'rest_framework',    # 加上这句，加在api的前面
    ....             # 后面是你的应用名称
]
```
### 三、路由注册
子urls.py
```python
from django.conf.urls import url,include
from JiaBlog import views
from rest_framework import routers
 
app_name = 'JiaBlog'
 
# 定义路由地址
route = routers.DefaultRouter()
 
# 注册新的路由地址
route.register(r'getarticleinfo' , views.GetArticleInfo)
 
urlpatterns = [
    ...
    url('api/', include(route.urls)),
]
```
主urls.py
```python
"""MyBlog URL Configuration
The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/2.1/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import include
from django.conf.urls import url
from django.urls import path
from django.conf import settings
from MyBlog.settings import MEDIA_ROOT
from django.conf.urls.static import static
from JiaBlog import views
from haystack.views import SearchView
from django.views.static import serve
#from django.views import static
 
urlpatterns = [
    url(r'^$', views.blog_index),
    path('admin/', admin.site.urls),
    path('JiaBlog/', include('JiaBlog.urls', namespace="JiaBlog")),
]+static(settings.MEDIA_URL,document_root=settings.MEDIA_ROOT)
```
### 四、视图函数
```python
from rest_framework import serializers,viewsets
from JiaBlog.models import Articles
# Create your views here.
 
 
class ArticlesSerializers(serializers.ModelSerializer):
    class Meta:
        model = Articles                        # 指定的模型类
        fields = ('id', 'title', 'body', 'timestamp','authorname','views','tags','category')  # 需要序列化的属性
 
 
class GetArticleInfo(viewsets.ModelViewSet):
    queryset = Articles.objects.all().order_by('-id')
    serializer_class = ArticlesSerializers
```
### 五、数据模型
```python
class Articles(models.Model):
    id = models.AutoField(primary_key=True)                         # id
    title = models.CharField(max_length = 150)                      # 博客标题
    body = models.TextField()                                       # 博客正文
    timestamp = models.DateTimeField()                              # 创建时间
    authorname = models.ForeignKey('JiaBlog.BlogUser',on_delete=models.CASCADE)        # 作者姓名
    views = models.PositiveIntegerField(default=0)
    category = models.ForeignKey(Category,on_delete=models.CASCADE,primary_key=False)
    tags = models.ManyToManyField(Tag, blank=True,null=True)
    greats = models.PositiveIntegerField(default=0)
    comments = models.IntegerField(default=0)
    status = models.CharField(max_length=20, default="DEL")
    brief = models.CharField(max_length=200,blank=True,null=True)
    pic = models.ImageField(upload_to='jiablogimages')
    # bodypic = models.ImageField(upload_to='jiablogimages',blank=True,null=True)
    istop = models.CharField(max_length=5,default='',null=True,blank=True)
    articlebodybrief = models.TextField(blank=True,null=True)
    last_edit_timestamp = models.DateTimeField(auto_now=True,verbose_name="更新时间",editable=True)
```
### 效果
```
GET /JiaBlog/api/getarticleinfo/
HTTP 200 OK
Allow: GET, POST, HEAD, OPTIONS
Content-Type: application/json
Vary: Accept
 
[
{
        "id": 3,
        "title": "python中的GIL锁",
        "body": "为什么我们说python中无法实现真正的多线程呢，这是因为在C语言写的python解释器中存在全局解释器锁，由于全局解释器锁的存在，在同一时间内，python解释器只能运行一个线程的代码，这大大影响了python多线程的性能。而这个解释器锁由于历史原因，现在几乎无法消除。\r\n\r\npython GIL 之所以会影响多线程等性能，是因为在多线程的情况下，只有当线程获得了一个全局锁的时候，那么该线程的代码才能运行，而全局锁只有一个，所以使用python多线程，在同一时刻也只有一个线程在运行，因此在即使在多核的情况下也只能发挥出单核的性能。\r\n\r\n任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。\r\n\r\n对于有io操作的线程，当一个线程在做io操作的时候，因为io操作不需要cpu，所以，这个时候，python会释放python全局锁，这样其他需要运行的线程就会使用该锁。",
        "timestamp": "2019-02-21T22:01:52",
        "authorname": 1,
        "views": 2249,
        "tags": [
            3
        ],
        "category": 2
    },
    {
        "id": 2,
        "title": "Django和Flask的区别",
        "body": "##写在最前面：\r\npython web开发有很多常用的框架，主要包括Django，Flask，Tornado，Diesel，Cubes，Pulsar，Falcon，webpy，大家最熟知的还是Django和Flask，今天就来简单介绍一下Django和Flask的区别，本博客就是使用Django开发的，Flask不是很了解，因为没有用过，今天就借花献佛，简单介绍一下。\r\n\r\n##Flask\r\n\r\nFlask确实很“轻”，不愧是Micro Framework，从Django转向Flask的开发者一定会如此感慨，除非二者均为深入使用过\r\nFlask自由、灵活，可扩展性强，第三方库的选择面广，开发时可以结合自己最喜欢用的轮子，也能结合最流行最强大的Python库\r\n    入门简单，即便没有多少web开发经验，也能很快做出网站\r\n    非常适用于小型网站\r\n    非常适用于开发web服务的API\r\n    开发大型网站无压力，但代码架构需要自己设计，开发成本取决于开发者的能力和经验\r\n    各方面性能均等于或优于Django\r\n    Django自带的或第三方的好评如潮的功能，Flask上总会找到与之类似第三方库\r\n    Flask灵活开发，Python高手基本都会喜欢Flask，但对Django却可能褒贬不一\r\n    Flask与关系型数据库的配合使用不弱于Django，而其与NoSQL数据库的配合远远优于Django\r\n    Flask比Django更加Pythonic，与Python的philosophy更加吻合\r\n\r\n##Django\r\n\r\nDjango太重了，除了web框架，自带ORM和模板引擎，灵活和自由度不够高\r\n    Django能开发小应用，但总会有“杀鸡焉用牛刀”的感觉\r\n    Django的自带ORM非常优秀，综合评价略高于SQLAlchemy\r\n    Django自带的模板引擎简单好用，但其强大程度和综合评价略低于Jinja\r\n    Django自带ORM也使Django与关系型数据库耦合度过高，如果想使用MongoDB等NoSQL数据，需要选取合适的第三方库，且总感觉Django+SQL才是天生一对的搭配，Django+NoSQL砍掉了Django的半壁江山\r\n    Django目前支持Jinja等非官方模板引擎\r\n    Django自带的数据库管理app好评如潮\r\n    Django非常适合企业级网站的开发：快速、靠谱、稳定\r\n    Django成熟、稳定、完善，但相比于Flask，Django的整体生态相对封闭\r\n    Django是Python web框架的先驱，用户多，第三方库最丰富，最好的Python库，如果不能直接用到Django中，也一定能找到与之对应的移植\r\n    Django上手也比较容易，开发文档详细、完善，相关资料丰富",
        "timestamp": "2019-02-21T21:25:28",
        "authorname": 1,
        "views": 2599,
        "tags": [
            1,
            2
        ],
        "category": 1
    },
    {
        "id": 1,
        "title": "用Django写一个自己的博客网站",
        "body": "##写在最前面：\r\n想用Django写网站很久了，本地也建立过很多demo，对于整个框架的入门算是熟练了。用pycharm可 \r\n以很方便的新建一个Django工程。专业版的Pycharm是自带Django的，目前Diango的最新版本应该是2.1。\r\n\r\nIDE:Pycharm\r\npython版本：3.6\r\n操作系统：macOS Mojave\r\n\r\n![图片](/media/images/blog1body1.png)\r\n\r\n这是用Django2.1写的一个个人博客的展示，还有一个含有带登录、注册、登出的session控制的监控系统，博客本身内置下载、天气查询、Google在线翻译，还可以发送QQ邮件。\r\n下面先贴一下目录结构：\r\n\r\n![图片](/static/images/blog1body2.jpg)",
        "timestamp": "2019-02-21T21:20:23",
        "authorname": 1,
        "views": 2657,
        "tags": [
            1
        ],
        "category": 1
    }
]
```