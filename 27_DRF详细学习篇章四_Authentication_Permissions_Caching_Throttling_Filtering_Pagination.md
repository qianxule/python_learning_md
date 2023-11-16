@[toc]

# Authentication
关于身份认证，这边只给出相关的操作，但是不细说，原因就是这些实际上需要搭配着实例进行理解，这边只介绍操作，以及稍微介绍一下相关的区别。
## 设置身份验证方案
可以使用该 DEFAULT_AUTHENTICATION_CLASSES 设置全局设置默认身份验证方案（一般采用这一种）。例如：

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
       	······这边加入你的登录的方案··················
    ]
}
```
还可以使用基于类的 APIView 视图，基于每个视图或每个视图集设置身份验证方案。

```python
from rest_framework.authentication import SessionAuthentication, BasicAuthentication
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    authentication_classes = [SessionAuthentication, BasicAuthentication]
    permission_classes = [IsAuthenticated]

    def get(self, request, format=None):
        content = {
            'user': str(request.user),  # `django.contrib.auth.User` instance.
            'auth': str(request.auth),  # None
        }
        return Response(content)
```

## 未经授权和禁止的响应
可能产生下面两种禁止状态
```python
HTTP 401 Unauthorized 
HTTP 403 Permission Denied
```

## BasicAuthentication
最基础的登录，只有登录按钮，登录也仅仅只能在本次使用，当我们换页面或者关闭浏览器再打开，登录状态就消失了。

这个一般也不会去使用，只是做一个基类进行继承，这边就不进行介绍此种登录的操作了。

如果身份验证成功， BasicAuthentication 则提供以下凭据：

1. request.user 将是一个 Django User 实例。
2. request.auth 将是 None .
## SessionAuthentication

Session登录，这边值得关注的就是这是采用传递cookie进行登录，传递一个session-id作为登录的标识，并存储到数据库当中，作为登录凭证，时间限制在2周。

如果身份验证成功， SessionAuthentication 则提供以下凭据：

1. request.user 将是一个 Django User 实例。
2. request.auth 将是 None .

如果您将 AJAX 样式的 API 与 SessionAuthentication 一起使用，则需要确保为任何“不安全”的 HTTP 方法调用（如 PUT 、 PATCH POST 或 DELETE 请求）包含有效的 CSRF 令牌

## RemoteUserAuthentication
此身份验证方案允许您将身份验证委托给 Web 服务器，该服务器设置环境 REMOTE_USER 变量。

要使用它，您的 AUTHENTICATION_BACKENDS 设置中必须包含 django.contrib.auth.backends.RemoteUserBackend （或子类）。默认情况下， RemoteUserBackend 为尚不存在的用户名创建 User 对象。

如果身份验证成功， RemoteUserAuthentication 则提供以下凭据：

1. request.user 将是一个 Django User 实例。
2. request.auth 将是 None .


## TokenAuthentication
这个token诞生的原因实际上是为了解决session登录的不足，我们在具体具体的使用环境中，session只能满足存储在一个服务器当中，但是真实的情况往往一个网站部署之下对应着不止一个服务器，有多个服务器，也就是当我们第一次访问这个页面登录了，这台服务器传递过来一个session-id，但是你下一次点击，你访问同一个网站，但是网络给你分配到另一个服务器下的这个网页，那你的session不在这个服务器下存储，这就产生了问题，token就直接从数学的产生字符串的角度解决了这个问题，高并发的情况下。

若要使用该 TokenAuthentication 方案，需要将身份验证类配置为包括 TokenAuthentication ，并在 INSTALLED_APPS 设置中另外包含 rest_framework.authtoken ：

```python
INSTALLED_APPS = [
    ...
    'rest_framework.authtoken'
]
```
确保在更改设置后运行 manage.py migrate ，该应用程序rest_framework.authtoken 会将 Django 数据库迁移。

您还需要为用户创建令牌：

```python
from rest_framework.authtoken.models import Token

