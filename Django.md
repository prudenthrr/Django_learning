本课程网页链接：https://code.ziqiangxuetang.com/django/django-tutorial.html

​                              http://www.liujiangblog.com/

# 一、Django基础教程

## 1.什么是Django？

Django是一个由Python编写的开放源代码的Web应用框架。采用了MTV的框架模式，即模型M，模板T，视图V。

## 2.Django的特点

（1）拥有完善的ORM关系映射

（2）拥有一个强大的后台admin

（3）使用正则表达式匹配网址，传递到对应的视图函数

（4）拥有强大、易扩展的模板系统

## 3.Django的优点

（1）开发速度快。

（2）Django更安全。Django通过动态生成网页并通过模板向浏览器发送信息，隐藏了网站的源代码，并且很好得阻止了常见的安全错误。

## 4.Django的核心模块

（1）urls.py

​		网址的入口，关联到views.py文件中所对应的视图函数

（2）views.py

​		处理用户的发送请求。通过渲染templates中的一些网页来展示相应的内容

（4）models.py

​		与数据库操作有关

（5）forms.py

​		表单，输入框的生成。用户在浏览器上输入提交的数据后，对数据进行验证。

（6）admin.py

​		后台，可以使用少量的代码来管理

（7）setting.py

​		Django的配置文件，比如DEBUG 的开关，静态文件的位置等

（8）migrations文件夹

​        记录数据库模型中的数据变更

（9）templates文件夹

​		存放当前app的模板文件

（10）apps.py

​       用于应用程序的配置

## 5.Django的常用命令

搭建项目流程参考：https://blog.csdn.net/weixin_41644725/article/details/89287568

（1）创建项目名称： django-admin.py  startproject   [项目名称]

（2）创建项目APP：python manage.py startapp  [app名称]

（3）当在models.py文件中设置类后，可使用如下命令**生成数据库**

```bash
$ python manage.py makemigrations     *# 生成数据库模型*
$ python manage.py migrate     *#在数据库中生成数据表,迁移同步到数据库*
```

（4）运行开发服务器 ：python manage.py runserver

（5）创建超级管理员：python manage.py createsuperuser

（6）更多命令输入：python manage.py **可以看到详细的列表**



# 二、Django视图与网址

**路由的编写方式是Django2.0和1.11最大的区别所在**。Django官方迫于压力和同行的影响，不得不将原来的正则匹配表达式，改为更加简单的**path表达式**，但依然通过re_path()方法保持对1.x版本的兼容。

```python
from django.urls import path
from . import views
urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<slug:slug>/', views.article_detail),
]
```

```python
from django.urls import path, re_path

from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<slug>[\w-]+)/$', views.article_detail),
]
```



## 1.Django不同版本的url格式

Django 1.8.x :    url(r'^add/$', calc_views.add, name='add')

Django 2.0 :       path('add/', calc_views.add, name='add')

## 2.views.py中常用的方法

（1）render 是渲染模板， 例如：

```Python
return render(request, 'home.html')
```

（2）redirect是重定向网址，例如：

``` python
return redirect('/user_info/')
```

## 3.删除模板中硬编码的URLs

例如以下代码中的url使用硬编码：

```html
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```

它对于代码修改非常不利。设想如果在urls.py文件里修改了路由表达式，那么所有的模板中对该url的引用都需要修改，这是无法接受的！

前面对urls定义时设置了一个name别名，可以用url模板标签来解决这个问题。具体代码如下：

```html 
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

## 4.path转换器

默认情况下，Django内置下面的路径转换器：

- `str`：匹配任何非空字符串，但不含斜杠`/`，如果你没有专门指定转换器，那么这个是默认使用的；
- `int`：匹配0和正整数，返回一个int类型
- `slug`：可理解为注释、后缀、附属等概念，是url拖在最后的一部分解释性字符。该转换器匹配任何ASCII字符以及连接符和下划线，比如`building-your-1st-django-site`；
- `uuid`：匹配一个uuid格式的对象。为了防止冲突，规定必须使用破折号，所有字母必须小写，例如`075194d3-6885-417e-a8a8-6c931e272f00`。返回一个UUID对象；
- `path`：匹配任何非空字符串，重点是可以包含路径分隔符’/‘。这个转换器可以帮助你匹配整个url而不是一段一段的url字符串。**要区分path转换器和path()方法**。

## 5.Django内置的快捷方法

Django在`django.shortcuts`模块中，为我们提供了很多快捷方便的类和方法，它们都很重要，使用频率很高。

## 1. render()

render(request, template_name, context=None, content_type=None, status=None, using=None)[source]

结合一个给定的模板和一个给定的上下文字典，返回一个渲染后的HttpResponse对象。

**必需参数：**

- **request**：视图函数处理的当前请求，封装了请求头的所有数据，其实就是视图参数request。
- **template_name**：要使用的模板的完整名称或者模板名称的列表。如果是一个列表，将使用其中能够查找到的第一个模板。

**可选参数：**

- **context**：添加到模板上下文的一个数据字典。默认是一个空字典。可以将认可需要提供给模板的数据以字典的格式添加进去。这里有个小技巧，使用Python内置的locals()方法，可以方便的将函数作用于内的所有变量一次性添加。
- **content_type**：用于生成的文档的MIME类型。 默认为`DEFAULT_CONTENT_TYPE`设置的值。
- **status**：响应的状态代码。 默认为200。
- **using**：用于加载模板使用的模板引擎的NAME。

**范例：**

下面的例子将渲染模板`myapp/index.html`，MIME类型为`application/xhtml+xml`：

```python
from django.shortcuts import render

def my_view(request):
    # View code here...
    return render(request, 'myapp/index.html', {
        'foo': 'bar',
    }, content_type='application/xhtml+xml')
```

这个示例等同于：

```python
from django.http import HttpResponse
from django.template import loader

def my_view(request):
    # View code here...
    t = loader.get_template('myapp/index.html')
    c = {'foo': 'bar'}
    return HttpResponse(t.render(c, request), content_type='application/xhtml+xml')
```

## 2. render_to_response()

render_to_response(template_name, context=None, content_type=None, status=None, using=None)[source]

此功能在引入render()之前进行，不推荐，以后可能会被弃用。

## 3. redirect()

redirect(to, permanent=False, *args,* *kwargs)[source]

根据传递进来的url参数，返回HttpResponseRedirect。

参数to可以是：

- 一个模型：将调用模型的`get_absolute_url()`函数，反向解析出目的url；
- 视图名称：可能带有参数：reverse()将用于反向解析url；
- 一个绝对的或相对的URL：将原封不动的作为重定向的目标位置。

默认情况下是临时重定向，如果设置`permanent=True`将永久重定向。

**范例：**

1.调用对象的`get_absolute_url()`方法来重定向URL：

```python
from django.shortcuts import redirect

def my_view(request):
    ...
    object = MyModel.objects.get(...)
    return redirect(object)
```

2.传递视图名，使用reverse()方法反向解析url：

```python
def my_view(request):
    ...
    return redirect('some-view-name', foo='bar')
```

1. 重定向到硬编码的URL：

```python
def my_view(request):
    ...
    return redirect('/some/url/')
```

1. 重定向到一个完整的URL：

```python 
def my_view(request):
    ...
    return redirect('https://example.com/')
```

所有上述形式都接受permanent参数；如果设置为True，将返回永久重定向：

```python
def my_view(request):
    ...
    object = MyModel.objects.get(...)
    return redirect(object, permanent=True)
```

## 4. get_object_or_404()

get_object_or_404(klass, *args,* *kwargs)[source]

这个方法，非常有用，请一定熟记。常用于查询某个对象，找到了则进行下一步处理，如果未找到则给用户返回404页面。

在后台，Django其实是调用了模型管理器的get()方法，只会返回一个对象。不同的是，如果get()发生异常，会引发Http404异常，从而返回404页面，而不是模型的DoesNotExist异常。

**必需参数**：

- **klass**：要获取的对象的Model类名或者Queryset等；
- `**kwargs`:查询的参数，格式应该可以被get()接受。

**范例：**

1.从MyModel中使用主键1来获取对象：

```python
from django.shortcuts import get_object_or_404

def my_view(request):
    my_object = get_object_or_404(MyModel, pk=1)
```

这个示例等同于：

```python
from django.http import Http404

def my_view(request):
    try:
        my_object = MyModel.objects.get(pk=1)
    except MyModel.DoesNotExist:
        raise Http404("No MyModel matches the given query.")
```

2.除了传递Model名称，还可以传递一个QuerySet实例：

```python 
queryset = Book.objects.filter(title__startswith='M')
get_object_or_404(queryset, pk=1)
```

上面的示例不够简洁，因为它等同于：

```python
get_object_or_404(Book, title__startswith='M', pk=1)
```

但是如果你的queryset来自其它地方，它就会很有用了。

3.还可以使用Manager。 如果你自定义了管理器，这将很有用：

```python
get_object_or_404(Book.dahl_objects, title='Matilda')
```

4.还可以使用related managers：

```python
author = Author.objects.get(name='Roald Dahl')
get_object_or_404(author.book_set, title='Matilda')
```

与get()一样，如果找到多个对象将引发一个MultipleObjectsReturned异常。

## 5. get_list_or_404()

get_list_or_404(klass, *args,* *kwargs)[source]

这其实就是`get_object_or_404`多值获取版本。

在后台，返回一个给定模型管理器上filter()的结果，并将结果映射为一个列表，如果结果为空则弹出Http404异常。

**必需参数**：

- **klass**：获取该列表的一个Model、Manager或QuerySet实例。
- `**kwargs`：查询的参数，格式应该可以被filter()接受。

**范例：**

下面的示例从MyModel中获取所有发布出来的对象：

```python
from django.shortcuts import get_list_or_404

def my_view(request):
    my_objects = get_list_or_404(MyModel, published=True)
```

这个示例等同于：

```python
from django.http import Http404

def my_view(request):
    my_objects = list(MyModel.objects.filter(published=True))
    if not my_objects:
        raise Http404("No MyModel matches the given query.")
