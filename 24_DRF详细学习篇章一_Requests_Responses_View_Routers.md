@[toc]
# 快速入门你可能搞混的知识
## 重写preform_ 与重写save
![[image/0047811f858626a87a63897f2e6269c0_MD5.png]]

回想一下我们之前什么时候用到了这两个？

关于前者，我们重写了preform_create这个方法（在view当中），我们的目的是，在运行的时候自动获取账户的名称，然后加入我们的json当中传回数据，并保存到数据库当中，所以我们使用了preform_create也就是为了在真正的POST请求到来，你传入的数据多这么一个，这个流程是先于后者的save的。


而对于save来说，我们是得到了前端传回的所有数据（包括那个owner）之后，我们要进行存入数据，如果不写就直接进行存储，如果重写这个函数，就可以先进行数据分析，然后再存入数据，整个流程是这样子的。

这二者的关键理解在于是否理解好ORM，依赖倒置原则的处理流程，根据不同情况选择不同的函数进行重写，就是面向切片变成的重要思想，了解切片变成重点了解的就是其运行的流程图。


# Requests 请求
源代码位于：request.py

对于DRF的request对象，有下面的主要属性：
1. .data
2. .query_params
3. .parsers
4. .accepted_renderer
5. .accepted_media_type
6. .user
7. .auth
8. .authenticators
9. .method
10. .content_type
11. .stream

后面我们会对这些进行一一介绍，并且标注重要星级：

重要程度分级：
1. 不重要：（⭐）
2. 了解：（⭐⭐）
3. 重点了解（⭐⭐⭐）
## 原本的Django的request （⭐）
![[image/2f2d7649ac31fd1cc8ddd0d8289c41da_MD5.png]]
我们到：使用了request的create当中进行调试：
![[image/e30f86c28d0feedb7700cb33b2c6de54_MD5.png]]
然后前端随便传入一个数据上去:
![[image/e9225717433951c00359969086b38b3e_MD5.png]]
然后在调试窗口可以进行查看，稍微玩个花火，实际上drf框架的request实际上已经将Django的request中的内容进行调到了自己的内部了，也没太必要去看这一步，除非你是修改框架的人士。OwO

## .data (⭐⭐⭐)
\.data：提交的数据都在data当中，比较类似的是request.post

先给出我刚刚提交的data：
![[image/8d1d0456780cb3c0168c8c77f3452be1_MD5.png]]
调试窗口，可以看到内部数据都在这里：
![[image/39748f8d60988f8227dccfdf45559e42_MD5.png]]
request.data 返回请求正文的分析内容。这与标准 request.POST 和request.FILES 属性类似，不同之处在于：
1. 它包括所有解析的内容，包括文件和非文件输入。
2. 它支持解析 POST HTTP 方法的内容，这意味着您可以访问 和 PATCH 请求PUT 的内容。
3. 它支持 REST 框架的灵活请求解析，而不仅仅是支持表单数据。例如，您可以像处理传入表单数据一样处理传入的 JSON 数据。

## .query_params (⭐⭐⭐)
request.query_params 是 的 request.GET 更正确命名的同义词，实际上他内部的参数是浏览器上方的？page=2，这种类型的参数

这边用换页进行展示：
因为类似的是Get，所以我们这边选择到list进行查看（你也可以到RetrieveModelMixin进行查看）。
![[image/d90835fc2ba6c860359bdb08ed0f106d_MD5.png]]
![[image/ecb2200df28a7ecbcb1d8f4244417d53_MD5.png]]
![[image/cc027c5d6c3b65f13232fe539ce04b7a_MD5.png]]

如此自然可以看到这个参数代表的含义。


## .parsers (⭐)
APIView 类或 @api_view 装饰器将根据view中设置的 parser_classes 值或DEFAULT_PARSER_CLASSES 配置参数进行设置，确保此属性自动设置为Parser 实例列表。通常并不需要访问这个属性。