token = Token.objects.create(user=...)
print(token.key)
```

对于要进行身份验证的客户端，令牌密钥应包含在 Authorization HTTP 标头中。键应以字符串文本“Token”为前缀，两个字符串之间用空格分隔。例如：

```python
Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b
```

如果要在标头中使用其他关键字，例如 Bearer ，只需子类并设置 keyword 类 TokenAuthentication 变量即可。


如果身份验证成功， TokenAuthentication 则提供以下凭据：

request.user 将是一个 Django User 实例。
request.auth 将是一个 rest_framework.authtoken.models.Token 实例。

被拒绝权限的未经身份验证的响应将导致具有相应 WWW 身份验证标头的 HTTP 401 Unauthorized 响应。例如：

```python
WWW-Authenticate: Token
```

# Permissions
## 设置权限策略
### 设置全局权限
可以使用该 DEFAULT_PERMISSION_CLASSES 设置全局设置默认权限策略。例如：

```python
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ]
}
```

如果未指定，此设置默认为允许无限制访问：

```python
'DEFAULT_PERMISSION_CLASSES': [
   'rest_framework.permissions.AllowAny',
]
```


### 设置局部权限
局部权限一般分为两种：

第一：

```python
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
```


第二种：

```python
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response

@api_view(['GET'])
@permission_classes([IsAuthenticated])
def example_view(request, format=None):
    content = {
        'status': 'request was permitted'
    }
    return Response(content)
```


## 常见的权限
### AllowAny
AllowAny 权限类将允许不受限制的访问，无论请求是经过身份验证还是未经身份验证


### IsAuthenticated
IsAuthenticated已经登录的，只有登录的用户才能进行后面的操作


### IsAdminUser
IsAdminUser 管理员用户才允许后面的操作


### IsAuthenticatedOrReadOnly
IsAuthenticatedOrReadOnly 登录的或者只读，讲人话就是只有登录用户才能post put delete 不登陆的用户仅能进行查看


## 自定义权限
新建一个Permissions.py，在里面写权限类

若要实现自定义权限，请重写 BasePermission 并实现以下方法之一或两者：

1. .has_permission(self, request, view)
2. .has_object_permission(self, request, view, obj)

下面是一个权限类示例，该权限类根据阻止列表检查传入请求的 IP 地址，并在 IP 被阻止时拒绝请求：

```python
from rest_framework import permissions

class BlocklistPermission(permissions.BasePermission):
    """
    Global permission check for blocked IPs.
    """

    def has_permission(self, request, view):
        ip_addr = request.META['REMOTE_ADDR']
        blocked = Blocklist.objects.filter(ip_addr=ip_addr).exists()
        return not blocked
```


# Caching 缓存
缓存实际上作用在二次查询的时候，首次查询甚至会比原本的查询还慢（因为需要写操作，写入缓存），drf当中的默认缓存是用一个简单的字典进行存储的，（模拟redius），我们这边，对于知识的介绍，所以就采用业内常用的redius进行介绍。

![[image/7cf92d4e551a607c6c114c4e307e2b43_MD5.png]]
他们之间的关系大概按照上图，从图中我们可以明显看出Redius的缓存具有滞后性，也就是在数据更新后，redius当中的数据并没有随之更新，所以对于这个非关系型数据库当中的数据，需要我们手动删除。

Django 提供了一个 method_decorator 使用带有基于类的视图的装饰器。这可以与其他缓存修饰器一起使用，例如 cache_page 和 vary_on_cookie vary_on_headers 。

```python
from django.utils.decorators import method_decorator
from django.views.decorators.cache import cache_page
from django.views.decorators.vary import vary_on_cookie,vary_on_headers

from rest_framework.response import Response
from rest_framework.views import APIView
from rest_framework import viewsets


class UserViewSet(viewsets.ViewSet):
    # With cookie: cache requested url for each user for 2 hours
    @method_decorator(cache_page(60*60*2))
    @method_decorator(vary_on_cookie)
    def list(self, request, format=None):
        content = {
            'user_feed': request.user.get_user_feed()
        }
        return Response(content)