```



# 三、Django模板标签

模板文件（templates）一般位于新创建的APP目录下面，Django会自动去这个文件夹中找

## 1.Django模板查找机制

Django 查找模板的过程是在**每个** app 的 templates 文件夹中找，而不只是当前 app 中的templates 文件夹中找。各个 app 的 templates 形成一个文件夹列表，Django 遍历这个列表，一个个文件夹进行查找，当在某一个文件夹找到的时候就停止，所有的都遍历完了还找不到指定的模板的时候就是 Template Not Found （过程类似于Python找包）。

**例如：项目 zqxt 有两个 app，分别为 tutorial 和 tryit**

```python
zqxt
├── tutorial
│   ├── __init__.py
│   ├── admin.py
│   ├── models.py
│   ├── templates
│   │   └── tutorial
│   │       ├── index.html
│   │       └── search.html
│   ├── tests.py
│   └── views.py
├── tryit
│   ├── __init__.py
│   ├── admin.py
│   ├── models.py
│   ├── templates
│   │   └── tryit
│   │       ├── index.html
│   │       └── poll.html
│   ├── tests.py
│   └── views.py
├── manage.py
└── zqxt
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

这样，使用的时候，模板就是 "tutorial/index.html" 和 "tryit/index.html" 这样有app作为名称的一部分，就不会混淆。

## 2.Django的上下文渲染器(标签的使用)

如果需要将**一个或多个变量共享给多个网页或者所有网页使用**，这个可以使用 **Django 上下文渲染器**来做。

**简单总结一下：**一般的变量之类的用 {{ }}（变量），功能类的，比如循环，条件判断是用 {%  %}（标签）

#### （1）显示一个基本的字符串在网页上  

views.py

```python
from django.shortcuts import render
def home(request):
    s = u"我在自强学堂学习Django，用它来建网站"
    return render(request, 'home.html', {'string': s})
```

home.html

```html
{{ string }}
```

#### （2）基本的 for 循环  

views.py

```python
from django.shortcuts import render
def home(request):
    TutorialList = ["HTML", "CSS", "jQuery", "Python", "Django"]
    return render(request, 'home.html', {'TutorialList': TutorialList})
```

home.html

```html
{% for i in TutorialList %}
{{ i }}
{% endfor %}
```

#### （3）显示字典中的内容

views.py

```python
def home(request):
    info_dict = {'site': u'自强学堂', 'content': u'各种IT技术教程'}
    return render(request, 'home.html', {'info_dict': info_dict})
```

home.html

``` html
{% for key, value in info_dict.items %}
    {{ key }}: {{ value }}
{% endfor %}
```

#### （4）条件判断  

forloop.last判断是否是最后一个元素

```html
{% for item in List %}
    {{ item }}{% if not forloop.last %},{% endif %} 
{% endfor %}
```

for循环中其他的方法：

| 变量                  | 描述                                                 |
| --------------------- | ---------------------------------------------------- |
| `forloop.counter`     | 索引从 1 开始算                                      |
| `forloop.counter0`    | 索引从 0 开始算                                      |
| `forloop.revcounter`  | 索引从最大长度到 1                                   |
| `forloop.revcounter0` | 索引从最大长度到 0                                   |
| `forloop.first`       | 当遍历的元素为第一项时为真                           |
| `forloop.last`        | 当遍历的元素为最后一项时为真                         |
| `forloop.parentloop`  | 用在嵌套的 for 循环中，获取上一层 for 循环的 forloop |

当列表中可能为空值时用 {%  empty   %}

```html
<ul>
{% for athlete in athlete_list %}
    <li>{{ athlete.name }}</li>
{% empty %}
    <li>抱歉，列表为空</li>
{% endfor %}
</ul>
```

#### （5）**模板上得到视图对应的网址**

{%  url   '网址名称'   参数1   参数2   参数3...   %}

还可以使用 as 语句将内容取别名（相当于定义一个变量），多次使用（但视图名称到网址转换只进行了一次）

```html
{% url '网址名称' arg1 arg2 as the_url %}
<a href="{{ the_url }}">链接到：{{ the_url }}</a>
```

#### （6）逻辑操作

==, !=, >=, <=, >, < 这些比较都可以在模板中使用

```html
{% if var >= 90 %}
成绩优秀，自强学堂你没少去吧！学得不错
{% elif var >= 80 %}
成绩良好
{% elif var >= 70 %}
成绩一般
{% elif var >= 60 %}
需要努力
{% else %}
不及格啊，大哥！多去自强学堂学习啊！
{% endif %}
```

#### （7）获取当前用户

```html
{{ request.user }}
```

如果登陆就显示内容，不登陆就不显示内容：

```html
{% if request.user.is_authenticated %}
    {{ request.user.username }}，您好！
{% else %}
    请登陆，这里放登陆链接
{% endif %}
```

#### （8）获取当前网址

```html
{{ request.path }}
```

#### （9）获取当前GET参数

```html
{{ request.GET.urlencode }}
```

 **合并到一起用**

```html
<a href="{{ request.path }}?{{ request.GET.urlencode }}&delete=1">当前网址加参数 delete</a>
```

完整的内容参考官方文档：<https://docs.djangoproject.com/en/1.11/ref/templates/builtins/>

## 3.自定义错误页面

当Django找不到与请求匹配的URL时，或者当抛出一个异常时，将调用一个错误处理视图。Django默认的自带的错误视图包括400、403、404和500，分别表示请求错误、拒绝服务、页面不存在和服务器错误。它们分别位于：

- handler400 —— django.conf.urls.handler400。
- handler403 —— django.conf.urls.handler403。
- handler404 —— django.conf.urls.handler404。
- handler500 —— django.conf.urls.handler500。

这些值可以在根URLconf中设置。在其它app中的二级URLconf中设置这些变量无效。

Django有内置的HTML模版，用于返回错误页面给用户，但是这些403，404页面实在丑陋，通常我们都自定义错误页面。

首先，在根URLconf中额外增加下面的条目，并导入views模块：

```
from django.contrib import admin
from django.urls import path
from app import views

urlpatterns = [
    path('admin/', admin.site.urls),
]

# 增加的条目
handler400 = views.bad_request
handler403 = views.permission_denied
handler404 = views.page_not_found
handler500 = views.error
```

然后在，app/views.py文件中增加四个处理视图：

```
def bad_request(request):
    return render(request, '400.html')


def permission_denied(request):
    return render(request, '403.html')


def page_not_found(request):
    return render(request, '404.html')


def error(request):
    return render(request, '500.html')
```

再根据自己的需求，创建对应的400、403、404、500.html四个页面文件，就可以了（要注意好模板文件的引用方式，视图的放置位置等等）。



# 四、Django模型

Django 模型是与数据库相关的，与数据库相关的代码一般写在 models.py 中，Django 支持 sqlite3, MySQL, PostgreSQL等数据库，只需要在settings.py中配置即可。

## 1.继承models.Model

```python
class Person(models.Model):
```

下表列出了所有Django内置的字段类型，但不包括关系字段类型（字段名采用驼峰命名法，初学者请一定要注意）：

| 类型                       |                             说明                             |
| :------------------------- | :----------------------------------------------------------: |
| AutoField                  | 一个自动增加的整数类型字段。通常你不需要自己编写它，Django会自动帮你添加字段：`id = models.AutoField(primary_key=True)`，这是一个自增字段，从1开始计数。如果你非要自己设置主键，那么请务必将字段设置为`primary_key=True`。Django在一个模型中只允许有一个自增字段，并且该字段必须为主键！ |
| BigAutoField               | (1.10新增)64位整数类型自增字段，数字范围更大，从1到9223372036854775807 |
| BigIntegerField            | 64位整数字段（看清楚，非自增），类似IntegerField ，-9223372036854775808 到9223372036854775807。在Django的模板表单里体现为一个textinput标签。 |
| BinaryField                |               二进制数据类型。使用受限，少用。               |
| **BooleanField**           | 布尔值类型。默认值是None。在HTML表单中体现为CheckboxInput标签。如果要接收null值，请使用NullBooleanField。 |
| **CharField**              | 字符串类型。必须接收一个max_length参数，表示字符串长度不能超过该值。默认的表单标签是input text。最常用的filed，没有之一！ |
| CommaSeparatedIntegerField | 逗号分隔的整数类型。必须接收一个max_length参数。常用于表示较大的金额数目，例如1,000,000元。 |
| **DateField**              | `class DateField(auto_now=False, auto_now_add=False, **options)`日期类型。一个Python中的datetime.date的实例。在HTML中表现为TextInput标签。在admin后台中，Django会帮你自动添加一个JS的日历表和一个“Today”快捷方式，以及附加的日期合法性验证。两个重要参数：（参数互斥，不能共存） `auto_now`:每当对象被保存时将字段设为当前日期，常用于保存最后修改时间。`auto_now_add`：每当对象被创建时，设为当前日期，常用于保存创建日期(注意，它是不可修改的)。设置上面两个参数就相当于给field添加了`editable=False`和`blank=True`属性。如果想具有修改属性，请用default参数。例子：`pub_time = models.DateField(auto_now_add=True)`，自动添加发布时间。 |
| DateTimeField              | 日期时间类型。Python的datetime.datetime的实例。与DateField相比就是多了小时、分和秒的显示，其它功能、参数、用法、默认值等等都一样。 |
| DecimalField               | 固定精度的十进制小数。相当于Python的Decimal实例，必须提供两个指定的参数！参数`max_digits`：最大的位数，必须大于或等于小数点位数 。`decimal_places`：小数点位数，精度。 当`localize=False`时，它在HTML表现为NumberInput标签，否则是text类型。例子：储存最大不超过999，带有2位小数位精度的数，定义如下：`models.DecimalField(..., max_digits=5, decimal_places=2)`。 |
| DurationField              | 持续时间类型。存储一定期间的时间长度。类似Python中的timedelta。在不同的数据库实现中有不同的表示方法。常用于进行时间之间的加减运算。但是小心了，这里有坑，PostgreSQL等数据库之间有兼容性问题！ |
| **EmailField**             | 邮箱类型，默认max_length最大长度254位。使用这个字段的好处是，可以使用DJango内置的EmailValidator进行邮箱地址合法性验证。 |
| **FileField**              | `class FileField(upload_to=None, max_length=100, **options)`上传文件类型，后面单独介绍。 |
| FilePathField              |                  文件路径类型，后面单独介绍                  |
| FloatField                 |                   浮点数类型，参考整数类型                   |
| **ImageField**             |                   图像类型，后面单独介绍。                   |
| **IntegerField**           | 整数类型，最常用的字段之一。取值范围-2147483648到2147483647。在HTML中表现为NumberInput标签。 |
| **GenericIPAddressField**  | `class GenericIPAddressField(protocol='both', unpack_ipv4=False, **options)[source]`,IPV4或者IPV6地址，字符串形式，例如`192.0.2.30`或者`2a02:42fe::4`在HTML中表现为TextInput标签。参数`protocol`默认值为‘both’，可选‘IPv4’或者‘IPv6’，表示你的IP地址类型。 |
| NullBooleanField           |       类似布尔字段，只不过额外允许`NULL`作为选项之一。       |
| PositiveIntegerField       |              正整数字段，包含0,最大2147483647。              |
| PositiveSmallIntegerField  |                较小的正整数字段，从0到32767。                |
| SlugField                  | slug是一个新闻行业的术语。一个slug就是一个某种东西的简短标签，包含字母、数字、下划线或者连接线，通常用于URLs中。可以设置max_length参数，默认为50。 |
| SmallIntegerField          |                 小整数，包含-32768到32767。                  |
| **TextField**              | 大量文本内容，在HTML中表现为Textarea标签，最常用的字段类型之一！如果你为它设置一个max_length参数，那么在前端页面中会受到输入字符数量限制，然而在模型和数据库层面却不受影响。只有CharField才能同时作用于两者。 |
| TimeField                  | 时间字段，Python中datetime.time的实例。接收同DateField一样的参数，只作用于小时、分和秒。 |
| **URLField**               |      一个用于保存URL地址的字符串类型，默认最大长度200。      |
| **UUIDField**              | 用于保存通用唯一识别码（Universally Unique Identifier）的字段。使用Python的UUID类。在PostgreSQL数据库中保存为uuid类型，其它数据库中为char(32)。这个字段是自增主键的最佳替代品，后面有例子展示。 |



