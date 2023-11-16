@[toc]
# Parsers 解析器
这一块不太重要，直接了解即可，这一块，一般不会去修改，一般只会使用一些已经写好的东西

解析器是干什么的？因为前后端分离，因为可能采用json、xml、html等各种不同格式的内容，后端必须要有一个解析器来解析前端发送过来的数据，也就是翻译器！否则后端凭什么看懂前端的数据？对应地，后端也有一个渲染器Render，和解析器是相反的方向，将后端的数据翻译成前端能明白的数据格式。

## 解析的过程
DRF将有效的解析器集定义为类的列表。当request.data被访问时，REST框架将检查请求头部的属性，以此来确定要使用哪个解析器来解析数据。解析器只有在请求request.data的时候才会被调用！如果不需要data数据，那么就不用解析.

例子：
在开发客户端应用程序时，务必确保在请求头部中包含 Content-Type 属性。如果未设置内容类型，则大多数客户端将默认使用 'application/x-www-form-urlencoded' 类型，但这可能不是你想要的。例如，如果使用jQuery的ajax或者vue的axios方法发送 json 编码的数据，则应在请求头部中设置contentType: 'application/json‘


## 设置全局解析器
可以使用该 DEFAULT_PARSER_CLASSES 设置全局设置默认解析器集。例如，以下设置将仅允许包含内容的请求 JSON ，而不是默认的 JSON 或表单数据。

```python
REST_FRAMEWORK = {
    'DEFAULT_PARSER_CLASSES': [
        'rest_framework.parsers.JSONParser',
    ]
}
```


## 使用局部解析器
一般有两种使用方式：

方式一：

```python
from rest_framework.parsers import JSONParser
from rest_framework.response import Response
from rest_framework.views import APIView

class ExampleView(APIView):
    """
    A view that can accept POST requests with JSON content.
    """
    parser_classes = [JSONParser]

    def post(self, request, format=None):
        return Response({'received data': request.data})
```

方式二：

```python
from rest_framework.decorators import api_view
from rest_framework.decorators import parser_classes
from rest_framework.parsers import JSONParser

@api_view(['POST'])
@parser_classes([JSONParser])
def example_view(request, format=None):
    """
    A view that can accept POST requests with JSON content.
    """
    return Response({'received data': request.data})
```

## 常见的接口函数
### JSONParser
分析 JSON 请求内容。 request.data 将填充数据字典。

.media_type: application/json

### FormParser 和 MultiPartParser
解析 HTML 表单内容。 request.data 将填充 的 QueryDict 数据。通常需要同时 FormParser 使用两者 MultiPartParser ，以便完全支持 HTML 表单数据。

前者：.media_type: application/x-www-form-urlencoded
后者：.media_type: multipart/form-data

### FileUploadParser
分析原始文件上传内容。该 request.data 属性将是一个字典，其中包含包含上传文件的单个键 'file' 。

如果使用 的视图使用 filename URL FileUploadParser 关键字参数调用，则该参数将用作文件名。