class ProfileView(APIView):
    # With auth: cache requested url for each user for 2 hours
    @method_decorator(cache_page(60*60*2)) # 2小时缓存时间
    @method_decorator(vary_on_headers("Authorization",))
    def get(self, request, format=None):
        content = {
            'user_feed': request.user.get_user_feed()
        }
        return Response(content)


class PostView(APIView):
    # Cache page for the requested url
    @method_decorator(cache_page(60*60*2))
    def get(self, request, format=None):
        content = {
            'title': 'Post title',
            'body': 'Post content'
        }
        return Response(content)
```

# Throttling 限流
限流的作用首先就是防止爬虫，以及一些暴力访问，维护网站的稳定性，在drf当中的限流首先坐的事情就是类似pessmission的事情一样，或者说就是另类的认证。

## 全局设置
这种一般用的少，一般都是个性化设置，不过这边也列出来

可以使用 DEFAULT_THROTTLE_CLASSES 和DEFAULT_THROTTLE_RATES 设置全局设置默认限制策略。

例如：（在setting当中进行设置）

```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle'
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '1/sec',
        'user': '1000/day'
    }
}
```

简要介绍一下上面的那些术语：

|术语|说明  |
|--|--|
| AnonRateThrottle |未登录用户的限制，一般和‘anon’联合使用，来进行限制|
|UserRateThrottle|登录用户的限制，一般和‘user’进行联合使用，来进行限制|
|anon|对应的就是AnonRateThrottle|
|user|对应的就是UserRateThrottle|
|day|时间单位，相同的时间还有sec hou min 取的都是英语单词的前三个字母|


## 局部设置

还是分为两种，一种是类视图 ，一种式api/函数视图


第一种类视图：这个式关于登录用户的限流，然后限流的具体参数在setting当中进行书写

```python
'DEFAULT_THROTTLE_RATES': {
        'user': '1000/day'
    }
```

```python
from rest_framework.response import Response
from rest_framework.throttling import UserRateThrottle
from rest_framework.views import APIView

class ExampleView(APIView):
    throttle_classes = [UserRateThrottle]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
```

第二种函数视图：

```python
@api_view(['GET'])
@throttle_classes([UserRateThrottle])
def example_view(request, format=None):
    content = {
        'status': 'request was permitted'
    }
    return Response(content)

或者：

@action(detail=True, methods=["post"], throttle_classes=[UserRateThrottle])
def example_adhoc_method(request, pk=None):
    content = {
        'status': 'request was permitted'
    }
    return Response(content)
```

## 限流是如何取识别用户的
一般默认使用 WSGI 环境中变量的值 REMOTE_ADDR的这个属性进行识别用户，也就是drf当中的request.Meta.REMOTE_ADDR这个属性也就是访问网站的ip地址进行限制。

当设置了X-Forwarded-For的话，就是采用这个方法进行检测，但是这个一般好像有点问题，（笔者暂时不知道），这个是通过转发前的ip进行检测。

## 自定义设置限流
上文你会发现，即使是局部的限制，我们仍然没办法做到一个页面设置这个页面一分钟最多访问2次，下一个页面一分钟访问3次这样子的效果，这时候就需要用上自定义的访问了

操作也有两种，推荐方式二，操作少，OwO：

### 方式一
首先先新建一个Throttle.py，内部专门写这个限流类

然后写上：aaa是随便的名字，一般和你要限制的相关

```python
class BurstRateThrottle(UserRateThrottle):
    scope = 'aaa'

```

全局设置就先跳过了，感觉不是很适合，直接开始介绍局部限制
```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_RATES': {
        'aaa': '60/min',
    }
}
```
然后到对应的视图当中写上：

```python
class ExampleView(APIView):
    throttle_classes = [BurstRateThrottle]

    def get(self, request, format=None):
        content = {
            'status': 'request was permitted'
        }
        return Response(content)