## 2.创建模型对象使用   

 **模型.objects.create(arg1='', arg2='',...)**

 或者使用：

```python
new_class = 模型（）       # 模型.objects.create(arg1='', arg2='',...)自动							  保存
new_class.arg1 = ''
new_class.save()
```

## 3.根据条件查询对象

```python
模型.objects.filter(arg1=变量)
```

## 4.查询所有的对象

```python
模型.objects.all()
```

## 5.模糊查询

Person为数据库模型，name为模型的一个属性

```python
Person.objects.filter(name__contains="abc")  # 名称中包含 "abc"的人
# 找出名称含有abc, 但是排除年龄是23岁的
Person.objects.filter(name__contains="abc").exclude(age=23)  
```

## 6.正则表达式查询

```Python
Person.objects.filter(name__regex="^abc")  # 正则表达式查询
```

## 7.查找对象不区分大小写

```python
Person.objects.filter(name__iexact="abc")  # 名称为 abc 但是不区分大小写，可以找到 ABC, Abc, aBC，这些都符合条件
```

## 8.删除符合条件的结果

```python
Person.objects.filter(name__contains="abc").delete() # 删除 名称中包含 "abc"的人
```

## 9.更新某个内容

```python
Person.objects.filter(name__contains="abc").update(name='xxx') # 名称中包含 "abc"的人 都改成 xxx
```

## 10.QuerySet 查询结果排序

```python
Person.objects.all().order_by('name')
Person.objects.all().order_by('-name') # 在 column name 前加一个负号，可以实现倒序
```

## 11.QuerySet 支持链式查询

```python
Person.objects.filter(name__contains="WeizhongTu").filter(email="tuweizhong@163.com")
Person.objects.filter(name__contains="Wei").exclude(email="tuweizhong@163.com")
# 找出名称含有abc, 但是排除年龄是23岁的
Person.objects.filter(name__contains="abc").exclude(age=23)
```

**参考文档：**

更多相关内容：<http://code.ziqiangxuetang.com/django/django-queryset-api.html>

Django models 官方教程: <https://docs.djangoproject.com/en/dev/topics/db/models/>

Fields相关官方文档：<https://docs.djangoproject.com/en/dev/ref/models/fields/>



# 五、返回新QuerySets的API

**以下的方法都将返回一个新的QuerySets。**重点是加粗的几个API，其它的使用场景很少。

| 方法名                | 解释                                         |
| :-------------------- | :------------------------------------------- |
| **filter()**          | 过滤查询对象。                               |
| **exclude()**         | 排除满足条件的对象                           |
| **annotate()**        | 使用聚合函数                                 |
| **order_by()**        | 对查询集进行排序                             |
| **reverse()**         | 反向排序                                     |
| **distinct()**        | 对查询集去重                                 |
| **values()**          | 返回包含对象具体值的字典的QuerySet           |
| **values_list()**     | 与values()类似，只是返回的是元组而不是字典。 |
| dates()               | 根据日期获取查询集                           |
| datetimes()           | 根据时间获取查询集                           |
| **none()**            | 创建空的查询集                               |
| **all()**             | 获取所有的对象                               |
| union()               | 并集                                         |
| intersection()        | 交集                                         |
| difference()          | 差集                                         |
| **select_related()**  | 附带查询关联对象                             |
| `prefetch_related()`  | 预先查询                                     |
| extra()               | 附加SQL查询                                  |
| defer()               | 不加载指定字段                               |
| only()                | 只加载指定的字段                             |
| using()               | 选择数据库                                   |
| `select_for_update()` | 锁住选择的对象，直到事务结束。               |
| raw()                 | 接收一个原始的SQL查询                        |

## 1. filter()

filter(**kwargs)

返回满足查询参数的对象集合。

查找的参数（**kwargs）应该满足下文字段查找中的格式。多个参数之间是和AND的关系。

## 2. exclude()

exclude(**kwargs)

返回一个新的QuerySet，它包含**不**满足给定的查找参数的对象。

查找的参数（**kwargs）应该满足下文字段查找中的格式。多个参数通过AND连接，然后所有的内容放入NOT() 中。

下面的示例**排除**所有`pub_date`晚于2005-1-3**且**headline为“Hello” 的记录：

```python
Entry.objects.exclude(pub_date__gt=datetime.date(2005, 1, 3), headline='Hello')
```

下面的示例**排除**所有`pub_date`晚于2005-1-3**或者**headline 为“Hello” 的记录：

```python
Entry.objects.exclude(pub_date__gt=datetime.date(2005, 1, 3)).exclude(headline='Hello')
```

## 3. annotate()

annotate(*args,* *kwargs)

使用提供的聚合表达式查询对象。

表达式可以是简单的值、对模型（或任何关联模型）上的字段的引用或者聚合表达式（平均值、总和等）。

annotate()的每个参数都是一个annotation，它将添加到返回的QuerySet每个对象中。

关键字参数指定的Annotation将使用关键字作为Annotation 的别名。 匿名参数的别名将基于聚合函数的名称和模型的字段生成。 只有引用单个字段的聚合表达式才可以使用匿名参数。 其它所有形式都必须用关键字参数。

例如，如果正在操作一个Blog列表，你可能想知道每个Blog有多少Entry：

```python
>>> from django.db.models import Count
>>> q = Blog.objects.annotate(Count('entry'))
# The name of the first blog
>>> q[0].name
'Blogasaurus'
# The number of entries on the first blog
>>> q[0].entry__count
42
```

Blog模型本身没有定义`entry__count`属性，但是通过使用一个关键字参数来指定聚合函数，可以控制Annotation的名称：

```python
>>> q = Blog.objects.annotate(number_of_entries=Count('entry'))
# The number of entries on the first blog, using the name provided
>>> q[0].number_of_entries
42
```

## 4. order_by()

order_by(*fields)

默认情况下，根据模型的Meta类中的ordering属性对QuerySet中的对象进行排序

```python
Entry.objects.filter(pub_date__year=2005).order_by('-pub_date', 'headline')
```

上面的结果将按照`pub_date`降序排序，然后再按照headline升序排序。"-pub_date"前面的负号表示降序顺序。 升序是默认的。 要随机排序，使用"?"，如下所示：

```python
Entry.objects.order_by('?')
```

注：`order_by('?')`可能耗费资源且很慢，这取决于使用的数据库。

若要按照另外一个模型中的字段排序，可以使用查询关联模型的语法。即通过字段的名称后面跟两个下划线（`__`），再加上新模型中的字段的名称，直到希望连接的模型。 像这样：

```python
Entry.objects.order_by('blog__name', 'headline')
```

如果排序的字段与另外一个模型关联，Django将使用关联的模型的默认排序，或者如果没有指定Meta.ordering将通过关联的模型的主键排序。 例如，因为Blog模型没有指定默认的排序：

```python
Entry.objects.order_by('blog')
```

与以下相同：

```
Entry.objects.order_by('blog__id')
```

如果Blog设置了`ordering = ['name']`，那么第一个QuerySet将等同于：

```
Entry.objects.order_by('blog__name')
```

还可以通过调用表达式的desc()或者asc()方法：

```
Entry.objects.order_by(Coalesce('summary', 'headline').desc())
```

考虑下面的情况，指定一个多值字段来排序（例如，一个ManyToManyField 字段或者ForeignKey 字段的反向关联）：

```python
class Event(Model):
   parent = models.ForeignKey(
       'self',
       on_delete=models.CASCADE,
       related_name='children',
   )
   date = models.DateField()

Event.objects.order_by('children__date')
```

在这里，每个Event可能有多个排序数据；具有多个children的每个Event将被多次返回到`order_by()`创建的新的QuerySet中。 换句话说，用`order_by()`方法对QuerySet对象进行操作会返回一个扩大版的新QuerySet对象。因此，使用多值字段对结果进行排序时要格外小心。

没有方法指定排序是否考虑大小写。 对于大小写的敏感性，Django将根据数据库中的排序方式排序结果。

可以通过Lower将一个字段转换为小写来排序，它将达到大小写一致的排序：

```python
Entry.objects.order_by(Lower('headline').desc())
```

可以通过检查`QuerySet.ordered`属性来知道查询是否是排序的。

每个`order_by()`都将清除前面的任何排序。 例如下面的查询将按照`pub_date`排序，而不是headline：

```
Entry.objects.order_by('headline').order_by('pub_date')
```

## 5. reverse()

reverse()

反向排序QuerySet中返回的元素。 第二次调用reverse()将恢复到原有的排序。

如要获取QuerySet中最后五个元素，可以这样做：

```
my_queryset.reverse()[:5]
```

这与Python直接使用负索引有点不一样。 Django不支持负索引，只能曲线救国。

## 6. distinct()

distinct(*fields)

去除查询结果中重复的行。

默认情况下，QuerySet不会去除重复的行。当查询跨越多张表的数据时，QuerySet可能得到重复的结果，这时候可以使用distinct()进行去重。

## 7. values()

values(*fields,* *expressions)

返回一个包含数据的字典的queryset，而不是模型实例。

每个字典表示一个对象，键对应于模型对象的属性名称。

下面的例子将values() 与普通的模型对象进行比较：

