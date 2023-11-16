@[toc]
# 框架基础介绍
## 模型类
见[16 django框架（上）](https://blog.csdn.net/Micoreal/article/details/132507401)

## 视图
见 [17 django框架（中）](https://blog.csdn.net/Micoreal/article/details/132534787)

## 模板
见 [17 django框架（中）](https://blog.csdn.net/Micoreal/article/details/132534787)

## 其他技术
### 静态文件
#### 静态加载-静态文件
首先就是在项目的setting中添加上下面两句
```python
STATIC_URL = '/abc/'
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]
```

1. 第一行的这一句的意思是我们将这个static文件改名成‘abc’，可以自己修改成别的 
2. 第二行的这一句的意思是，我们对应的static文件所在的位置是BASE_DIR下的static位置

那为了完成静态加载文件，直接按照下文执行即可

```python
<img src="/abc/images/mm.jpg">
```

#### 动态加载-静态文件
就是在html文件的最上面加上{% load static %}
使用的时候直接{% static 'url地址' %}就会替代对应的url

```python
<!DOCTYPE html>
{% load staticfiles %}
<html lang="en">
	<head>
		<meta charset="UTF-8">
		<title>静态文件</title>
	</head>

	<body>
		动态获取 STATIC_URL,拼接静态文件路径:<br/>
		<img src="{% static 'images/mm.jpg' %}">
	</body>
</html>
```

### 中间件
中间件函数是 django 框架给我们预留的函数接口，让我们可以干预请求和应答的过程。

![[image/aa6594c13c0f44acbeabdc09e510c962_MD5.png]]
通过看上面的图，我们会发现，当浏览器访问一个网站服务器的时候，浏览器的请求会先生成一个相对应的request对象，然后调用中间件的Process_request方法，然后进行url匹配，然后调用中间件的process_view方法，然后调用view方法，然后调用中间件的process_response方法，最后执行reponse，也就是说，==如果我们想要让我们的所有的网页在访问的时候都经过某种检测，或者都经过某种筛选，就需要自己去写一个中间件，然后进行配置==，（就比如说我们的csrf中间件，这一部分就是每一个页面都会进行访问的，当进行跳转页面的时候（表单提交），会进行的）。

#### 使用中间件
第一步就是在软件的文件夹下创建一个py文件，业内喜欢的用名为：middleware.py

第二步就是定义中间件（在这个文件中）
定义有两种方式：

方式一：（继承object）

\_\_init\_\_:服务器响应第一个请求的时候调用。
\_\_call\_\_是在产生 request 对象，进行 url 匹配之前调用。
process_view：是 url 匹配之后，调用视图函数之前。
response=self.get_response(request): 视图函数调用之后，内容返回给浏览器之前。

样例：

```python
class TestMiddleware(object):
	'''中间件类'''
	# init函数的self.get_response = get_response 是固定格式，实际上就是为了实现继承父类的get_response的方法
	def __init__( self, get_response ):
		print('---init---')
		self.get_response = get_response
	
	# call函数的格式也如下，下文也大概写了process_request 和 process_response执行的位置，然后process_view执行的位置实际上就是在调用self.get_response(request)方法的地方，然后在里面内部也调用了我们这边写的process_view方法。
	def __call__(self, request):
		'''产生 request 对象之后，url 匹配之前调用'''
		print('----process_request----')
		response=self.get_response(request)
		# 视图函数调用之后，内容返回浏览器之前
		print('------response------')
		return response
	
	# 和前面一样所有函数的定义都需要满足这种格式，view_func, *view_args,**view_kwargs代表接收这三种格式的传值，这一块我们可以进行替换成自己定义的变量。
	def process_view(self, request, view_func, *view_args,**view_kwargs):
		'''url 匹配之后，视图函数调用之前调用'''
		print('----process_view----')
		# view 视图函数没有得到执行，但是还是要走 process_response
```


方式二（继承MiddlewareMixin）：

```python
与上面的那种方式的重要区别就是这种方式比较明显。
class TestMiddleware2(MiddlewareMixin):
    def __init__( self, get_response ):
        print('---init2---')
        self.get_response = get_response

    def process_request(self, request):
        print('----process_request2----')

    def process_response(self, request, response):
        print('------process-response2------')
        return response

    def process_view(self, request, view_func, *view_args, **view_kwargs):
        '''url匹配之后，视图函数调用之前调用'''
        print('----process_view2----')
```

第三步就是注册中间件：
实际上就是到setting文件夹下的最后面加上我们自己的中间件
![[image/9e2a55c3b2cd7bac11bbf4f81c1d3b88_MD5.png]]

### admin后台管理-基础使用
关于这一部分，就将比较简单的，这一部分一般都是用别人的 第三方插件，所以了解即可

我们一般在准备后台管理的时候，也会一并设置时区等一系列操作，所以这里一并写了：

第一步：设置语言和时间本地化
在setting.py当中进行修改成如下代码：

```python
LANGUAGE_CODE = 'zh-hans'
TIME_ZONE = 'Asia/Shanghai'

这一块原本是True 用于自动感知ip 从而自动调整时间
USE_TZ = False
```

第二步：创建超级用户
python manage.py createsuperuser
然后系统会提示你输入账号密码进行注册

第三步：注册模型类
实际上就是在models.py文件中，写上你对应的数据，并且重写str方法，str方法关乎到你后面显示的名字，这边给出我写的例子：

```python
class AreaInfo(models.Model):
	'''地址模型类'''
	# 地区名称
	atitle = models.CharField(verbose_name='标题', max_length=20)
	# 自关联属性
	aParent = models.ForeignKey('self', null=True, blank=True,on_delete = models.CASCADE,)
	
```

第四步：自定义管理类

形式一（显示的是str方法传回的数据）：
这时我们在 admin.py 中添加admin.site.register(AreaInfo)即可在管理页面看到对应表的数据信息。

形式二 注册管理类
这时我们在 admin.py 中添加 admin.site.register(AreaInfo，AreaInfoAdmin)即可在管理页面看到对应表的数据信息。
具体样式见下文介绍

#### 模型管理类的具体属性

```python
class AreaInfoAdmin(admin.ModelAdmin):
	'''地区模型管理类'''
	list_per_page = 10 # 指定每页显示 10 条数据
	#方法名也可以作为一列进行显示
	list_display = ['id', 'atitle', 'title', 'parent']
	actions_on_bottom = True # 底部显示动作窗口
	actions_on_top = False #顶部不显示动作窗口
	list_filter = ['atitle'] # 列表页右侧过滤栏
	search_fields = ['atitle'] # 列表页上方的搜索框
```

### 上传图片
第一步：创建文件保存位置 我这边是在static文件夹下创建一个media文件夹，然后内部再创建我的应用名称

![[image/b375dc481b5b199799bf288527da98b6_MD5.png]]

第二步：配置配置文件
在setting文件中加上meida的路径位置

```python
MEDIA_ROOT=os.path.join(BASE_DIR,'static/media')
```

第三步：在model文件下创建相对应的类，也就是在数据库当中存储这些图片（路径）

```python
class PicTest(models.Model):
	'''上传图片'''
	goods_pic = models.ImageField(upload_to='booktest')
```
在这种情况下，upload_to='booktest’指定了文件的保存路径为 "media/booktest"，其中"media"是设置中定义的媒体文件夹，也就是我们上文定义的MEDIA_ROOT。如果我们上传一个名为"image.jpg"的文件，它将被保存为"media/booktest/image.jpg"。

第四步：使用admin进行测试一下，是否能进行上传文件等功能
在 admin.py 中添加

```python
admin.site.register(PicTest)
```

上传完毕后在 media 的 booktest 下就可以看到对应图片

上述步骤都完成之后，就可以开始后端上传 以及显示图片到前端的操作
#### 用户上传 图片
给个例子：

第一步：新建 upload_pic.html

```python
<form method="post" enctype="multipart/form-data" action="/upload_handle/">
	{% csrf_token %}
	<input type="file" name="pic"><br/>
	<input type="submit" value="上传">
</form>
```

第二步：定义接收上传文件的视图函数view

```python
def upload_handle(request):
	'''上传图片处理'''
	# 1.获取上传文件的处理对象 这边的pic对应的是上传表单中的pic
	pic = request.FILES['pic']
	
	# 2.创建一个文件
	save_path = '%s/booktest/%s'%(settings.MEDIA_ROOT,pic.name)
	with open(save_path, 'wb') as f:
		# 3.获取上传文件的内容并写到创建的文件中
		for content in pic.chunks():
			f.write(content)

	# 4.在数据库中保存上传记录 这边的create是我使用管理类进行重写的
	PicTest.objects.create(goods_pic='booktest/%s'%pic.name)
	
	# 5.返回
	return HttpResponse('ok')
```

#### 显示图片
第一步：创建相关的视图

```python
from booktest.models import PicTest

def pic_show(request):
	pic=PicTest.objects.get(id=3)
	context={'pic':pic}
	return render(request,'booktest/pic_show.html',context)
```

第二步：配置相关的url

第三步：模板文件夹下创建相关的html

```python
<html>
	<head>
		<title>显示上传的图片</title>
	</head>
	<body>
		<img src="/static/media/{{pic.goods_pic}}"/>
	</body>
</html>
```

### 分页
Paginator 类对象的属性：
|属性名| 说明|
|-|-|
|num_pages| 返回分页之后的总页数|
|page_range| 返回分页后页码的列表|

Paginator类对象的方法:
|方法名| 说明|
|-|-|
|page(self, number)| 返回第 number 页的 Page 类实例对象|

Page 类对象的属性：
|属性名| 说明|
|-|-|
|number| 返回当前页的页码|
|object_list| 返回包含当前页的数据的查询集|
|paginator| 返回对应的 Paginator 类对象|

Page 类对象的方法：
|属性名| 说明|
|-|-|
|has_previous| 判断当前页是否有前一页|
|has_next| 判断当前页是否有下一页|
|previous_page_number| 返回前一页的页码|
|next_page_number| 返回下一页的页码|


使用示例：

html文件show_areas.html

```python
<!DOCTYPE html>
<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>显示地区</title>
	</head>
	
	<body>
		{% for area in page %}
		    <li>{{ area.atitle }} </li>
		{% endfor %}
		
		{# 判断是否有上一页 #}
		{% if page.has_previous %}
		    <a href="/show_area/{{ page.previous_page_number }}">&lt;上一页</a>
		{% endif %}
		
		{# 遍历显示页码的链接 #}
		{% for pindex in page.paginator.page_range %}
		    {# 判断是否是当前页 #}
		    {% if pindex == page.number %}
		        <strong>{{ pindex }}</strong>
		    {% else %}
		        <a href="/show_area/{{ pindex }}">{{ pindex }}</a>
		    {% endif %}
		{% endfor %}
		
		{# 判断是否有下一页 #}
		{% if page.has_next %}
		    <a href="/show_area/{{ page.next_page_number }}">下一页&gt;</a>
		{% endif %}
		
		<span>总页数 {{ page.paginator.num_pages }}</span>
		
	</body>
</html>
```

view方法：

```python
from django.core.paginator import Paginator

def show_area(request,pindex=1):
    areas = Areas.objects.filter(aParent__isnull=True)

    # 2. 分页,每页显示10条
    paginator = Paginator(areas, 10)

    # 3. 获取第pindex页的内容
    pindex = int(pindex)
    # page是Page类的实例对象
    page = paginator.page(pindex)

    # 4.使用模板
    return render(request, 'booktest/show_area.html', {'page':page})
```

第三步：urls配置

```python
    path('show_area/',views.show_area),
    path('show_area/<int:pindex>',views.show_area),
```