![[image/839c08ad93cbd08b3485465d56a2c01b_MD5.png]]
实际上这三个就是解析器，是按照这三个的顺序分别进行解析数据的，比如我们在浏览器中的末尾加上.json，那么就会按照json的格式传递给我们数据，原因就是在这里调用了JSONParser 渲染器，html同理。

注意：如果客户端发送格式不正确的内容，则访问 request.data 可能会引发ParseError .默认情况下， APIView REST 框架的类或 @api_view 装饰器将捕获错误并返回 400 Bad Request 响应。

解析器（media type），实际上就是传入数据的格式，比如你选择了json，那么他就会按照json的格式去识别你的content，并解析，然后再传到data当中：
![[image/46a7b22dd81337d45cd2968a19991537_MD5.png]]


## .accepted_renderer (⭐⭐)
内容协商阶段选择的呈现器实例。

![[image/49adc64262c1cf19c8454ccd1fb19daa_MD5.png]]
## .accepted_media_type (⭐⭐)
![[image/1449f656d5f74feab84c8deb2929e2fa_MD5.png]]
这个就是我们返回的数据是什么格式的，现在我们返回的不都是json的数据，也就是都是字符串，后续也可能包括图片什么的，这边就会进行改变


## .user (⭐⭐⭐)
顾名思义，也就是显示你当前登录的账户的user，一般是需要利用这个user做身份验证。
![[image/0e99ca985e65c8fc542c810e3648ee69_MD5.png]]
如果请求未经身份验证，则默认值为 ：

```python
request.user = django.contrib.auth.models.AnonymousUser
```

## .auth (⭐⭐⭐)
这边只进行展示，虽然很重要，但详情的介绍等待之后，进行揭晓：

request.auth 返回任何其他身份验证上下文。的确切行为取决于所使用的身份验证策略，但它通常可能是请求所针对的令牌的 request.auth 实例。

如果请求未经身份验证，或者不存在其他上下文，则缺省 request.auth 值为 None 。

![[image/5c630ab09088ebad95d85236603faeec_MD5.png]]

## .authenticators (⭐)
返回的就是验证身份的方法，这一部分也到后面再进行讲解：

![[image/08a95d977735aea7b1964c9b261012f3_MD5.png]]
值得关注的是这边出现了Session，学过Django的我们自然懂得相关的属性含义

APIView 类或修饰器将确保此属性根据视图上的 authentication_classes 设置或 @api_view 基于 DEFAULT_AUTHENTICATORS 设置自动设置为实例列表 Authentication 。

通常不需要访问此属性

## .method (⭐⭐⭐)
request.method 返回请求的 HTTP 方法的大写字符串表示形式。

这边就不进行演示了，如果想看这个method的方法，就不能再我们刚刚那个打的断点的位置进行查看，需要到上一级，也就是到request.py里面进行打断点进行查看，这边大家肯定懂这个的意思，这边就不详细说明OvO，懒~~

不过很重要哈
## .content_type (⭐)
![[image/247f6315672dd1923ca810875a28033c_MD5.png]]
返回一个字符串对象，表示 HTTP 请求正文的媒体类型，如果未提供媒体类型，则返回空字符串，一般不使用。

## .stream (⭐)
![[image/1516ffba665067494cd121fd84a83cb6_MD5.png]]
request.stream 返回表示请求正文内容的流。

一般不使用。

# Responses
这一块其实不是很重要，就不带领大家一步步调试理解了，这边就列几个常见的，可能常用的进行了解

如下表建立起来，但是一般来说前两个记忆一下即可。

|参数|说明|
|-|-|
|data |响应的序列化数据，或者说就是后端要传给前端的数据|
|status |响应的状态代码。默认值为 200。|
|template_name |选择 HTMLRenderer 时要使用的模板名称。|
|headers |要在响应中使用的 HTTP 标头的字典。|
|content_type |响应的内容类型。通常，这将由呈现器根据内容协商确定自动设置，但在某些情况下可能需要显式指定内容类型。|

