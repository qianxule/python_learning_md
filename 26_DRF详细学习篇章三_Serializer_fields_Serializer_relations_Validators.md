@[toc]
# Serializer fields
序列化程序字段在fields.py当中进行声明，但按照惯例，应使用导入from rest_framework import serializers ，并将字段称为 serializers.\<FieldName\> 。
## 公用字段参数
### read_only
只读字段包含在 API 输出中，但在创建或更新操作期间不应包含在输入中。序列化程序输入中错误包含的任何“read_only”字段都将被忽略。 

设置此选项 True 可确保在序列化表示时使用该字段，但在反序列化期间创建或更新实例时不使用该字段。默认为 False
### write_only
只写字段和read_only相反，只有post提交的时候才需要显示，在前端显示数据的时候，不会显示回去。默认为 False

### required
其实实际上个人觉得功能和read_only有一点点重复。

通常，如果在反序列化期间未提供字段，则会引发错误，这个字段的含义就是这个字段是否必填 。用户名，密码等是必须填写的，不填写就直接报错。如果反序列化期间不需要此字段，则设置为 false，默认值为 True。

### default
如果设置，这将给出默认值，如果未提供输入值，则将用于该字段。如果未设置，则默认行为是根本不填充属性，在部分更新操作期间不应用 。 default 在部分更新情况下，只有传入数据中提供的字段才会返回经过验证的值。

### allow_null
默认为 False

是否允许为NULL/空 ，换个说法就是你在填写数据的时候不想要这个数据可以是：

```python
name=""
age=null
```
这种情况的，值得关注的是，这个字段并不意味着可以不填，这个字段一般可以搭配default字段进行使用。

### source
将用于填充字段的属性的名称。可以是仅接受 self 参数的方法，例如 ，也可以是使用点表示法遍历属性的方法 URLField(source='get_absolute_url') ，例如 EmailField(source='user.email') 。

该值 source='*' 具有特殊含义，用于指示应将整个对象传递到字段。这对于创建嵌套表示或需要访问完整对象以确定输出表示的字段非常有用。


### label
一个短文本字符串，可用作 HTML 表单域或其他描述性元素中的字段名称，就是下文位置所对应的名字
![[image/5bc2a9aa8a90638ec704ba80a44580a8_MD5.png]]
### help_text
一个文本字符串，可用作 HTML 表单域或其他描述性元素中字段的说明。
![[image/f199028bf79df8cce81d88da63dc5111_MD5.png]]

### initial
应用于预填充 HTML 表单域的值的值。你可以传递给它一个可调用的，就像你可以对任何常规的 Django Field 所做的那样。

其实就是相当于给出默认值到前端

### style
键值对的字典，可用于控制呈现器应如何呈现字段。

这里有两个例子是 'input_type' ： 'base_template'

```python
# Use <input type="password"> for the input.这个实际上就对应了html的input的输出了，可以去看看是什么
password = serializers.CharField(
    style={'input_type': 'password'}
)

# Use a radio input instead of a select input.
color_channel = serializers.ChoiceField(
    choices=['red', 'green', 'blue'],
    style={'base_template': 'radio.html'}
)
```