```
# 列表中包含的是Blog对象
>>> Blog.objects.filter(name__startswith='Beatles')
<QuerySet [<Blog: Beatles Blog>]>
# 列表中包含的是数据字典
>>> Blog.objects.filter(name__startswith='Beatles').values()
<QuerySet [{'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}]>
```

该方法接收可选的位置参数`*fields`，它指定values()应该限制哪些字段。如果指定字段，每个字典将只包含指定的字段的键/值。如果没有指定字段，每个字典将包含数据库表中所有字段的键和值。

例如：

```
>>> Blog.objects.values()
<QuerySet [{'id': 1, 'name': 'Beatles Blog', 'tagline': 'All the latest Beatles news.'}]>
>>> Blog.objects.values('id', 'name')
<QuerySet [{'id': 1, 'name': 'Beatles Blog'}]>
```

values()方法还有关键字参数`**expressions`，这些参数将传递给`annotate()`：

```
>>> from django.db.models.functions import Lower
>>> Blog.objects.values(lower_name=Lower('name'))
<QuerySet [{'lower_name': 'beatles blog'}]>
```

在values()子句中的聚合应用于相同values()子句中的其他参数之前。 如果需要按另一个值分组，请将其添加到较早的values()子句中。 像这样：

```
>>> from django.db.models import Count
>>> Blog.objects.values('author', entries=Count('entry'))
<QuerySet [{'author': 1, 'entries': 20}, {'author': 1, 'entries': 13}]>
>>> Blog.objects.values('author').annotate(entries=Count('entry'))
<QuerySet [{'author': 1, 'entries': 33}]>
```

注意：

如果你有一个字段foo是一个ForeignKey，默认的`foo_id`参数返回的字典中将有一个叫做foo 的键，因为这是保存实际值的那个隐藏的模型属性的名称。 当调用`foo_id`并传递字段的名称，传递foo 或values()都可以，得到的结果是相同的。像这样：

```
>>> Entry.objects.values()
<QuerySet [{'blog_id': 1, 'headline': 'First Entry', ...}, ...]>
>>> Entry.objects.values('blog')
<QuerySet [{'blog': 1}, ...]>
>>> Entry.objects.values('blog_id')
<QuerySet [{'blog_id': 1}, ...]>
```

当values()与distinct()一起使用时，注意排序可能影响最终的结果。