# View
还是这张图，很重要哈：
![[image/02aa5c0fcdb8b603682e8c48c62786e0_MD5.png]]
经过了快速入门之后，这边再进行系统的介绍一遍方便查缺补漏，进行理解：
## APIView、MinixView、GenericAPIView
这三个类就进行跳过了，详情见我的快速入门
[链接](https://blog.csdn.net/Micoreal/article/details/132672779)

### Minix具体的通用类视图

|类|说明|
|-|-|
|CreateAPIView|仅用于创建功能的视图，只提供post方法|
|ListAPIView|以只读的方式列出某些查询对象的集合。提供list方法|
|RetrieveAPIView|以只读的形式获取某个对象。提供get 方法|
|DestroyAPIView|删除单个模型实例。提供delete方法。 |
|UpdateAPIView|更新单个模型实例。提供 put和 patch方法。|
|ListCreateAPIView|创建或者列出模型实例的集合。提供get和list方法。同时继承了ListModelMixin和CreateModelMixin两个mixin类，以及基类 GenericAPIView。|
|RetrieveUpdateAPIView|读取或更新单个模型实例。提供get和 put 和patch方法|
|RetrieveDestroyAPIView|读取或删除单个模型实例。提供get和 delete方法。|
|RetrieveUpdateDestroyAPIView|读写删除单个模型实例。提供 get, put, patch和delete方法|

## ModelViewSets
这边进行回想一下，在GenericAPIView下我们view的代码是不是已经写好了正常的list页面和detail页面，而我们这里的ModelView就是基于那一个的基础上进行增加的，或者这么说，我们前面展示的页面都是对于（/user/ 或者 /user/1/），但此时我们想要更加多的页面的时候，该怎么办？

最基础的形态：

```python
class UserViewSet(viewsets.ModelViewSet):
    """
    A viewset for viewing and editing user instances.
    """
    serializer_class = UserSerializer
    queryset = User.objects.all()
```
urls：

```python
from myapp.views import UserViewSet
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register(r'users', UserViewSet, basename='user')
urlpatterns = router.urls
```

在这个最基础的效果之下，我们的基础的list 和 detail页面的get post put delete等一系列操作就已经写好了，但此时我们想像快速入门一样，在原本的url的基础上进行添加新的页面，此时就需要引入action的装饰器操作。

下面先介绍ModelViewSet下的一些基础属性（不是很重要，了解即可）：

|属性|说明|
|-|-|
|basename|根url路径|
|action | 当前操作的名称（例如， list 、 create ）。|
|detail | 布尔值，指示当前操作是否配置为列表或详细信息视图。|
|suffix | viewset类型的前缀|
|name |viewset的名字|
|description| 视图集的单个视图的显示说明|


为了方便展示，这边把代码修改成这样子：

```python
class UserViewSet(viewsets.ReadOnlyModelViewSet):
    """
    This viewset automatically provides `list` and `retrieve` actions.
    """
    queryset = User.objects.all()
    serializer_class = UserSerializer

    @action(detail=False, methods=['get'])
    def A(self, request):
        return Response({'status': 'A'})

    @action(detail=True, methods=['get', 'post'])
    def B(self, request, pk):
        return Response({'status': 'B'})
```
把断点打在A函数的return上，然后访问http://127.0.0.1:8000/users/A/

此时就可以看出action实际上就是访问的网页的页名，或者说是函数的名称：
![[image/4e88339084c202a1cd93e4cc3a4cdc94_MD5.png]]
basename就是类名，或者说是
![[image/5ecc886f29f5d454481b7e7546b844af_MD5.png]]
![[image/9096eccf7ff59e472c7499f318093b83_MD5.png]]
也等价于：
![[image/470c30775afcc73b7cf428c35422a038_MD5.png]]
detail：判断是list的子页面还是detail的子页面
![[image/2479c420efdfb1d79feb0a9c685c60cb_MD5.png]]
suffix就是后缀，这边没有显示，或者可以说就是那个html
![[image/d5f2c6884d71c24c618568131507a42b_MD5.png]]

name
是一整个名字，比如这边就是A
![[image/b9d92a332eb4557cb5785c41834f60ee_MD5.png]]

### 创建新的页面
![[image/07cd4e98d6e77698a64bf32495c0e05b_MD5.png]]
当然还有一些特定化的操作：

```python
修饰器允许您覆盖任何视图集级别的配置，例如 permission_classes 、、 serializer_class filter_backends ...：


    @action(detail=True, methods=['post'], permission_classes=[IsAdminOrIsSelf])
    def set_password(self, request, pk=None):
       ...
```

### 路由其他 HTTP 方法以进行额外操作
额外的操作可以将其他 HTTP 方法映射到单独的 ViewSet 方法。例如，上述密码设置/取消设置方法可以合并为单个路由。请注意，其他映射不接受参数。

```python
	# name=“change password”只是修改名字，并不是修改访问的url，此时还是需要使用password进行访问的
    @action(detail=True, methods=['put'], name='Change Password')
    def password(self, request, pk=None):
        """Update the user's password."""
        ...

    @password.mapping.delete
    def delete_password(self, request, pk=None):
        """Delete the user's password."""
        ...
```
![[image/458da110e49e851ab651e74891ed41f1_MD5.png]]
换到html当中就是其的title
### 获取url
如果需要获取操作的 URL，请使用该方法 .reverse_action() 。这是一个方便的包装器 reverse() ，会自动传递视图 request 的对象并在 前面 url_name 加上 .basename 属性。

请注意，路由器在注册期间 ViewSet 提供。 basename 如果不使用路由器，则必须为 .as_view() 该方法提供 basename 参数。

```python
>>> view.reverse_action('set-password', args=['1'])
'http://localhost:8000/api/users/1/set_password'
```

# Routers
常见的Router函数：

```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from snippets import views

# 路由器
# Create a router and register our viewsets with it.
router = DefaultRouter()
router.register(r'snippets', views.SnippetViewSet, basename="snippet")
router.register(r'users', views.UserViewSet, basename="user")

# The API URLs are now determined automatically by the router.
urlpatterns = [
    path('', include(router.urls)),
]
```

## 常见参数的含义
该方法 register() 可接收下面的几个常用的参数：

|参数|说明|
|-|-|
|prefix|  用于这组路由的 URL 前缀，比如这边就是‘’空，实际上访问的话，就是由路由器自动生成‘’+你对应的类名加后续|
|viewset | 视图集类。|
|basename | 用于创建的 URL 名称的基础。如果未设置，将根据视图集 queryset 的属性自动生成基本名称（如果有）。==请注意，如果视图集不包含 queryset 属性，则必须在注册视图集时进行设置 basename 。==|

## 关于include

```python
urlpatterns = [
    path('forgot-password/', ForgotPasswordFormView.as_view()),
    path('api/', include((router.urls, 'app_name'), namespace='instance_name')),
]
```
又出现两个参数：
|参数|说明  |
|--|--|
| app_name |这个是为了避免由于一个项目拥有多个app，这边就需要添加上对应的app_name，可以随便取名，但是后面在调用包含在这个app的对应的方法的时候，就需要使用app_name:view_name ，下面会给个例子 |
|namespace|这个一般用于版本控制，这个后面再提|


## DefaultRouter 默认路由器
默认路由包括一个默认的 API 根视图，该视图返回包含指向所有列表视图的超链接的响应。它还为可选的 .json 样式格式后缀生成路由，一般直接使用这个即可，其他的自定义类型的就不定义了。

![[image/71f35a6ddc9d20b5a5875bf10845a744_MD5.png]]