参考：
[链接](https://blog.csdn.net/qq_52301431/article/details/123615767)

## Boolean fields
所在文件位置：django.db.models.fields
### BooleanField
值只能为True或者False
### NullBooleanField
不仅可以为True和False，也可以为None

## String fields
### CharField
|参数|说明|
|-|-|
|max_length| - 验证输入包含的字符数是否不超过此数。|
|min_length| - 验证输入包含的字符数是否少于此数。|
|allow_blank| - 如果设置为 ， True 则空字符串应被视为有效值。如果设置为 ， False 则空字符串被视为无效，并将引发验证错误。默认值为 False 。|
|trim_whitespace| - 如果设置为 ， True 则修剪前导和尾随空格。默认值为 True|

虽然 allow_null参数也可用于字符串字段，但不鼓励同时设置allow_blank=True和allow_null=True ，这可能会导致数据不一致和微妙的应用程序错误。

签名：

```python
CharField(max_length=None, min_length=None, allow_blank=False, trim_whitespace=True)
```

### EmailField
文本格式，验证为有效的电子邮件地址。

签名：

```python
EmailField(max_length=None, min_length=None, allow_blank=False)
```

### RegexField
文本格式，字段的值必须与regex参数指定的正则表达式相匹配。

```python
RegexField(regex, max_length=None, min_length=None, allow_blank=False)
```

### SlugField
A RegexField 根据模式 [a-zA-Z0-9_-]+ 验证输入，只允许数字字母下划线进行输入，也就是说是一个写死的正则表达式

```python
SlugField(max_length=50, min_length=None, allow_blank=False)
```

### URLField
需要表单 http://< host > / < path > 的完全限定 URL。

```python
URLField(max_length=200, min_length=None, allow_blank=False)
```

### UUIDField
确保输入的字段是有效的UUID字符串

签名：

```python
UUIDField(format='hex_verbose')
```
format ：确定 uuid 值的表示格式

1. 'hex_verbose' - 规范十六进制表示形式，包括连字符： "5ce0e9a5-5ffa-654b-cee0-1238041fb31a"
2. 'hex' - UUID 的紧凑十六进制表示形式，不包括连字符："5ce0e9a55ffa654bcee01238041fb31a"
3. 'int' - UUID 的 128 位整数表示形式： "123456789012312313134124512351145145114"
4. 'urn' - RFC 4122 UUID 的 URN 表示形式： "urn:uuid:5ce0e9a5-5ffa-654b-cee0-1238041fb31a" 更改 format 参数仅影响表示值。


### FilePathField
文件路径类型字段，其选择仅限于文件系统上某个目录中的文件名

签名：

```python
FilePathField(path, match=None, recursive=False, allow_files=True, allow_folders=False, required=None, **kwargs)
```

path - 指向此文件路径字段应从中选择的目录的绝对文件系统路径。
match - 一个正则表达式，作为字符串，FilePathField 将用于筛选文件名。
recursive - 指定是否应包含路径的所有子目录。默认值为 False 。
allow_files - 指定是否应包括指定位置中的文件。默认值为 True 。
allow_folders - 指定是否应包括指定位置中的文件夹。默认值为 False 。他和上面的那个必须有一个为true。

### IPAddressField
确保输入是有效的IPv4或IPv6的字符串。

签名
```python
IPAddressField(protocol='both', unpack_ipv4=False, **options)
```

## Numeric fields
### IntegerField
签名：

```python
IntegerField(max_value=None, min_value=None)
```
### FloatField
签名： 

```python
FloatField(max_value=None, min_value=None)
```

### DecimalField
签名：

```python
DecimalField(max_digits, decimal_places, coerce_to_string=None, max_value=None, min_value=None)
```

1. max_digits 数字中允许的最大位数。它必须是 None 大于或等于 的 decimal_places 或整数。
2. decimal_places 要与数字一起存储的小数位数。


## Date and time fields
### DateTimeField
签名：
```python
DateTimeField(format=api_settings.DATETIME_FORMAT,input_formats=None, default_timezone=None)
```

1. format 时间字符串的格式。如果未指定，则默认为settings中的设置。
2. input_formats 用于解析日期的输入格式的字符串列表。如果未指定，DATETIME_INPUT_FORMATS将使用默认设置 。
3. default_timezone  默认时区

### 其余的Date字段
DateField：日期类型字段
TimeField：时间类型字段，只有最后面的多少小时开始的
DurationField：持续时间长度类型的字段。


## Choice selection fields
### ChoiceField
签名： 

```python
ChoiceField(choices)
```

choices内部填的是选项，而且必须是一个可迭代的对象如：

```python
choices = [101,102,103,104]
```
### MultipleChoiceField
签名： 
```python
MultipleChoiceField(choices)
```

这个是可以多选的参数列表，与上面一样。

## File upload fields
### FileField
签名： 

```python
FileField(max_length=None, allow_empty_file=False, use_url=UPLOADED_FILES_USE_URL)
```

1. max_length - 指定文件名的最大长度。
2. allow_empty_file - 指定是否允许空文件。
3. use_url - 如果设置为 则 True URL 字符串值将用于输出表示形式。如果设置为 ， False 则文件名字符串值将用于输出表示。默认为设置键的值， True 除非另有 UPLOADED_FILES_USE_URL 设置。

### ImageField
签名：

```python
FileField(max_length=None, allow_empty_file=False, use_url=UPLOADED_FILES_USE_URL)
```
参数和上面一致，这边不进行介绍

## 复合字段
这边就不进行介绍了，详情见[链接](https://www.django-rest-framework.org/api-guide/fields/#regexfield)

## 其他字段
### ReadOnlyField
一个字段类，它只返回字段的值而不进行修改。默认情况下，当包含与属性而不是模型字段相关的字段名称时， ModelSerializer 将使用此字段。

签名： 

```python
ReadOnlyField()
```

### HiddenField
一个字段类，它不采用基于用户输入的值，而是从默认值或可调用对象中获取其值。

签名： 

```python
HiddenField()
```
例如，若要包括始终提供当前时间作为序列化程序验证数据的一部分的字段，可以使用以下内容：

```python
modified = serializers.HiddenField(default=timezone.now)
```
通常，仅当您有一些需要基于某些预先提供的字段值运行的验证，但您不希望向最终用户公开所有这些字段时，才需要该 HiddenField 类。

### ModelField
 签名： 

```python
 ModelField(model_field=<Django ModelField instance>)
```
### SerializerMethodField
这是一个只读字段。它通过在它附加到的序列化程序类上调用方法来获取其值。它可用于将任何类型的数据添加到对象的序列化表示形式中，换句话来讲的话就是，这个字段的值是由一个函数计算出来的

签名：

```python
SerializerMethodField(method_name=None)
```
method_name - 要调用的序列化程序上的方法的名称。如果未包括，则默认为 get_\<field_name\> 。

例子：

```python
from django.contrib.auth.models import User
from django.utils.timezone import now
from rest_framework import serializers

class UserSerializer(serializers.ModelSerializer):
    days_since_joined = serializers.SerializerMethodField()

    class Meta:
        model = User
        fields = '__all__'

    def get_days_since_joined(self, obj):
        return (now() - obj.date_joined).days
```

# Serializer relations
关系字段用于表示模型关系。在Django中存在ForeignKey、ManyToManyField和OneToOneField三种正向关系，以及反向关联和自定义关联。

使用外键进行关联的情况下，我们的每一次插入都是属于对于主表的全查询，当主表的数据量很大的时候，就是对于数据库的一次压力测试，所以尽量不要使用外键，我们使用逻辑层面的外键进行优化。


## API Reference
首先就是模型类：

```python
class Album(models.Model):
    album_name = models.CharField(max_length=100)
    artist = models.CharField(max_length=100)

class Track(models.Model):
    album = models.ForeignKey(Album, related_name='tracks', on_delete=models.CASCADE)
    order = models.IntegerField()
    title = models.CharField(max_length=100)
    duration = models.IntegerField()

    class Meta:
        unique_together = ['album', 'order']
        ordering = ['order']

    def __str__(self):
        return '%d: %s' % (self.order, self.title)
```

这边值得提到的一点==related_name==：

```python
# 我们以前从唱片中提取其track方法的时候需要按照下面的方式来：
album_obj.track_set 来进行提取，但是这有点不够个性化
#使用了related_name之后我们就可以使用下面的方式进行查找：
album_obj.tracks
```

### StringRelatedField
StringRelatedField 实际是通过使用其模型类中的\_\_str\_\_方法进行替代原本的只显示id的。此字段是只读的。并且这个字段如果是传出的是多个属性的需要加上many = True

例如搭配之前的模型类的str

```python
class AlbumSerializer(serializers.ModelSerializer):
    tracks = serializers.StringRelatedField(many=True)

    class Meta:
        model = Album
        fields = ['album_name', 'artist', 'tracks']
```
输出的就是：

```python
{
    'album_name': 'Things We Lost In The Fire',
    'artist': 'Low',
    'tracks': [
        '1: Sunflower',
        '2: Whitetail',
        '3: Dinosaur Act',
        ...
    ]
}
```
这边有一个小==细节==：tracks = serializers.StringRelatedField(many=True)当中的tracks需要和那个related_name相==一致==。


### PrimaryKeyRelatedField
PrimaryKeyRelatedField 可用于使用其主键来表示关系的目标，这个只给主键的用的不多，这边就不细说了，以前说的太多了。

### HyperlinkedRelatedField
可用于使用超链接来表示关系的目标，这个之前虽然说过了，但由于十分重要，这边就再次给个例子，理解一下：

```python
class AlbumSerializer(serializers.ModelSerializer):
    tracks = serializers.HyperlinkedRelatedField(
        many=True,
        read_only=True,
        view_name='track-detail'
    )

    class Meta:
        model = Album
        fields = ['album_name', 'artist', 'tracks']
```
输出
```python
{
    'album_name': 'Graceland',
    'artist': 'Paul Simon',
    'tracks': [
        'http://www.example.com/api/tracks/45/',
        'http://www.example.com/api/tracks/46/',
        'http://www.example.com/api/tracks/47/',
        ...
    ]
}
```
Arguments: 参数：

|参数|说明|
|--|--|
|view_name |应用作关系目标的视图名称。如果您使用的是标准路由器类，这将是一个格式\<modelname\>-detail 为 .必填。|
|queryset| - 验证字段输入时用于模型实例查找的查询集。关系必须显式设置查询集，或设置 read_only=True| 
|many|  如果应用于对多关系，则应将此参数设置为 True 。|
|allow_null| - 如果设置为 True ，则该字段将接受 的值 None 或空字符串的可为空的关系。默认值为 False 。|
|lookup_field | 目标上应用于查找的字段。应对应于引用视图上的 URL 关键字参数。默认值为 'pk' 。|
|lookup_url_kwarg| - 与查找字段对应的 URL conf 中定义的关键字参数的名称。默认使用与 相同的 lookup_field 值。|
|format| - 如果使用格式后缀，则超链接字段将对目标使用相同的格式后缀，除非使用 format 参数重写。|

### SlugRelatedField
完成的功能和stringRelatedField差不多，不过不用重写str方法而已

例如，以下序列化程序：

```python
class AlbumSerializer(serializers.ModelSerializer):
    tracks = serializers.SlugRelatedField(
        many=True,
        read_only=True,
        slug_field='title'
     )

    class Meta:
        model = Album
        fields = ['album_name', 'artist', 'tracks']
```
输出：

```python
{
    'album_name': 'Dear John',
    'artist': 'Loney Dear',
    'tracks': [
        'Airport Surroundings',
        'Everything Turns to You',
        'I Was Only Going Out',
        ...
    ]
}
```
输出的就只能是你写的那一个字段名称

### HyperlinkedIdentityField
没啥用，特别鸡肋，强烈差评


## Nested relationships（很重要）
实现的实际上是多级显示，也即多层json。

```python
class TrackSerializer(serializers.ModelSerializer):
    class Meta:
        model = Track
        fields = ['order', 'title', 'duration']

class AlbumSerializer(serializers.ModelSerializer):
    tracks = TrackSerializer(many=True)

    class Meta:
        model = Album
        fields = ['album_name', 'artist', 'tracks']

    def create(self, validated_data):
        tracks_data = validated_data.pop('tracks')
        album = Album.objects.create(**validated_data)
        for track_data in tracks_data:
            Track.objects.create(album=album, **track_data)
        return album
```
学习一下create 的类似写法。

## 自定义字段
### 自定义关系字段
回想一下关系字段在serializer当中的作用：他就是拿来验证这个传入的数据是否符合格式的，或者换句说法也就是说，他是用来处理数据，让数据满足我们想要的效果的。

那么这边就有了这么一个需求自定义话字段的需求，关于自定义化需求，我们需要满足如下的格式：

```python
class 自定义关系字段名称(serializers.RelatedField):
	# 这个value实际上就是前端传过来的对应字段名的属性的数据
	def to_representation(self,value):
		······数据进行处理，assert或者直接进行处理
		return 处理完的value
```


例子：
```python
import time

class TrackListingField(serializers.RelatedField):
    def to_representation(self, value):
        duration = time.strftime('%M:%S', time.gmtime(value.duration))
        return 'Track %d: %s (%s)' % (value.order, value.title, duration)

class AlbumSerializer(serializers.ModelSerializer):
    tracks = TrackListingField(many=True)

    class Meta:
        model = Album
        fields = ['album_name', 'artist', 'tracks']
```

### 自定义超链接字段
首先需要继承HyperlinkedRelatedField`类，然后根据需要，选择性地覆写下面两个方法：
1. get_url(self, obj, view_name, request, format)  此方法用于对象实例和它的URL表示之间的映射。
2. get_object(self, view_name, view_args, view_kwargs) 这个方法的返回值必须和匹配的URL conf参数一致。

下面假设我们有一个顾客对象的URL，并接受两个参数：

> /api/<organization_slug>/customers/<customer_pk>/

对于上面这种url，普通的超链接字段无法实现，因为超链接字段只能处理url中只有一个参数的情况，而上面有两个参数。只能自定义超链接字段，然后自己写代码处理了

```python
from rest_framework import serializers
from rest_framework.reverse import reverse

class CustomerHyperlink(serializers.HyperlinkedRelatedField):
    # We define these as class attributes, so we don't need to pass them as arguments.
    view_name = 'customer-detail'
    queryset = Customer.objects.all()

    def get_url(self, obj, view_name, request, format):
        url_kwargs = {
            'organization_slug': obj.organization.slug,
            'customer_pk': obj.pk
        }
        return reverse(view_name, kwargs=url_kwargs, request=request, format=format)

    def get_object(self, view_name, view_args, view_kwargs):
        lookup_kwargs = {
           'organization__slug': view_kwargs['organization_slug'],
           'pk': view_kwargs['customer_pk']
        }
        return self.get_queryset().get(**lookup_kwargs)
```

# Validators
## Validators
### UniqueValidator 唯一验证
此验证器可用于对模型字段强制实施 unique=True 约束。它需要一个必需的参数和一个可选 messages 参数

queryset 必需 - 这是应强制实施唯一性的查询集。
message - 验证失败时应使用的错误消息。
lookup - 用于查找具有正在验证的值的现有实例的查找。默认值为 'exact' 。

例子：

```python
slug = SlugField(max_length=100,validators=[UniqueValidator(queryset=BlogPost.objects.all())])
```
或者也可以统一放到Meta类当中统一声明

### UniqueTogetherValidator 联合唯一验证
此验证器可用于对模型实例强制实施 unique_together 约束。它有两个必需的参数和一个可选 messages 参数

queryset 必需的参数 - 这是应强制实施唯一性的查询集。
fields  必须的参数- 字段名称的列表或元组，应形成唯一的集合。这些字段必须作为序列化程序类上的字段存在。
message 可选的参数- 验证失败时应使用的错误消息。

```python
from rest_framework.validators import UniqueTogetherValidator

class ExampleSerializer(serializers.Serializer):
    # ...
    class Meta:
        # ToDo items belong to a parent list, and have an ordering defined
        # by the 'position' field. No two items in a given list may share
        # the same position.
        validators = [
            UniqueTogetherValidator(
                queryset=ToDoItem.objects.all(),
                fields=['list', 'position']
            )
        ]
```

### UniqueForDateValidator、UniqueForMonthValidator、UniqueForYearValidator 
实际上这也是一个联合唯一验证，不过他的验证是一个元素与另一个date元素的年或月或日的唯一性验证

queryset 必需的参数 - 这是应强制实施唯一性的查询集。
field 必须的参数 - 将验证给定日期范围内唯一性的字段名称。这必须作为序列化程序类上的字段存在。
date_field 必需的参数 - 将用于确定唯一性约束的日期范围的字段名称。这必须作为序列化程序类上的字段存在。
message - 验证失败时应使用的错误消息。

例子：这个例子就是slug字段和publish字段的年一起唯一性验证
```python
from rest_framework.validators import UniqueForYearValidator

class ExampleSerializer(serializers.Serializer):
    # ...
    class Meta:
        # Blog posts should have a slug that is unique for the current year.
        validators = [
            UniqueForYearValidator(
                queryset=BlogPostItem.objects.all(),
                field='slug',
                date_field='published'
            )
        ]
```

## 高级字段的默认值
我们使用这些默认值的时候，一般有下面的两种情况：

1. 使用 HiddenField .此字段将出现在序列化程序输出表示形式中validated_data ，但不会在序列化程序输出表示形式中使用。
2. 使用带有 的标准 read_only=True 字段，但这也包括一个 default=… 参数此字段将在序列化程序输出表示形式中使用，但不能由用户直接设置。

### CurrentUserDefault
当前用户默认：
```python
owner = serializers.HiddenField(default=serializers.CurrentUserDefault())
```

### CreateOnlyDefault 
一个默认类，可用于仅在创建操作期间设置默认参数。在更新期间，该字段将被省略。它采用单个参数，该参数是在创建操作期间应使用的默认值或可调用对象。

```python
created_at = serializers.DateTimeField(default=serializers.CreateOnlyDefault(timezone.now))
```

这当然还有一些，这边就不详细说明






































































































































































