```

### 方式二
当然还是局部设置

先到setting当中加上'rest_framework.throttling.ScopedRateThrottle'，这个就是用于此处的，然后就可以在后面的设置当中加上自定义的label和对应的时间设置。
```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.ScopedRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'bbb': '1000/day',
    }
}
```

然后我们在调用具体的view视图的时候：

```python
class ContactListView(APIView):
    throttle_scope = 'bbb'
```


就可以直接得到了。



# Filtering
首先先检查有没有django-filter这个包，有了才能进行后续的操作，没有就安装一下。
![[image/9bc0d59b6e9340894c64e4c0982cb1c5_MD5.png]]
## 通用过滤

首先的操作就是，到setting当中添加上

```python
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ['django_filters.rest_framework.DjangoFilterBackend']
}
```

以及应用上添加上‘django_filter’

然后就是在view当中导入：

```python
from rest_framework import filters

要在视图类中添加（同时注意要把django_filters在settings中配置）
filter_backends = [DjangoFilterBackend,filters.SearchFilter,filters.OrderingFilter]
filterset_fields = '__all__'
search_fields = ['order','title','album__album_name'] #外键加入两个下划线
ordering_fields = '__all__'
```
然后根据自己的实际去尽心修改即可

然后实际上的过滤就是在url后面加上这些东西。
![[image/92bd17827875fbb50d2a499d4023caed_MD5.png]]
# Pagination
我们之前就已经使用过了，这边就介绍一点不一样的。


## PageNumberPagination
首先就是我们之前使用的

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 2,
}
```
![[image/a87a45d5882e15bcbfa7182d39779c59_MD5.png]]
## LimitOffsetPagination

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'PAGE_SIZE': 2,
}
```

![[image/788f377248c34ba02133600ba883c9e6_MD5.png]]
## 局部设置分页
就把上面的那个注释掉，转到view函数当中

```python
pagination_class = LimitOffsetPagination
```
就把需要分页的框起来就可以

## 自设置分页样式

自定义的分页格式，先创建一个新的PaginationClass.py

在这个文件中写入你想要的参数

```python
class StandardResultsSetPagination(PageNumberPagination):
    page_size = 2
    page_size_query_param = 'page_size' # 这个主要用于前端传递的可指定参数，后面进行演示
    max_page_size = 1000
```


然后想要调用成局部的：

```python
class BillingRecordsView(generics.ListAPIView):
    queryset = Billing.objects.all()
    serializer_class = BillingRecordsSerializer
    pagination_class = StandardResultsSetPagination
```
想要调用成全局的：

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'apps.core.pagination.StandardResultsSetPagination'
}
```


以及可以修改的属性：

若要设置这些属性，应重写 PageNumberPagination 该类，然后启用自定义分页类，如上所示。

1. page_size - 指示页面大小的数值。如果设置，这将覆盖该 PAGE_SIZE 设置。默认为与 PAGE_SIZE 设置键相同的值。
2. page_query_param - 一个字符串值，指示要用于分页控件的查询参数的名称。
3. page_size_query_param - 如果设置，则这是一个字符串值，指示允许客户端基于每个请求设置页面大小的查询参数的名称。默认值为 None ，表示客户端可能无法控制请求的页面大小。
4. max_page_size - 如果设置，则这是一个数值，指示允许的最大请求页面大小。仅当还设置了此属性时 page_size_query_param ，此属性才有效。
5. last_page_strings - 字符串值的列表或元组，指示可与page_query_param 一起使用 page_query_param 以请求集合中的最后一页的值。默认为 ('last',)
6. template - 在可浏览 API 中呈现分页控件时要使用的模板的名称。可以重写以修改呈现样式，或设置为 None 完全禁用 HTML 分页控件。默认值为 "rest_framework/pagination/numbers.html" 。


上面指定的例子：
![[image/06769e524b02d3fd76ae5b74c376f41e_MD5.png]]
一页是有两条数据的

但是我们可以改变url来改变一页多少数量

![[image/136f9534aefc4acdeff6f3035b680d35_MD5.png]]