如果values()子句位于extra()调用之后，extra()中的select参数定义的字段必须显式包含在values()调用中。 values( 调用后面的extra( 调用将忽略选择的额外的字段。

在values()之后调用only()和defer()不太合理，所以将引发一个NotImplementedError。

可以通过ManyToManyField、ForeignKey 和 OneToOneFiel 属性反向引用关联的模型的字段：

```
>>> Blog.objects.values('name', 'entry__headline')
<QuerySet [{'name': 'My blog', 'entry__headline': 'An entry'},
     {'name': 'My blog', 'entry__headline': 'Another entry'}, ...]>
```

## 8. values_list()

values_list(*fields, flat=False)

与values()类似，只是在迭代时返回的是元组而不是字典。每个元组包含传递给`values_list()`调用的相应字段或表达式的值，因此第一个项目是第一个字段等。 像这样：

```
>>> Entry.objects.values_list('id', 'headline')
<QuerySet [(1, 'First entry'), ...]>
>>> from django.db.models.functions import Lower
>>> Entry.objects.values_list('id', Lower('headline'))
<QuerySet [(1, 'first entry'), ...]>
```

如果只传递一个字段，还可以传递flat参数。 如果为True，它表示返回的结果为单个值而不是元组。 如下所示：

```
>>> Entry.objects.values_list('id').order_by('id')
<QuerySet[(1,), (2,), (3,), ...]>
>>> Entry.objects.values_list('id', flat=True).order_by('id')
<QuerySet [1, 2, 3, ...]>
```

如果有多个字段，传递flat将发生错误。

如果不传递任何值给`values_list()`，它将返回模型中的所有字段，以在模型中定义的顺序。

常见的情况是获取某个模型实例的特定字段值。可以使用`values_list()`，然后调用get()：

```
>>> Entry.objects.values_list('headline', flat=True).get(pk=1)
'First entry'
```

`values()`和`values_list()`都用于特定情况下的优化：检索数据子集，而无需创建模型实例。

注意通过ManyToManyField进行查询时的行为：

```
>>> Author.objects.values_list('name', 'entry__headline')
<QuerySet [('Noam Chomsky', 'Impressions of Gaza'),
 ('George Orwell', 'Why Socialists Do Not Believe in Fun'),
 ('George Orwell', 'In Defence of English Cooking'),
 ('Don Quixote', None)]>
```

类似地，当查询反向外键时，对于没有任何作者的条目，返回None。

```
>>> Entry.objects.values_list('authors')
<QuerySet [('Noam Chomsky',), ('George Orwell',), (None,)]>
```

## 9. dates()

dates(field, kind, order='ASC')

返回一个QuerySet，表示QuerySet内容中特定类型的所有可用日期的`datetime.date`对象列表。

field参数是模型的DateField的名称。 kind参数应为"year"，"month"或"day"。 结果列表中的每个datetime.date对象被截取为给定的类型。

"year" 返回对应该field的所有不同年份值的列表。

"month"返回字段的所有不同年/月值的列表。

"day"返回字段的所有不同年/月/日值的列表。

order参数默认为'ASC'，或者'DESC'。 它指定如何排序结果。

例子：

```
>>> Entry.objects.dates('pub_date', 'year')
[datetime.date(2005, 1, 1)]
>>> Entry.objects.dates('pub_date', 'month')
[datetime.date(2005, 2, 1), datetime.date(2005, 3, 1)]
>>> Entry.objects.dates('pub_date', 'day')
[datetime.date(2005, 2, 20), datetime.date(2005, 3, 20)]
>>> Entry.objects.dates('pub_date', 'day', order='DESC')
[datetime.date(2005, 3, 20), datetime.date(2005, 2, 20)]
>>> Entry.objects.filter(headline__contains='Lennon').dates('pub_date', 'day')
[datetime.date(2005, 3, 20)]
```

## 10. datetimes()

datetimes(field_name, kind, order='ASC', tzinfo=None)

返回QuerySet，为datetime.datetime对象的列表，表示QuerySet内容中特定种类的所有可用日期。

`field_name`应为模型的DateTimeField的名称。

kind参数应为"hour"，"minute"，"month"，"year"，"second"或"day"。

结果列表中的每个datetime.datetime对象被截取到给定的类型。

order参数默认为'ASC'，或者'DESC'。 它指定如何排序结果。

tzinfo参数定义在截取之前将数据时间转换到的时区。

## 11. none()

none()

调用none()将创建一个不返回任何对象的查询集，并且在访问结果时不会执行任何查询。

例子：

```
>>> Entry.objects.none()
<QuerySet []>
>>> from django.db.models.query import EmptyQuerySet
>>> isinstance(Entry.objects.none(), EmptyQuerySet)
True
```

## 12. all()

all()

返回当前QuerySet（或QuerySet子类）的副本。通常用于获取全部QuerySet对象。

## 13. union()

union(*other_qs, all=False)

Django中的新功能1.11。也就是集合中并集的概念！

使用SQL的UNION运算符组合两个或更多个QuerySet的结果。例如：

```
>>> qs1.union(qs2, qs3)
```

默认情况下，UNION操作符仅选择不同的值。 要允许重复值，请使用all=True参数。

## 14. intersection()

intersection(*other_qs)

Django中的新功能1.11。也就是集合中交集的概念！

使用SQL的INTERSECT运算符返回两个或更多个QuerySet的共有元素。例如：

```
>>> qs1.intersection(qs2, qs3)
```

## 15. difference()

difference(*other_qs)

Django中的新功能1.11。也就是集合中差集的概念！

使用SQL的EXCEPT运算符只保留QuerySet中的元素，但不保留其他QuerySet中的元素。例如：

```
>>> qs1.difference(qs2, qs3)
```

## 16. select_related()

select_related(*fields)

沿着外键关系查询关联的对象的数据。这会生成一个复杂的查询并引起性能的损耗，但是在以后使用外键关系时将不需要再次数据库查询。

下面的例子解释了普通查询和`select_related()`查询的区别。 下面是一个标准的查询：

```
# 访问数据库。
e = Entry.objects.get(id=5)
# 再次访问数据库以得到关联的Blog对象。
b = e.blog
```

下面是一个`select_related`查询：

```
# 访问数据库。
e = Entry.objects.select_related('blog').get(id=5)
# 不会访问数据库，因为e.blog已经在前面的查询中获得了。
b = e.blog
```

`select_related()`可用于objects任何的查询集：

```
from django.utils import timezone

# Find all the blogs with entries scheduled to be published in the future.
blogs = set()

for e in Entry.objects.filter(pub_date__gt=timezone.now()).select_related('blog'):
    # 没有select_related()，下面的语句将为每次循环迭代生成一个数据库查询,以获得每个entry关联的blog。
    blogs.add(e.blog)
```

`filter()`和`select_related()`的顺序不重要。 下面的查询集是等同的：

```
Entry.objects.filter(pub_date__gt=timezone.now()).select_related('blog')
Entry.objects.select_related('blog').filter(pub_date__gt=timezone.now())
```

可以沿着外键查询。 如果有以下模型：

```
from django.db import models

class City(models.Model):
    # ...
    pass

class Person(models.Model):
    # ...
    hometown = models.ForeignKey(
        City,
        on_delete=models.SET_NULL,
        blank=True,
        null=True,
    )

class Book(models.Model):
    # ...
    author = models.ForeignKey(Person, on_delete=models.CASCADE)
```

调用`Book.objects.select_related('author__hometown').get(id=4)`将缓存相关的Person 和相关的City：

```
b = Book.objects.select_related('author__hometown').get(id=4)
p = b.author         # Doesn't hit the database.
c = p.hometown       # Doesn't hit the database.
b = Book.objects.get(id=4) # No select_related() in this example.
p = b.author         # Hits the database.
c = p.hometown       # Hits the database.
```

在传递给`select_related()`的字段中，可以使用任何ForeignKey和OneToOneField。

在传递给`select_related`的字段中，还可以反向引用OneToOneField。也就是说，可以回溯到定义OneToOneField 的字段。 此时，可以使用关联对象字段的`related_name`，而不要指定字段的名称。

## 17. prefetch_related()

prefetch_related(*lookups)

在单个批处理中自动检索每个指定查找的相关对象。

与`select_related`类似，但是策略是完全不同的。

假设有这些模型：

```
from django.db import models

class Topping(models.Model):
    name = models.CharField(max_length=30)

class Pizza(models.Model):
    name = models.CharField(max_length=50)
    toppings = models.ManyToManyField(Topping)

    def __str__(self):              # __unicode__ on Python 2
        return "%s (%s)" % (
            self.name,
            ", ".join(topping.name for topping in self.toppings.all()),
        )
```

并运行：

```
>>> Pizza.objects.all()
["Hawaiian (ham, pineapple)", "Seafood (prawns, smoked salmon)"...
```

问题是每次QuerySet要求`Pizza.objects.all()`查询数据库，因此`self.toppings.all()`将在`Pizza Pizza.__str__()`中的每个项目的Toppings表上运行查询。

可以使用`prefetch_related`减少为只有两个查询：

```
>>> Pizza.objects.all().prefetch_related('toppings')
```

这意味着现在每次`self.toppings.all()`被调用，不会再去数据库查找，而是在一个预取的QuerySet缓存中查找。

还可以使用正常连接语法来执行相关字段的相关字段。 假设在上面的例子中增加一个额外的模型：

```
class Restaurant(models.Model):
    pizzas = models.ManyToManyField(Pizza, related_name='restaurants')
    best_pizza = models.ForeignKey(Pizza, related_name='championed_by')
```

以下是合法的：

```
>>> Restaurant.objects.prefetch_related('pizzas__toppings')
```

这将预取所有属于餐厅的比萨饼，和所有属于那些比萨饼的配料。 这将导致总共3个查询 - 一个用于餐馆，一个用于比萨饼，一个用于配料。

```
>>> Restaurant.objects.prefetch_related('best_pizza__toppings')
```

这将获取最好的比萨饼和每个餐厅最好的披萨的所有配料。 这将在3个表中查询 - 一个为餐厅，一个为“最佳比萨饼”，一个为一个为配料。

当然，也可以使用`best_pizza`来获取`select_related`关系，以将查询数减少为2：

```
>>> Restaurant.objects.select_related('best_pizza').prefetch_related('best_pizza__toppings')
```

## 18. extra()

extra(select=None, where=None, params=None, tables=None, order_by=None, select_params=None)

有些情况下，Django的查询语法难以简单的表达复杂的WHERE子句，对于这种情况,可以在extra()生成的SQL从句中注入新子句。使用这种方法作为最后的手段，这是一个旧的API，在将来的某个时候可能被弃用。仅当无法使用其他查询方法表达查询时才使用它。

例如：

```
>>> qs.extra(
...     select={'val': "select col from sometable where othercol = %s"},
...     select_params=(someparam,),
... )
```

相当于：

```
>>> qs.annotate(val=RawSQL("select col from sometable where othercol = %s", (someparam,)))
```

## 19. defer()

defer(*fields)

在一些复杂的数据建模情况下，模型可能包含大量字段，其中一些可能包含大尺寸数据（例如文本字段），将它们转换为Python对象需要花费很大的代价。

当最初获取数据时不知道是否需要这些特定字段的情况下，如果正在使用查询集的结果，可以告诉Django不要从数据库中检索它们。

通过传递字段名称到defer()实现不加载：

```
Entry.objects.defer("headline", "body")
```

具有延迟加载字段的查询集仍将返回模型实例。

每个延迟字段将在你访问该字段时从数据库中检索（每次只检索一个，而不是一次检索所有的延迟字段）。

可以多次调用defer()。 每个调用都向延迟集添加新字段：

```
# 延迟body和headline两个字段。
Entry.objects.defer("body").filter(rating=5).defer("headline")
```

字段添加到延迟集的顺序无关紧要。对已经延迟的字段名称再次defer()没有问题（该字段仍将被延迟）。

可以使用标准的双下划线符号来分隔关联的字段，从而加载关联模型中的字段：

```
Blog.objects.select_related().defer("entry__headline", "entry__body")
```

如果要清除延迟字段集，将None作为参数传递到defer()：

```
# 立即加载所有的字段。
my_queryset.defer(None)
```

defer()方法（及其兄弟，only()）仅适用于高级用例，它们提供了数据加载的优化方法。

## 20. only()

only(*fields)

only()方法与defer()相反。

如果有一个模型几乎所有的字段需要延迟，使用only()指定补充的字段集可以使代码更简单。

假设有一个包含字段biography、age和name的模型。 以下两个查询集是相同的，就延迟字段而言：

```
Person.objects.defer("age", "biography")
Person.objects.only("name")
```

每当你调用only()时，它将替换立即加载的字段集。因此，对only()的连续调用的结果是只有最后一次调用的字段被考虑：

```
# This will defer all fields except the headline.
Entry.objects.only("body", "rating").only("headline")
```

由于defer()以递增方式动作（向延迟列表中添加字段），因此你可以结合only()和defer()调用：

```
# Final result is that everything except "headline" is deferred.
Entry.objects.only("headline", "body").defer("body")
# Final result loads headline and body immediately (only() replaces any
# existing set of fields).
Entry.objects.defer("body").only("headline", "body")
```

当对具有延迟字段的实例调用save()时，仅保存加载的字段。

## 21. using()

using(alias)

如果正在使用多个数据库，这个方法用于指定在哪个数据库上查询QuerySet。方法的唯一参数是数据库的别名，定义在DATABASES。

例如：

```
# queries the database with the 'default' alias.
>>> Entry.objects.all()
# queries the database with the 'backup' alias
>>> Entry.objects.using('backup')
```

## 22. select_for_update()

select_for_update(nowait=False, skip_locked=False)

返回一个锁住行直到事务结束的查询集，如果数据库支持，它将生成一个`SELECT ... FOR UPDATE`语句。

例如：

```
entries = Entry.objects.select_for_update().filter(author=request.user)
```

所有匹配的行将被锁定，直到事务结束。这意味着可以通过锁防止数据被其它事务修改。

一般情况下如果其他事务锁定了相关行，那么本查询将被阻塞，直到锁被释放。使用`select_for_update(nowait=True)`将使查询不阻塞。如果其它事务持有冲突的锁,那么查询将引发`DatabaseError`异常。也可以使用`select_for_update(skip_locked=True)`忽略锁定的行。nowait和`skip_locked`是互斥的。

目前，postgresql，oracle和mysql数据库后端支持`select_for_update()`。但是，MySQL不支持nowait和`skip_locked`参数。

## 23. raw()

raw(raw_query, params=None, translations=None)

接收一个原始的SQL查询，执行它并返回一个`django.db.models.query.RawQuerySet`实例。

这个RawQuerySet实例可以迭代，就像普通的QuerySet一样。



# 六、不返回QuerySets的API

以下的方法不会返回QuerySets，但是作用非常强大，尤其是粗体显示的方法，需要背下来。

| 方法名                 | 解释                             |
| :--------------------- | :------------------------------- |
| **get()**              | 获取单个对象                     |
| **create()**           | 创建对象，无需save()             |
| **get_or_create()**    | 查询对象，如果没有找到就新建对象 |
| **update_or_create()** | 更新对象，如果没有找到就创建对象 |
| `bulk_create()`        | 批量创建对象                     |
| **count()**            | 统计对象的个数                   |
| `in_bulk()`            | 根据主键值的列表，批量返回对象   |
| `iterator()`           | 获取包含对象的迭代器             |
| **latest()**           | 获取最近的对象                   |
| **earliest()**         | 获取最早的对象                   |
| **first()**            | 获取第一个对象                   |
| **last()**             | 获取最后一个对象                 |
| **aggregate()**        | 聚合操作                         |
| **exists()**           | 判断queryset中是否有对象         |
| **update()**           | 批量更新对象                     |
| **delete()**           | 批量删除对象                     |
| as_manager()           | 获取管理器                       |

## 1. get()

get(**kwargs)

返回按照查询参数匹配到的单个对象，参数的格式应该符合Field lookups的要求。

如果匹配到的对象个数不只一个的话，触发MultipleObjectsReturned异常

如果根据给出的参数匹配不到对象的话，触发DoesNotExist异常。例如：

```
Entry.objects.get(id='foo') # raises Entry.DoesNotExist
```

DoesNotExist异常从`django.core.exceptions.ObjectDoesNotExist`继承，可以定位多个DoesNotExist异常。 例如：

```
from django.core.exceptions import ObjectDoesNotExist
try:
    e = Entry.objects.get(id=3)
    b = Blog.objects.get(id=1)
except ObjectDoesNotExist:
    print("Either the entry or blog doesn't exist.")
```

如果希望查询器只返回一行，则可以使用get()而不使用任何参数来返回该行的对象：

```
entry = Entry.objects.filter(...).exclude(...).get()
```

## 2. create()

create(**kwargs)

在一步操作中同时创建并且保存对象的便捷方法.

```
p = Person.objects.create(first_name="Bruce", last_name="Springsteen")
```

等于:

```
p = Person(first_name="Bruce", last_name="Springsteen")
p.save(force_insert=True)
```

参数`force_insert`表示强制创建对象。如果model中有一个你手动设置的主键，并且这个值已经存在于数据库中, 调用create()将会失败并且触发IntegrityError因为主键必须是唯一的。如果你手动设置了主键，做好异常处理的准备。

## 3. get_or_create()

get_or_create(defaults=None, **kwargs)

**通过kwargs来查询对象的便捷方法（如果模型中的所有字段都有默认值，可以为空），如果该对象不存在则创建一个新对象**。

该方法**返回一个由(object, created)组成的元组**，元组中的object 是一个查询到的或者是被创建的对象， created是一个表示是否创建了新的对象的布尔值。

对于下面的代码：

```
try:
    obj = Person.objects.get(first_name='John', last_name='Lennon')
except Person.DoesNotExist:
    obj = Person(first_name='John', last_name='Lennon', birthday=date(1940, 10, 9))
    obj.save()
```

如果模型的字段数量较大的话，这种模式就变的非常不易用了。 上面的示例可以用`get_or_create()`重写 :

```
obj, created = Person.objects.get_or_create(
    first_name='John',
    last_name='Lennon',
    defaults={'birthday': date(1940, 10, 9)},
)
```

任何传递给`get_or_create()`的关键字参数，除了一个可选的defaults，都将传递给get()调用。 如果查找到一个对象，返回一个包含匹配到的对象以及False 组成的元组。 如果查找到的对象超过一个以上，将引发MultipleObjectsReturned。如果查找不到对象，`get_or_create()`将会实例化并保存一个新的对象，返回一个由新的对象以及True组成的元组。新的对象将会按照以下的逻辑创建:

```
params = {k: v for k, v in kwargs.items() if '__' not in k}
params.update({k: v() if callable(v) else v for k, v in defaults.items()})
obj = self.model(**params)
obj.save()
```

它表示从非'defaults' 且不包含双下划线的关键字参数开始。然后将defaults的内容添加进来，覆盖必要的键，并使用结果作为关键字参数传递给模型类。

如果有一个名为`defaults__exact`的字段，并且想在`get_or_create()`时用它作为精确查询，只需要使用defaults，像这样：

```
Foo.objects.get_or_create(defaults__exact='bar', defaults={'defaults': 'baz'})
```

当你使用手动指定的主键时，`get_or_create()`方法与`create()`方法有相似的错误行为 。 如果需要创建一个对象而该对象的主键早已存在于数据库中，IntegrityError异常将会被触发。

这个方法假设进行的是原子操作，并且正确地配置了数据库和正确的底层数据库行为。如果数据库级别没有对`get_or_create`中用到的kwargs强制要求唯一性（unique和unique_together），方法容易导致竞态条件，可能会有相同参数的多行同时插入。（简单理解，kwargs必须指定的是主键或者unique属性的字段才安全。）

最后建议只在Django视图的POST请求中使用get_or_create()，因为这是一个具有修改性质的动作，不应该使用在GET请求中，那样不安全。

可以通过ManyToManyField属性和反向关联使用`get_or_create()`。在这种情况下，应该限制查询在关联的上下文内部。 否则，可能导致完整性问题。

例如下面的模型：

```
class Chapter(models.Model):
    title = models.CharField(max_length=255, unique=True)

class Book(models.Model):
    title = models.CharField(max_length=256)
    chapters = models.ManyToManyField(Chapter)
```

可以通过Book的chapters字段使用`get_or_create()`，但是它只会获取该Book内部的上下文：

```
>>> book = Book.objects.create(title="Ulysses")
>>> book.chapters.get_or_create(title="Telemachus")
(<Chapter: Telemachus>, True)
>>> book.chapters.get_or_create(title="Telemachus")
(<Chapter: Telemachus>, False)
>>> Chapter.objects.create(title="Chapter 1")
<Chapter: Chapter 1>
>>> book.chapters.get_or_create(title="Chapter 1")
# Raises IntegrityError
```

发生这个错误是因为尝试通过Book “Ulysses”获取或者创建“Chapter 1”，但是它不能，因为它与这个book不关联，但因为title 字段是唯一的它仍然不能创建。

在Django1.11在defaults中增加了对可调用值的支持。

## 4. update_or_create()

update_or_create(defaults=None, **kwargs)

类似前面的`get_or_create()`。

**通过给出的kwargs来更新对象的便捷方法， 如果没找到对象，则创建一个新的对象**。defaults是一个由 (field, value)对组成的字典，用于更新对象。defaults中的值可以是可调用对象（也就是说函数等）。

该方法返回一个由(object, created)组成的元组,元组中的object是一个创建的或者是被更新的对象， created是一个标示是否创建了新的对象的布尔值。

`update_or_create`方法尝试通过给出的kwargs 去从数据库中获取匹配的对象。 如果找到匹配的对象，它将会依据defaults 字典给出的值更新字段。

像下面的代码：

```
defaults = {'first_name': 'Bob'}
try:
    obj = Person.objects.get(first_name='John', last_name='Lennon')
    for key, value in defaults.items():
        setattr(obj, key, value)
    obj.save()
except Person.DoesNotExist:
    new_values = {'first_name': 'John', 'last_name': 'Lennon'}
    new_values.update(defaults)
    obj = Person(**new_values)
    obj.save()
```

如果模型的字段数量较大的话，这种模式就变的非常不易用了。 上面的示例可以用`update_or_create()` 重写:

```
obj, created = Person.objects.update_or_create(
    first_name='John', last_name='Lennon',
    defaults={'first_name': 'Bob'},
)
```

kwargs中的名称如何解析的详细描述可以参见`get_or_create()`。

和`get_or_create()`一样，这个方法也容易导致竞态条件，如果数据库层级没有前置唯一性会让多行同时插入。

在Django1.11在defaults中增加了对可调用值的支持。

## 5. bulk_create()

bulk_create(objs, batch_size=None)

以高效的方式（通常只有1个查询，无论有多少对象）将提供的对象列表插入到数据库中：

```
>>> Entry.objects.bulk_create([
...     Entry(headline='This is a test'),
...     Entry(headline='This is only a test'),
... ])
```

注意事项：

- 不会调用模型的save()方法，并且不会发送`pre_save`和`post_save`信号。
- 不适用于多表继承场景中的子模型。
- 如果模型的主键是AutoField，则不会像save()那样检索并设置主键属性，除非数据库后端支持。
- 不适用于多对多关系。

`batch_size`参数控制在单个查询中创建的对象数。

## 6. count()

count()

返回在数据库中对应的QuerySet对象的个数。count()永远不会引发异常。

例如：

```
# 返回总个数.
Entry.objects.count()
# 返回包含有'Lennon'的对象的总数
Entry.objects.filter(headline__contains='Lennon').count()
```

## 7. in_bulk()

in_bulk(id_list=None)

获取主键值的列表，并返回将每个主键值映射到具有给定ID的对象的实例的字典。 如果未提供列表，则会返回查询集中的所有对象。

例如：

```
>>> Blog.objects.in_bulk([1])
{1: <Blog: Beatles Blog>}
>>> Blog.objects.in_bulk([1, 2])
{1: <Blog: Beatles Blog>, 2: <Blog: Cheddar Talk>}
>>> Blog.objects.in_bulk([])
{}
>>> Blog.objects.in_bulk()
{1: <Blog: Beatles Blog>, 2: <Blog: Cheddar Talk>, 3: <Blog: Django Weblog>}
```

如果向`in_bulk()`传递一个空列表，会得到一个空的字典。

在旧版本中，`id_list`是必需的参数，现在是一个可选参数。

## 8. iterator()

iterator()

提交数据库操作，获取QuerySet，并返回一个迭代器。

QuerySet通常会在内部缓存其结果，以便在重复计算时不会导致额外的查询。而iterator()将直接读取结果，不在QuerySet级别执行任何缓存。对于返回大量只需要访问一次的对象的QuerySet，这可以带来更好的性能，显著减少内存使用。

请注意，在已经提交了的iterator()上使用QuerySet会强制它再次提交数据库操作，进行重复查询。此外，使用iterator()会导致先前的`prefetch_related()`调用被忽略，因为这两个一起优化没有意义。

## 9. latest()

latest(field_name=None)

使用日期字段field_name，按日期返回最新对象。

下例根据Entry的'pub_date'字段返回最新发布的entry：

```
Entry.objects.latest('pub_date')
```

如果模型的Meta指定了`get_latest_by`，则可以将latest()参数留给earliest()或者`field_name`。 默认情况下，Django将使用`get_latest_by`中指定的字段。

earliest()和latest()可能会返回空日期的实例,可能需要过滤掉空值：

```
Entry.objects.filter(pub_date__isnull=False).latest('pub_date')
```

## 10. earliest()

earliest(field_name=None)

类同latest()。

## 11. first()

first()

返回结果集的第一个对象, 当没有找到时返回None。如果QuerySet没有设置排序,则将会自动按主键进行排序。例如：

```
p = Article.objects.order_by('title', 'pub_date').first()
```

first()是一个简便方法，下面的例子和上面的代码效果是一样：

```
try:
    p = Article.objects.order_by('title', 'pub_date')[0]
except IndexError:
    p = None
```

## 12. last()

last()

工作方式类似first()，只是返回的是查询集中最后一个对象。

## 13. aggregate()

aggregate(*args,* *kwargs)

返回汇总值的字典（平均值，总和等）,通过QuerySet进行计算。每个参数指定返回的字典中将要包含的值。

使用关键字参数指定的聚合将使用关键字参数的名称作为Annotation 的名称。 匿名参数的名称将基于聚合函数的名称和模型字段生成。 复杂的聚合不可以使用匿名参数，必须指定一个关键字参数作为别名。

例如，想知道Blog Entry 的数目：

```
>>> from django.db.models import Count
>>> q = Blog.objects.aggregate(Count('entry'))
{'entry__count': 16}
```

通过使用关键字参数来指定聚合函数，可以控制返回的聚合的值的名称：

```
>>> q = Blog.objects.aggregate(number_of_entries=Count('entry'))
{'number_of_entries': 16}
```

## 14. exists()

exists()

如果QuerySet包含任何结果，则返回True，否则返回False。

查找具有唯一性字段（例如primary_key）的模型是否在一个QuerySet中的最高效的方法是：

```
entry = Entry.objects.get(pk=123)
if some_queryset.filter(pk=entry.pk).exists():
    print("Entry contained in queryset")
```

它将比下面的方法快很多，这个方法要求对QuerySet求值并迭代整个QuerySet：

```
if entry in some_queryset:
   print("Entry contained in QuerySet")
```

若要查找一个QuerySet是否包含任何元素：

```
if some_queryset.exists():
    print("There is at least one object in some_queryset")
```

将快于：

```
if some_queryset:
    print("There is at least one object in some_queryset")
```

## 15. update()

update(**kwargs)

**对指定的字段执行批量更新操作，并返回匹配的行数**（如果某些行已具有新值，则可能不等于已更新的行数）。

例如，要对2010年发布的所有博客条目启用评论，可以执行以下操作：

```
>>> Entry.objects.filter(pub_date__year=2010).update(comments_on=False)
```

可以同时更新多个字段 （没有多少字段的限制）。 例如同时更新comments_on和headline字段：

```
>>> Entry.objects.filter(pub_date__year=2010).update(comments_on=False, headline='This is old')
```

update()方法无需save操作。唯一限制是它只能更新模型主表中的列，而不是关联的模型，例如不能这样做：

```
>>> Entry.objects.update(blog__name='foo') # Won't work!
```

仍然可以根据相关字段进行过滤：

```
>>> Entry.objects.filter(blog__id=1).update(comments_on=True)
```

update()方法返回受影响的行数：

```
>>> Entry.objects.filter(id=64).update(comments_on=True)
1
>>> Entry.objects.filter(slug='nonexistent-slug').update(comments_on=True)
0
>>> Entry.objects.filter(pub_date__year=2010).update(comments_on=False)
132
```

如果你只是更新一下对象，不需要为对象做别的事情，最有效的方法是调用update()，而不是将模型对象加载到内存中。 例如，不要这样做：

```
e = Entry.objects.get(id=10)
e.comments_on = False
e.save()
```

建议如下操作：

```
Entry.objects.filter(id=10).update(comments_on=False)
```

用update()还可以防止在加载对象和调用save()之间的短时间内数据库中某些内容可能发生更改的竞争条件。

如果想更新一个具有自定义save()方法的模型的记录，请循环遍历它们并调用save()，如下所示：

```
for e in Entry.objects.filter(pub_date__year=2010):
    e.comments_on = False
    e.save()
```

## 16. delete()

delete()

批量删除QuerySet中的所有对象，并返回删除的对象个数和每个对象类型的删除次数的字典。

delete()动作是立即执行的。

不能在QuerySet上调用delete()。

例如，要删除特定博客中的所有条目：

```
>>> b = Blog.objects.get(pk=1)
# Delete all the entries belonging to this Blog.
>>> Entry.objects.filter(blog=b).delete()
(4, {'weblog.Entry': 2, 'weblog.Entry_authors': 2})
```

默认情况下，Django的ForeignKey使用SQL约束ON DELETE CASCADE，任何具有指向要删除的对象的外键的对象将与它们一起被删除。 像这样：

```
>>> blogs = Blog.objects.all()
# This will delete all Blogs and all of their Entry objects.
>>> blogs.delete()
(5, {'weblog.Blog': 1, 'weblog.Entry': 2, 'weblog.Entry_authors': 2})
```

这种级联的行为可以通过的ForeignKey的on_delete参数自定义。（什么时候要改变这种行为呢？比如日志数据，就不能和它关联的主体一并被删除！）

delete()会为所有已删除的对象（包括级联删除）发出`pre_delete`和`post_delete`信号。

## 17. as_manager()

classmethod as_manager()

一个类方法，返回Manager的实例与QuerySet的方法的副本。



# 七、字段查询参数及聚合函数

字段查询是指如何指定SQL WHERE子句的内容。它们用作QuerySet的filter(), exclude()和get()方法的关键字参数。

**默认查找类型为exact。**

------

下表列出了所有的字段查询参数：

| 字段名          | 说明                     |
| :-------------- | :----------------------- |
| **exact**       | 精确匹配                 |
| **iexact**      | 不区分大小写的精确匹配   |
| **contains**    | 包含匹配                 |
| **icontains**   | 不区分大小写的包含匹配   |
| **in**          | 在..之内的匹配           |
| **gt**          | 大于                     |
| **gte**         | 大于等于                 |
| **lt**          | 小于                     |
| **lte**         | 小于等于                 |
| **startswith**  | 从开头匹配               |
| **istartswith** | 不区分大小写从开头匹配   |
| **endswith**    | 从结尾处匹配             |
| **iendswith**   | 不区分大小写从结尾处匹配 |
| **range**       | 范围匹配                 |
| **date**        | 日期匹配                 |
| **year**        | 年份                     |
| **month**       | 月份                     |
| **day**         | 日期                     |
| **week**        | 第几周                   |
| **week_day**    | 周几                     |
| **time**        | 时间                     |
| **hour**        | 小时                     |
| **minute**      | 分钟                     |
| **second**      | 秒                       |
| **isnull**      | 判断是否为空             |
| search          | 1.10中被废弃             |
| **regex**       | 区分大小写的正则匹配     |
| **iregex**      | 不区分大小写的正则匹配   |

## 1. exact

精确匹配。 默认的查找类型！

```
Entry.objects.get(id__exact=14)
Entry.objects.get(id__exact=None)
```

## 2. iexact

不区分大小写的精确匹配。

```
Blog.objects.get(name__iexact='beatles blog')
Blog.objects.get(name__iexact=None)
```

第一个查询将匹配 'Beatles Blog', 'beatles blog', 'BeAtLes BLoG'等等。

## 3. contains

大小写敏感的包含关系匹配。

```
Entry.objects.get(headline__contains='Lennon')
```

这将匹配标题'Lennon honored today'，但不匹配'lennon honored today'。

## 4. icontains

不区分大小写的包含关系匹配。

```
Entry.objects.get(headline__icontains='Lennon')
```

## 5. in

在给定的列表里查找。

```
Entry.objects.filter(id__in=[1, 3, 4])
```

还可以使用动态查询集，而不是提供文字值列表：

```
inner_qs = Blog.objects.filter(name__contains='Cheddar')
entries = Entry.objects.filter(blog__in=inner_qs)
```

或者从values()或`values_list()`中获取的QuerySet作为比对的对象：

```
inner_qs = Blog.objects.filter(name__contains='Ch').values('name')
entries = Entry.objects.filter(blog__name__in=inner_qs)
```

下面的例子将产生一个异常，因为试图提取两个字段的值，但是查询语句只需要一个字段的值：

```
# 错误的实例，将弹出异常。
inner_qs = Blog.objects.filter(name__contains='Ch').values('name', 'id')
entries = Entry.objects.filter(blog__name__in=inner_qs)
```

## 6. gt

大于

```
Entry.objects.filter(id__gt=4)
```

## 7. gte

大于或等于

## 8. lt

小于

## 9. lte

小于或等于

## 10. startswith

区分大小写，从开始位置匹配。

```
Entry.objects.filter(headline__startswith='Lennon')
```

## 11. istartswith

不区分大小写，从开始位置匹配。

```
Entry.objects.filter(headline__istartswith='Lennon')
```

## 12. endswith

区分大小写，从结束未知开始匹配。

```
Entry.objects.filter(headline__endswith='Lennon')
```

## 13. iendswith

不区分大小写，从结束未知开始匹配。

```
Entry.objects.filter(headline__iendswith='Lennon')
```

## 14. range

范围测试（包含于之中）。

```
import datetime
start_date = datetime.date(2005, 1, 1)
end_date = datetime.date(2005, 3, 31)
Entry.objects.filter(pub_date__range=(start_date, end_date))
```

警告:过滤具有日期的DateTimeField不会包含最后一天，因为边界被解释为“给定日期的0am”。

## 15. date

进行日期对比。

```
Entry.objects.filter(pub_date__date=datetime.date(2005, 1, 1))
Entry.objects.filter(pub_date__date__gt=datetime.date(2005, 1, 1))
```

当`USE_TZ`为True时，字段将转换为当前时区，然后进行过滤。

## 16. year

对年份进行匹配。

```
Entry.objects.filter(pub_date__year=2005)
Entry.objects.filter(pub_date__year__gte=2005)
```

当`USE_TZ`为True时，在过滤之前，datetime字段将转换为当前时区。

## 17. month

对月份进行匹配。取整数1（1月）至12（12月）。

```
Entry.objects.filter(pub_date__month=12)
Entry.objects.filter(pub_date__month__gte=6)
```

当USE_TZ为True时，在过滤之前，datetime字段将转换为当前时区。

## 18. day

对具体到某一天的匹配。

```
Entry.objects.filter(pub_date__day=3)
Entry.objects.filter(pub_date__day__gte=3)
```

当USE_TZ为True时，在过滤之前，datetime字段将转换为当前时区。

## 19. week

Django1.11中的新功能。根据ISO-8601返回周号（1-52或53），即星期一开始的星期，星期四或之前的第一周。

```
Entry.objects.filter(pub_date__week=52)
Entry.objects.filter(pub_date__week__gte=32, pub_date__week__lte=38)
```

当USE_TZ为True时，字段将转换为当前时区，然后进行过滤。

## 20. week_day

进行“星期几”匹配。 取整数值，星期日为1，星期一为2，星期六为7。

```
Entry.objects.filter(pub_date__week_day=2)
Entry.objects.filter(pub_date__week_day__gte=2)
```

当USE_TZ为True时，在过滤之前，datetime字段将转换为当前时区。

## 21. time

Django1.11中的新功能。

将字段的值转为datetime.time格式并进行对比。

```
Entry.objects.filter(pub_date__time=datetime.time(14, 30))
Entry.objects.filter(pub_date__time__between=(datetime.time(8), datetime.time(17)))
```

USE_TZ为True时，字段将转换为当前时区，然后进行过滤。

## 22. hour

对小时进行匹配。 取0和23之间的整数。

```
Event.objects.filter(timestamp__hour=23)
Event.objects.filter(time__hour=5)
Event.objects.filter(timestamp__hour__gte=12)
```

当USE_TZ为True时，值将过滤前转换为当前时区。

## 23. minute

对分钟匹配。取0和59之间的整数。

```
Event.objects.filter(timestamp__minute=29)
Event.objects.filter(time__minute=46)
Event.objects.filter(timestamp__minute__gte=29)
```

当USE_TZ为True时，值将被过滤前转换为当前时区。

## 24. second

对秒数进行匹配。取0和59之间的整数。

```
Event.objects.filter(timestamp__second=31)
Event.objects.filter(time__second=2)
Event.objects.filter(timestamp__second__gte=31)
```

当USE_TZ为True时，值将过滤前转换为当前时区。

## 25. isnull

值为False或True, 相当于SQL语句IS NULL和IS NOT NULL.

```
Entry.objects.filter(pub_date__isnull=True)
```

## 26. search

自1.10版以来已弃用。

## 27. regex

区分大小写的正则表达式匹配。

```
Entry.objects.get(title__regex=r'^(An?|The) +')
```

建议使用原始字符串（例如，r'foo'而不是'foo'）来传递正则表达式语法。

## 28. iregex

不区分大小写的正则表达式匹配。

```
Entry.objects.get(title__iregex=r'^(an?|the) +')
```

## 聚合函数

Django的`django.db.models`模块提供以下聚合函数。

## 1. expression

引用模型字段的一个字符串，或者一个query expression。

## 2. output_field

用来表示返回值的model field，一个可选的参数。

## 3. `**extra`

关键字参数可以给聚合函数生成的SQL提供额外的信息。

## 4. Avg

class Avg(expression, output_field=FloatField(), **extra)[source]

返回给定表达式的平均值，它必须是数值，除非指定不同的`output_field`。

```
默认的别名：<field>__avg
返回类型：float（或指定任何output_field的类型）
```

## 5. Count

class Count(expression, distinct=False, **extra)[source]

返回与expression相关的对象的个数。

```
默认的别名：<field>__count
返回类型：int
有一个可选的参数：distinct。如果distinct=True，Count 将只计算唯一的实例。默认值为False。
```

## 6. Max

class Max(expression, output_field=None, **extra)[source]

返回expression的最大值。

```
默认的别名：<field>__max
返回类型：与输入字段的类型相同，如果提供则为`output_field`类型
```

## 7. Min

class Min(expression, output_field=None, **extra)[source]

返回expression的最小值。

```
默认的别名：<field>__min
返回类型：与输入字段的类型相同，如果提供则为`output_field`类型
```

## 8. StdDev

class StdDev(expression, sample=False, **extra)[source]

返回expression的标准差。

```
默认的别名：<field>__stddev
返回类型：float
有一个可选的参数：sample。默认情况下，返回群体的标准差。如果sample=True，返回样本的标准差。
SQLite 没有直接提供StdDev。
```

## 9. Sum

class Sum(expression, output_field=None, **extra)[source]

计算expression的所有值的和。

```
默认的别名：<field>__sum
返回类型：与输入字段的类型相同，如果提供则为output_field类型
```

## 10. Variance

class Variance(expression, sample=False, **extra)[source]

返回expression的方差。

```
默认的别名：<field>__variance
返回类型：float
有一个可选的参数：sample。
SQLite 没有直接提供Variance。
```



# 八、Django后台

**与后台相关文件：**每个app中的 admin.py 文件与后台相关。

## 1.在admin.py文件中注册数据库模型

创建app后，打开该文件下的admin.py文件并注册该模型，其中Article为数据库模型：

```python
from  django.contrib import admin
from .models import Article
admin.site.register(Article)
```

**推荐定义 Model 的时候 写一个__str__函数**

```python
def __str__(self):
        return self.title
```

## 2.在列表显示与字段相关的其它内容

后台已经基本上做出来了，可是如果我们还需要显示一些其它的fields呢？

```python
from django.contrib import admin
from models import Article, Person

class ArticleAdmin(admin.ModelAdmin):
    list_display =('title', 'pub_date', 'update_time',)
admin.site.register(Article, ArticleAdmin)
```

## 3.其它一些常用的功能

**搜索功能**：search_fields = ('title', 'content',) 这样就可以按照 标题或内容搜索了

<https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.ModelAdmin.search_fields>

**筛选功能**：list_filter = ('status',) 这样就可以根据文章的状态去筛选，比如找出是草稿的文章

<https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_filter>

新增或修改时的**布局顺序**：<https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.ModelAdmin.fieldsets>

有时候我们需要对django admin site进行修改以满足自己的需求，那么我们可以从哪些地方入手呢？

以下举例说明：

**（1）定制加载的列表, 根据不同的人显示不同的内容列表，比如输入员只能看见自己输入的，审核员能看到所有的草稿，这时候就需要重写get_queryset方法**

```python
class MyModelAdmin(admin.ModelAdmin):
    def get_queryset(self, request):
        qs = super(MyModelAdmin, self).get_queryset(request)
        if request.user.is_superuser:
            return qs
        else:
            return qs.filter(author=request.user)
```

该类实现的功能是如果是超级管理员就列出所有的，如果不是，就仅列出访问者自己相关的


**（2）定制搜索功能（django 1.6及以上才有)**

```python
class PersonAdmin(admin.ModelAdmin):
    list_display = ('name', 'age')
    search_fields = ('name',)

    def get_search_results(self, request, queryset, search_term):
        queryset, use_distinct = super(PersonAdmin, self).get_search_results(request, queryset, search_term)
        try:
            search_term_as_int = int(search_term)
            queryset |= self.model.objects.filter(age=search_term_as_int)
        except:
            pass
        return queryset, use_distinct
```

queryset 是默认的结果，search_term 是在后台搜索的关键词

**（3）修改保存时的一些操作，可以检查用户，保存的内容等，比如保存时加上添加人**

```python
from django.contrib import admin

class ArticleAdmin(admin.ModelAdmin):
    def save_model(self, request, obj, form, change):
        obj.user = request.user
        obj.save()
```

其中obj是修改后的对象，form是返回的表单（修改后的），**当新建一个对象时 change = False, 当修改一个对象时 change = True**
**如果需要获取修改前的对象的内容可以用**

```python
from django.contrib import admin

class ArticleAdmin(admin.ModelAdmin):
    def save_model(self, request, obj, form, change):
        obj_original = self.model.objects.get(pk=obj.pk)
        obj.user = request.user
        obj.save()
```

那么又有问题了，这里如果原来的obj不存在，也就是如果我们是新建的一个怎么办呢，这时候可以用try,except的方法尝试获取,当然更好的方法是判断一下这个对象是新建还是修改，是新建就没有 obj_original，是修改就有

```python
from django.contrib import admin

class ArticleAdmin(admin.ModelAdmin):
    def save_model(self, request, obj, form, change):
        if change:# 更改的时候
            obj_original = self.model.objects.get(pk=obj.pk)
        else:# 新增的时候
            obj_original = None

        obj.user = request.user
        obj.save()
```

**（4） 删除时做一些处理,**

```python
from django.contrib import admin

class ArticleAdmin(admin.ModelAdmin):
    def delete_model(self, request, obj):
        """
        Given a model instance delete it from the database.
        """
        # handle something here
        obj.delete()
```



Django的后台非常强大，这个只是帮助大家入门，更完整的还需要查看官方文档，如果你有更好的方法或不懂的问题，欢迎评论！



# 九、Django表单

作用：（1）自动生成html标签；（2）验证输入内容

```python
class UserForm(forms.Form):
    username = forms.CharField(label='用户名', max_length=100)
    password = forms.CharField(label='密码',widget=forms.PasswordInput())
    email = forms.EmailField(label='电子邮箱')
```

这里label为网页上显示的提示输入的内容标签，widget=forms.PasswordInput()表示输入的内容不可见。

views.py文件中获取表单提交的数据方式如下：

## 1.通过GET方式获取参数

一般使用：var = request.GET.get('var', '')

```python
id = request.GET.get('id'. '')    #id存在就返回，不存在就返回空
name = request.GET.get('name','')     #name存在就返回，否则返回空
```

## 2.通过POST方式获取参数

一般使用： var = request.POST.get('var', '')

```html
<form action='/login/' method='POST'>
	<input type='text' name='username'>
    <input type='password' name='password'>
</form>
```

```python
username = request.POST.get('username','')
password = request.POST.get('password','')
```

## 3.通过自定义的表单POST方式获取参数

表单例子如下：

```python
class UserForm(forms.Form):
    username = forms.CharField(label='用户名', max_length=100)
    password = forms.CharField(label='密码',widget=forms.PasswordInput())
    email = forms.EmailField(label='电子邮箱')
```

在views.py文件中获取参数的方式如下：

```python
from forms import UserForm

def register(request):
	if request.method = 'POST':     # 提交方式是POST
		uf = UserForm(request.POST)
		if uf.is_valid():   #验证提交的数据是否合法
			username = request.POST.get('username','')
			password = request.POST.get('password','')
            # pass #
     else:  # 显示表单内容
        uf = UserForm()
        return render('home.html', {'uf':uf})   
```

## 4.获取表单信息

**表单数据的获取一定要在request.method = 'POST'，if uf.is_valid()内。**

表单信息也可以采用如下方式获取：

```python 
uf = UserForm(request.POST)
if uf.is_valid():   #验证提交的数据是否合法
    username = uf.cleaned_data['username']
    password = uf.cleaned_data['password']
```

## 5.CSRF攻击解决方案

Django对CSRF攻击默认**通过调用CSRF令牌插件**的方式进行了防范，在调用令牌插件之后，每个表单Form里面应该包括一个CSRF令牌，**设置方式是在表单中加入{{  csrf_token  }}**。如果不使用CSRF令牌，可以在setting.py中将其注释掉。

## 6.表单的Widgets





# 十、Django 的配置

## 1.项目所在的目录

在Django 2.1版本的settings.py文件中，第一句为

```python
import os
# Build paths inside the project like this: os.path.join(BASE_DIR, ...)
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
```

```python
os.path.abspath(__file__)    # 表示获取当前文件的路径
```

```python
os.path.dirname()      #表示获取文件的父目录
```

此处的**BASE_DIR**表示项目所在的目录。

## 2.关于DEBUG

在settings.py文件中有下面一段代码：

```python
# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True
```

## 3.关于ALLOW_HOSTS

Django的默认设置方式如下：

```python
ALLOWED_HOSTS = []
```

ALLOWED_HOSTS 允许你设置哪些域名可以访问，即使在 Apache 或 Nginx 等中绑定了，这里不允许的话，也是不能访问的。例如下面的设置：

```python
ALLOWED_HOSTS = ['*.besttome.com','www.ziqiangxuetang.com']
```

当DEBUG = False时，必须对其设置，如果不想输入，可以用 **ALLOW_HOSTS = ['\*']** 来允许所有的。

## 4.静态文件static

static 是静态文件所有目录，比如 jquery.js, bootstrap.min.css 等文件。

```python
STATIC_URL = '/static/'
#STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
```

一般来说我们只要把静态文件放在 APP 中的 static 目录下，部署时用 python manage.py collectstatic 就可以把静态文件收集到（复制到） STATIC_ROOT 目录，但是有时我们有一些共用的静态文件，这时候设置 **STATICFILES_DIRS** ，如下：

```python
STATICFILES_DIRS = (
	os.path.join(BASE_DIR,"static"),
    os.path.join(BASE_DIR,"upload"),
)
```

这样我们就可以把静态文件放在 static 和 upload中了，Django也能找到它们。

当 **DEBUG = True** 时，Django 就能自动找到放在里面的静态文件。

## 5.TEMPLATE_DIRS

有时候有一些模板不是属于app的，比如 baidutongji.html, share.html等，

```python
TEMPLATES = [  
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            os.path.join(BASE_DIR,'templates').replace('\\', '/'),
            os.path.join(BASE_DIR,'templates2').replace('\\', '/'),
        ],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

```

## 6.Database

Django默认使用SQLite数据库

```python
# Database
# https://docs.djangoproject.com/en/1.11/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

如果你不是使用默认的SQLite数据库，那么一些诸如USER，PASSWORD和HOST的参数必须手动指定！下面给出一个基于pymysql操作Mysql数据库的例子：

```python
import pymysql         # 一定要添加这两行！通过pip install pymysql！
pymysql.install_as_MySQLdb()

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mysite',
        'HOST': '192.168.1.1',
        'USER': 'root',
        'PASSWORD': 'pwd',
        'PORT': '3306',
    }
}
```

**注意**：

- **在使用非SQLite的数据库时，请务必预先在数据库管理系统的提示符交互模式下创建数据库，你可以使用命令：CREATE DATABASE database_name;。Django不会自动帮你做这一步工作。**
- 确保你在settings文件中提供的数据库用户具有创建数据库表的权限，因为在接下来的教程中，我们需要自动创建一个test数据表。（在实际项目中也需要确认这一条要求。）
- 如果你使用的是SQLite，那么你无需做任何预先配置，直接使用就可以了。