.media_type: */*

```python
# views.py
class FileUploadView(views.APIView):
    parser_classes = [FileUploadParser]

    def put(self, request, filename, format=None):
        file_obj = request.data['file']
        # ...
        # do some stuff with uploaded file
        # ...
        return Response(status=204)

# urls.py
urlpatterns = [
    # ...
    path(r'upload/<str:filename>/', FileUploadView.as_view())
]
```

其他部分如自定义就见官方文档，这边不进行介绍：[链接](https://www.django-rest-framework.org/api-guide/parsers/)

# Renderers 渲染器
所谓的渲染器（Renderer），其实就是将服务器生成的数据的格式转换为HTTP请求的格式。REST框架包含许多内置的Renderer类，可以返回各种媒体类型的响应。还支持定义自定义渲染器，灵活地设计自己的媒体类型，这一块也不是十分重要，了解即可，一般不进行修改。

在DRF配置参数中，可用的渲染器依然是作为一个类的列表进行定义。但与解析器不同的是，渲染器的列表是有顺序关系的。REST框架将对传入请求执行内容协商，根据请求的类型确定最合适的渲染器以满足类型要求。

内容协商的基本过程包括检查请求的 Accept 标头，以确定响应中期望的媒体类型。或者，URL 上的格式后缀可用于显式请求特定表示形式。例如，URL http://example.com/api/users_count.json 可能是始终返回 JSON 数据的终结点。

## 解析的过程
在为 API 指定呈现器类时，请务必考虑要为每种媒体类型分配的优先级。如果客户端未指定它可以接受的表示形式，例如发送标头或根本不包含 Accept: * / * Accept 标头，则 REST 框架将选择列表中的第一个渲染器以用于响应，讲人话就是如果没有进行局部定义，那么就将按照全局定义的顺序进行判断是否和Accept相比，能否满足其条件，满足的话就直接使用，而不会使用别的，所以警惕所有Accept 的 * / *骗局

例如，如果您的 API 提供 JSON 响应和 HTML 可浏览 API，您可能希望制作 JSONRenderer 默认渲染器，以便将响应发送到 JSON 未指定 Accept 标头的客户端。

## 设置全局渲染器
可以使用该 DEFAULT_RENDERER_CLASSES 设置全局设置默认渲染器集。例如，以下设置将用作 JSON 主媒体类型，并且还包括自描述 API。

```python
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': [
        'rest_framework.renderers.JSONRenderer',
        'rest_framework.renderers.BrowsableAPIRenderer',
    ]
}
```

## 使用局部渲染器
一般也还是两种方法：

```python
from django.contrib.auth.models import User
from rest_framework.renderers import JSONRenderer
from rest_framework.response import Response
from rest_framework.views import APIView

class UserCountView(APIView):
    """
    A view that returns the count of active users in JSON.
    """
    renderer_classes = [JSONRenderer]

    def get(self, request, format=None):
        user_count = User.objects.filter(active=True).count()
        content = {'user_count': user_count}
        return Response(content)
```

或者，如果您将 @api_view 装饰器与基于函数的视图一起使用。

```python
@api_view(['GET'])
@renderer_classes([JSONRenderer])
def user_count_view(request, format=None):
    """
    A view that returns the count of active users in JSON.
    """
    user_count = User.objects.filter(active=True).count()
    content = {'user_count': user_count}
    return Response(content)
```
## 常用接口函数
### JSONRenderer
请注意，默认样式是包含 unicode 字符，并使用没有不必要的空格的紧凑样式呈现，响应客户端还可以包含媒体 'indent' 类型参数，在这种情况下，返回 JSON 的内容将缩进。例如 Accept: application/json; indent=4。（宽度默认是80，indent = 4首行缩进一个tab）

关于indent = 4 的介绍引用别人的博客吧：[链接](https://blog.csdn.net/lixiang5453/article/details/120005487)

```python
import pprint
 
 
stus = {
    "name": "lixiang",
    "id": 1,
    "age": 18,
    "addr": "北京",
    "high": 188
}
 
# 正常输出 stus 字典
print(stus)
 
# 格式化输出 stus 字典，如果不设置 width，默认行宽是 80，如果不超过 80 输出显示和 print 一样
# indent = 4 代表设置缩进 4 个字符，width = 5，代表长度是 5
pprint.pprint(stus, indent=4, width=5)
```
输出结果：

```python
{'name': 'lixiang', 'id': 1, 'age': 18, 'addr': '北京', 'high': 188}
{   'addr': '北京',
    'age': 18,
    'high': 188,
    'id': 1,
    'name': 'lixiang'}
```

html格式
```python
.media_type: application/json 
.format: 'json' 
.charset: None 
```

### TemplateHTMLRenderer
这一个实际上的使用使前后端不分离的地方的，所以个人感觉用处不大。

展示一下使用：

```python
from my_project.example.models import Profile
from rest_framework.renderers import TemplateHTMLRenderer
from rest_framework.response import Response
from rest_framework.views import APIView
 
class ProfileList(APIView):
    renderer_classes = [TemplateHTMLRenderer]
    template_name = 'profile_list.html'
 
    def get(self, request):
        queryset = Profile.objects.all()
        return Response({'profiles': queryset})
```

Profile_list.html:

```python
<html><body>
<h1>Profiles</h1>
<ul>
    {% for profile in profiles %}
    <li>{{ profile.name }}</li>
    {% endfor %}
</ul>
</body></html>
```

```python
.media_type: text/html
.format: 'html' 
.charset: utf-8 
```

### StaticHTMLRenderer
建议使用这个Static代替上面的Temple

使用介绍：
```python
@api_view(['GET'])
@renderer_classes([StaticHTMLRenderer])
def simple_html_view(request):
    data = '<html><body><h1>Hello, world</h1></body></html>'
    return Response(data)
```
url自己配一下。

```python
.media_type: text/html .media_type： text/html

.format: 'html' 。格式： 'html'

.charset: utf-8 .字符集： utf-8
```
![[image/90f81ac8389c03f13611431c5da2f0db_MD5.png]]
### BrowsableAPIRenderer
通过这个渲染器可以将数据渲染为HTML页面，提供可浏览的API页面

BrowsableAPIRenderer会探测哪个其他渲染器被赋予了最高优先级，并使用该渲染器在HTML页面中显示API样式。

```python
.media_type: text/html 
.format: 'api' 
.charset: utf-8 
.template: 'rest_framework/api.html' 
```

### 剩下等待补充

后面几个不太重要，基本只有需要使用的时候进行查询计科，这边不太过多介绍，基本上记忆个json和statichtml就可以了

剩下的官网进行查看，并且去csdn上查找应用方式：
[链接](https://www.django-rest-framework.org/api-guide/renderers/#browsableapirenderer)

# Serializers
序列化程序允许将复杂数据（如查询集和模型实例）转换为本机 Python 数据类型，然后可以轻松地将其呈现为 JSON 或其他 XML 内容类型。序列化程序还提供反序列化，允许在首先验证传入数据后将分析的数据转换回复杂类型。

REST 框架中的序列化程序的工作方式与 Django Form 和 ModelForm 类非常相似。我们提供了一个类，它为您提供了一种强大的通用方法来控制响应的输出，以及一个 ModelSerializer 类，该 Serializer 类为创建处理模型实例和查询集的序列化程序提供了有用的快捷方式。
## Serializers
对于前面的序列化和反序列化就不再进行介绍，如果不懂的话，建议看我前几篇博客的快速入门，那边讲解更加详细，这边对于流程进行相关的介绍，（个人觉得对于面向切面编程来说，最为重要的事情就是，了解每个执行流程，自己可以按照一定的时间顺序梳理下来。）

首先我们可以了解前后端分离的情况下，后端实际上要做的事情就是传递json或者别的格式的数据到对应的url即可，至于数据的调用全部由前端进行处理，组合。

所以就先讲解一下序列化：（就是我们要把数据传递给前端）流程就是先获取模型类的对象，然后就是调用serializer，进行json化，在内部调用了你所定义的类属性作为键，你传入的属性值作为值，形成一个python的字典格式，然后再jsonrender，以字节流json格式传出给到前端。

然后讲解一下反序列化的流程：(就是我们需要将前端传递来的数据进行存储到数据库当中)，首先就是前端传递来的数据先进行一定的steam和json处理，得到对应的字典，然后再传入serializer当中，反序列化得到一个对应的ORM类对象，（==这个时候可以插入一个pre_create，这种类型的方法，序列化后，我们可以类似插入提前动作一样，先插入我们自己传入的数据==），这时候就需要进行存储了，存储的过程就是先判断is_val，判断对象属性是否是合法合规的，然后进行如果满足了，就进行save操作，（==这边可以进行重写save操作做到自定义操作内容，大概率是进行数据处理。==），调用save操作之时，在内部会调用serializer当中的create或uptate进行保存，所以在model准备进行保存的时候，需要我们写这两个函数（如果继承的类是ViewSets的话，就不不需要写这两个，这两个使用继承下来的属性即可，一般不用进行修改）。

然后后文只讲述一些快速入门没有讲解到的知识。

### 调用create 与 update方法
实际上就是通过传入的参数多一个对应查出来的类成员，进行调用不同的方法：

```python
# .save() will create a new instance.
serializer = CommentSerializer(data=data)

# .save() will update the existing `comment` instance.
serializer = CommentSerializer(comment, data=data)

然后都是进行is_vallid() 验证

然后进行save，就能区分是create 还是 update了
```
### Validation 验证
反序列化数据时，始终需要在尝试访问已验证的数据或保存对象实例之前调用 is_valid() 。如果发生任何验证错误，该 .errors 属性将包含一个表示生成的错误消息的字典。例如：

```python
serializer = CommentSerializer(data={'email': 'foobar', 'content': 'baz'})
serializer.is_valid()
# False
serializer.errors
# {'email': ['Enter a valid e-mail address.'], 'created': ['This field is required.']}
```

#### 字段级验证
您可以通过向 Serializer 子类添加 .validate_<field_name> 方法来指定自定义字段级验证。这些类似于 Django 表单上 .clean_<field_name> 的方法。

字段级验证的函数名称一定需要命名成validate_对应的字段名称，这样子才是对于对应字段的验证，传入的参数也按照他的来。

```python
from rest_framework import serializers

class BlogPostSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=100)
    content = serializers.CharField()

    def validate_title(self, value):
        """
        Check that the blog post is about Django.
        """
        if 'django' not in value.lower():
            raise serializers.ValidationError("Blog post is not about Django")
        return value
```

#### 对象级验证
若要执行需要访问多个字段的任何其他验证，请添加一个调用 .validate() 到 Serializer 子类的方法。此方法采用单个参数，该参数是字段值的字典。如有必要，它应该引发 a serializers.ValidationError ，或者只返回经过验证的值。例如：

```python
from rest_framework import serializers

class EventSerializer(serializers.Serializer):
    description = serializers.CharField(max_length=100)
    start = serializers.DateTimeField()
    finish = serializers.DateTimeField()

    def validate(self, data):
        """
        Check that start is before finish.
        """
        if data['start'] > data['finish']:
            raise serializers.ValidationError("finish must occur after start")
        return data
```

想要进行输出验证失败的内容用：raise serializers.ValidationError进行输出
#### 函数作为参数进行验证
序列化程序上的各个字段可以包含验证程序，方法是在字段实例上声明验证程序，例如：

```python
def multiple_of_ten(value):
    if value % 10 != 0:
        raise serializers.ValidationError('Not a multiple of ten')

class GameRecord(serializers.Serializer):
    score = IntegerField(validators=[multiple_of_ten])
    ...
```
这种方法适用于很多参数需要相同的验证的时候

想要进行输出验证失败的内容用：raise serializers.ValidationError进行输出


#### 唯一性验证
序列化程序类还可以包括应用于完整字段数据集的可重用验证程序。通过在内部 Meta 类上声明这些验证器来包含这些验证器，如下所示：

```python
class EventSerializer(serializers.Serializer):
    name = serializers.CharField()
    room_number = serializers.IntegerField(choices=[101, 102, 103, 201])
    date = serializers.DateField()

    class Meta:
        # Each room only has one event per day.
        validators = [
            UniqueTogetherValidator(
                queryset=Event.objects.all(),
                fields=['room_number', 'date']
            )
        ]
```
这个唯一性验证，验证的是房间号和date的组合合起来是唯一的，那为什么需要是[]的对象呢，原因就是我们可能需要多重验证，比如我们想要房间号和日期做一个唯一性验证，还有入住人和什么别的字段进行唯一性验证，就可以用到这个。

更多的验证，在之后也会进行介绍，这边先按下不表。

### 部分更新
还记得update当中的instance吗？将初始对象或查询集传递给序列化程序实例时，该对象将作为 .instance 提供。如果未传递初始对象，则 .instance 属性将为 None 。将数据传递到序列化程序实例时，未修改的数据将作为 .initial_data .如果未传递 data 关键字参数，则该 .initial_data 属性将不存在。

默认情况下，必须为所有必填字段传递序列化程序的值，否则它们将引发验证错误。您可以使用该 partial 参数来允许部分更新。

```python
# Update `comment` with partial data
serializer = CommentSerializer(comment, data={'content': 'foo bar'}, partial=True)
```

### 处理嵌套对象
这个将放到后面的序列化关系当中进行详细的介绍，这边先不介绍，有兴趣的可以去查看官网的Dealing with nested objects一块

[链接](https://www.django-rest-framework.org/api-guide/serializers/#dealing-with-nested-objects)

## ModelSerializer
该 ModelSerializer 类与常规 Serializer 类相同，不同之处在于：

1. 它将根据模型自动生成一组字段。
2. 它将自动生成序列化程序的验证程序，例如unique_together验证程序，但这个唯一是所有一起的唯一
3. 它包括 和 .update() 的 .create() 简单默认实现。

模型上的任何关系（如外键）都将映射到 PrimaryKeyRelatedField 。默认情况下不包括反向关系，除非按照序列化程序关系文档中的规定明确包含。

至于代码就不演示了，大家应该也很熟悉


### repr辅助理解对应的字段关系
序列化程序类生成有用的详细表示字符串，允许您完全检查其字段的状态。当您想要确定自动为您创建的字段和验证器集时 ModelSerializers ，这尤其有用。
```python
>>> from myapp.serializers import AccountSerializer
>>> serializer = AccountSerializer()
>>> print(repr(serializer))
AccountSerializer():
    id = IntegerField(label='ID', read_only=True)
    name = CharField(allow_blank=True, max_length=100, required=False)
    owner = PrimaryKeyRelatedField(queryset=User.objects.all())
```

### 指定要显示的片段

三种：

最正常的，不解释
```python
class AccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = Account
        fields = ['id', 'account_name', 'users', 'created']
```

您还可以将 fields 属性设置为特殊值 '\_\_all\_\_' ，以指示应使用模型中的所有字段。

```python
class AccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = Account
        fields = '__all__'
```

可以将属性 exclude 设置为要从序列化程序中排除的字段列表。

```python
class AccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = Account
        exclude = ['users']
```

### 指定嵌套序列化
depth实际上用于展示数据的，如果想要数据的展示再展开，比如我们之前使用的PrimaryKeyRelatedField，这个属性，实际上就是将数据嵌套到其中，我们就可以通过显式的提供depth的大小进行展开，不过这个属性不是很推荐进行修改，到后面的关系字段用另一种方式好一些，这边也列出来，自己决定使用什么。

```python
class AccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = Account
        fields = ['id', 'account_name', 'users', 'created']
        depth = 1
```

该选项应设置为一个整数值，该 depth 值指示在恢复为平面表示之前应遍历的关系深度。

### 显式指定字段
您可以通过在类上声明字段来向 添加 ModelSerializer 额外的字段或覆盖默认字段，就像为 Serializer 类声明一样。

```python
class AccountSerializer(serializers.ModelSerializer):
    url = serializers.CharField(source='get_absolute_url', read_only=True)
    groups = serializers.PrimaryKeyRelatedField(many=True)

    class Meta:
        model = Account
        fields = ['url', 'groups']
```

就比如说group，实际上就是原本就不在内部存在的字段，但这边想要他来进行显示，这边就用到了这个属性。


### 指定只读字段
meta版本的
```python
class AccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = Account
        fields = ['id', 'account_name', 'users', 'created']
        read_only_fields = ['account_name']
```

一般如果出现只读字段的话，那么你进行post的情况下，就需要自定义其数据的传递，要么给default，要么就是prefom_create进行重写，自己需要决定好


### 其他关键字参数
还有一个快捷方式允许您使用该 extra_kwargs 选项在字段上指定任意附加关键字参数。与 的情况 read_only_fields 一样，这意味着您无需在序列化程序上显式声明该字段。

此选项是一个字典，将字段名称映射到关键字参数的字典。例如：

```python
class CreateUserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['email', 'username', 'password']
        extra_kwargs = {'password': {'write_only': True}}

    def create(self, validated_data):
        user = User(
            email=validated_data['email'],
            username=validated_data['username']
        )
        user.set_password(validated_data['password'])
        user.save()
        return user
```

==请记住，如果该字段已在序列化程序类上显式声明，则该 extra_kwargs 选项将被忽略。==

## HyperlinkedModelSerializer
HyperlinkedModelSerializer类直接继承ModelSerializer类，不同之处在于它使用超链接来表示关联关系而不是主键。

默认情况下，HyperlinkedModelSerializer序列化器将包含一个url字段而不是主键字段。url字段将使用HyperlinkedIdentityField字段来表示，模型的任何关联都将使用HyperlinkedRelatedField字段来表示。

基本使用：

```python
class AccountSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Account
        fields = ['url', 'id', 'account_name', 'users', 'created']
```

### 修改超链接视图
默认情况下，超链接应对应于与样式 '{model_name}-detail' 匹配的视图名称，并通过 pk 关键字参数查找实例。您可以通过在 extra_kwargs 设置中使用 and view_name lookup_field 选项之一或两者来覆盖 URL 字段视图名称和查阅字段，如下所示：

```python
class AccountSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Account
        fields = ['account_url', 'account_name', 'users', 'created']
        extra_kwargs = {
            'url': {'view_name': 'accounts', 'lookup_field': 'account_name'},
            'users': {'lookup_field': 'username'}
        }
```

或者，您可以显式设置序列化程序上的字段。例如：

```python
class AccountSerializer(serializers.HyperlinkedModelSerializer):
    url = serializers.HyperlinkedIdentityField(
        view_name='accounts',
        lookup_field='slug'
    )
    users = serializers.HyperlinkedRelatedField(
        view_name='user-detail',
        lookup_field='username',
        many=True,
        read_only=True
    )

    class Meta:
        model = Account
        fields = ['url', 'account_name', 'users', 'created']
```
提示：正确匹配超链接表示形式和您的 URL conf 有时可能有点繁琐。打印 repr HyperlinkedModelSerializer 实例是一种特别有用的方法，可以准确检查关系也应映射哪些视图名称和查找字段。

### 更改网址字段名称
网址字段的名称默认为“网址”。您可以使用该 URL_FIELD_NAME 设置全局覆盖此设置。


## ListSerializer
这个等之后再进行介绍，这一块意义不太大，除了自定义，不然使用参数即可替代




## BaseSerializer（了解即可）
BaseSerializer 可用于轻松支持替代序列化和反序列化样式的类。

此类实现与 Serializer 类相同的基本 API：

.data - 返回传出的基元表示形式。
.is_valid() - 反序列化和验证传入数据。
.validated_data - 返回经过验证的传入数据。
.errors - 在验证期间返回任何错误。
.save() - 将验证的数据保存到对象实例中。

有四种方法可以重写，具体取决于您希望序列化程序类支持的功能：

.to_representation() - 重写此选项以支持序列化和读取操作。
.to_internal_value() - 重写此选项以支持反序列化，用于写入操作。
.create() 和 .update() - 覆盖其中一个或两个以支持保存实例。





