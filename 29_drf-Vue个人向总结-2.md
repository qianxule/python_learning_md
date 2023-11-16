@[toc]

# drf项目总结2
## 重写create
我们之前的讲解当中已经讲解了重写perform_create，而重写create实际上在这边重写其返回，就可以让返回新增上你添加的数据，之前perform_create做的是新增添加的数据到数据库当中，而create是新增到外面的列表或者详细页当中。


```python
    def perform_create(self, serializer):
        return serializer.save()

    def create(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        user = self.perform_create(serializer)
        #拿到serializer.data然后将name和token填写进去
        re_dict = serializer.data
        payload = jwt_payload_handler(user)
        re_dict["token"] = jwt_encode_handler(payload)
        re_dict["name"] = user.name if user.name else user.username

        headers = self.get_success_headers(serializer.data)
        return Response(re_dict, status=status.HTTP_201_CREATED, headers=headers)
```
主要就是区别好二者之间的关系，重写不同的类，就可以做到呈现不同样式的风格。


## 自定义验证类
这个之前已经讲解过了，这边由于十分重要，这边就再沾一次，但是不做介绍。

```python
class SmsSerializer(serializers.Serializer):
    mobile = serializers.CharField(max_length=11)

    def validate_mobile(self, mobile):
        """
        验证手机号码
        :param data:
        :return:
        """

        # 手机是否注册
        if User.objects.filter(mobile=mobile).count():
            raise serializers.ValidationError("用户已经存在")

        # 验证手机号码是否合法
        if not re.match(REGEX_MOBILE, mobile):
            raise serializers.ValidationError("手机号码非法")

        # 验证码发送频率
        one_mintes_ago = datetime.now() - timedelta(hours=0, minutes=1, seconds=0)
        if VerifyCode.objects.filter(add_time__gt=one_mintes_ago, mobile=mobile).count():
            raise serializers.ValidationError("距离上一次发送未超过60s")
        #验证通过，返回手机号
        return mobile
```

## 获取个性化内容 与 lookup_field 的用处

```python
class UserFavViewset(mixins.CreateModelMixin,mixins.ListModelMixin,mixins.RetrieveModelMixin,
                     mixins.DestroyModelMixin,viewsets.GenericViewSet):
    '''
    list:
        获取用户收藏列表
    retrieve:
        判断某个商品是否已经收藏
    create:
        收藏商品
    '''
    permission_classes = (IsAuthenticated,IsOwnerOrReadOnly) #验证是否登录
    lookup_field = "goods_id"
    def get_queryset(self):
        return UserFav.objects.filter(user = self.request.user)

    def perform_create(self, serializer):
        instance = serializer.save()#得到serializer
        goods = instance.goods
        goods.fav_num += 1#对收藏数加1
        goods.save()

    def get_serializer_class(self):
        if self.action == "list":
            return UserFavDetailSerializer
        elif self.action == "create":
            return UserFavSerializer

        return UserFavSerializer
```

不过值得关注的是

```python
lookup_field = "goods_id"
```
这个参数做的是什么呢？

之前也写过，这个参数做的事情就是修改详情页的pk的含义，原本不修改的pk实际上对应的就是那张表中的id，但是我们这边希望他的修改可以针对这张表中的别的字段，所以可以选择重写lookup_field 这个字段，==不过值得关注的是，这个东西需要在每一组querry当中是唯一指定的==。


## 重写get_queryset，get_serializer_class类
其实上面的代码也已经显示了重写后的效果，根据那个来理解就可以，实际上就是把原本queryset变成非固定的querryset这样子的。


## docs帮助文档

首先我们在 urls 中增加

```python
from rest_framework.documentation import include_docs_urls
path('docs/', include_docs_urls(title="我的生鲜"))
```

还需要在 settings 中增加：

```python
REST_FRAMEWORK = { 
'DEFAULT_SCHEMA_CLASS': 'rest_framework.schemas.AutoSchema' }
```

这时我们访问 http://localhost:8000/docs

实际上就访问了那个对应的窗口，然后内部的调试用的帮助文档实际上就是你的函数注释，类注释，这个是方便前后端联调使用的东西，这边不细讲。



## 支付宝支付原理（微信同原理）
为什么不用微信支付呢？原因就是微信支付需要商家，支付宝个人即可使用，所以使用支付宝，但是二者的使用内核是一致的，这边就大概讲解一下支付宝的支付原理。

首先登录支付宝开放平台（微信有商家的话也可以使用微信开放平台进行使用）：[链接](https://open.alipay.com/)

这边主要介绍的是沙箱应用：

![[image/129a62a24a5a749b09846833382ade0a_MD5.png]]
点击进入就会得到一些相对应的数据，点击创建新的沙箱应用即可使用。

### 使用流程
流程参考：[链接](https://opendocs.alipay.com/open/270/105899/)

具体的使用流程：

#### 创建公钥私钥

首先就是需要创建一个公钥，一个私钥，然后得到一个支付宝公钥。

相关下载链接：[链接](https://opendocs.alipay.com/common/02kipk?pathHash=0d20b438)
![[image/aefa8c505607afc4c2576d1bfa655ee4_MD5.png]]
下载之后双击运行：
![[image/3a0634cb6e0a0a529c89cedc93b46305_MD5.png]]
然后会得到相对应的公钥私钥的代码
![[image/ce66aa0caeaa14ebb645a516994915eb_MD5.png]]


然后到对应的使用的app上创建一个文件夹keys

内部放入创建一个新的新的文件：

复制私钥内容放在文件 private_2048

```python
-----BEGIN PRIVATE KEY-----
密钥
-----END PRIVATE KEY-----
```

然后对于得到的公钥，你也可以放入那个key当中的pub_2048内，这个可以不做，对于使用来说没什么差别，但是之后如果需要查询公钥放在这里比较好找。

公钥放入的格式：

```python
-----BEGIN PRIVATE KEY-----
公钥
-----END PRIVATE KEY-----
```

众所周知，公钥是要给别人使用的，所以我们的公钥实际上是要给支付宝进行使用的，到之前的那个沙箱的控制页面：[链接](https://open.alipay.com/develop/sandbox/app)
![[image/2944b58b6eadacb1fa8b72ad6e7e1230_MD5.png]]
![[image/3cc39a024f4e8b07b1a3bea59cdf0481_MD5.png]]
![[image/a6e7eb3d6c8aecab1391cc5fc434a669_MD5.png]]
然后把得到的支付宝公钥放到 alipay_key_2048,这个是我们去支付宝中查询订单状态的时候就会有用。

格式也是像上文的一样：
```python
-----BEGIN PRIVATE KEY-----
公钥
-----END PRIVATE KEY-----
```

至此就完成了支付宝的加密公钥私钥的控制

#### 使用的理论介绍
![[image/904834080c5104e2315d64cc0d76fd9c_MD5.png]]
首先先介绍一下具体使用流程中的整个流程。

具体的流程如上图，具体解释一下同步和异步的意思，这里的同步指的是我们用户在支付了商品的价格之后，会马上发送一个get请求，异步就是等待支付成功之后，会post返回一个请求，对于get常用的就是跳转页面，post常用的就是拿来判断他的支付状态。

#### 使用的代码介绍

首先就是关于测试的代码介绍：

```python
from datetime import datetime
from Crypto.PublicKey import RSA
from Crypto.Signature import PKCS1_v1_5
from Crypto.Hash import SHA256
from base64 import b64encode, b64decode
from urllib.parse import quote_plus
from urllib.parse import urlparse, parse_qs
from urllib.request import urlopen
from base64 import decodebytes, encodebytes

import json


class AliPay(object):
    """
    支付宝支付接口
    """
    def __init__(self, appid, app_notify_url, app_private_key_path,
                 alipay_public_key_path, return_url, debug=False):
        self.appid = appid  #支付宝分配给开发者的应用ID
        self.app_notify_url = app_notify_url  #回调url
        self.app_private_key_path = app_private_key_path
        self.app_private_key = None
        self.return_url = return_url
        #读取我们的私钥和alipay的公钥
        with open(self.app_private_key_path) as fp:
            self.app_private_key = RSA.importKey(fp.read())

        self.alipay_public_key_path = alipay_public_key_path
        with open(self.alipay_public_key_path) as fp:
            self.alipay_public_key = RSA.import_key(fp.read())


        if debug is True:
            self.__gateway = "https://openapi.alipaydev.com/gateway.do"
        else:
            self.__gateway = "https://openapi.alipay.com/gateway.do"

    def direct_pay(self, subject, out_trade_no, total_amount, return_url=None, **kwargs):
        #这里和官方文档中请求参数的必填写项次一样的
        biz_content = {
            "subject": subject,
            "out_trade_no": out_trade_no,
            "total_amount": total_amount,
            "product_code": "FAST_INSTANT_TRADE_PAY",
            # "qr_pay_mode":4
        }

        biz_content.update(kwargs)
        data = self.build_body("alipay.trade.page.pay", biz_content, self.return_url)
        return self.sign_data(data)
    #这里和我们公共请求参数一样的
    def build_body(self, method, biz_content, return_url=None):
        data = {
            "app_id": self.appid,
            "method": method,
            "charset": "utf-8",
            "sign_type": "RSA2",
            "timestamp": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
            "version": "1.0",
            "biz_content": biz_content
        }

        if return_url is not None:
            data["notify_url"] = self.app_notify_url
            data["return_url"] = self.return_url

        return data
    #签名，所有的支付宝请求都需要签名
    def sign_data(self, data):
        #如果data中有sign这个字段我们先清除
        data.pop("sign", None)
        # 排序后的字符串
        unsigned_items = self.ordered_data(data)
        unsigned_string = "&".join("{0}={1}".format(k, v) for k, v in unsigned_items)
        sign = self.sign(unsigned_string.encode("utf-8"))
        # ordered_items = self.ordered_data(data)
        #quote_plus主要是针对含有冒号双斜杠等进行处理
        quoted_string = "&".join("{0}={1}".format(k, quote_plus(v)) for k, v in unsigned_items)

        # 把sign加上，获得最终的订单信息字符串
        signed_string = quoted_string + "&sign=" + quote_plus(sign)
        return signed_string

    def ordered_data(self, data):
        complex_keys = []
        for key, value in data.items():
            if isinstance(value, dict):
                complex_keys.append(key)

        # 将字典类型的数据dump出来
        for key in complex_keys:
            data[key] = json.dumps(data[key], separators=(',', ':'))

        return sorted([(k, v) for k, v in data.items()])
    #按照支付宝官方给的手法签名
    def sign(self, unsigned_string):
        # 开始计算签名
        key = self.app_private_key
        signer = PKCS1_v1_5.new(key)
        signature = signer.sign(SHA256.new(unsigned_string))
        # base64 编码，转换为unicode表示并移除回车
        sign = encodebytes(signature).decode("utf8").replace("\n", "")
        return sign

    def _verify(self, raw_content, signature):
        # 开始计算签名，使用阿里给我们的公钥，对阿里返回的签名是否正确
        key = self.alipay_public_key
        signer = PKCS1_v1_5.new(key)
        digest = SHA256.new()
        digest.update(raw_content.encode("utf8"))
        if signer.verify(digest, decodebytes(signature.encode("utf8"))):
            return True
        return False

    def verify(self, data, signature):
        if "sign_type" in data:
            sign_type = data.pop("sign_type")
        # 排序后的字符串
        unsigned_items = self.ordered_data(data)
        message = "&".join(u"{}={}".format(k, v) for k, v in unsigned_items)
        return self._verify(message, signature)

#拿到支付宝返回给我们的url，进行解析，为了防止黑客截获，

if __name__ == "__main__":
    IP="47.115.45.50:8000" # 你对应的部署服务器的公网ip
    return_url = 'http://'+IP+'/alipay/return/?charset=utf-8&out_trade_n=20191115999&method=alipay.trade.page.pay.return&total_amount=100.00&sign=K1QkuZEX5nQDHzL%2BuCh3chDLesPXWyqmA2Trc5IYbH06jqUfAUle8mezNAFcGld6Lcv4KKXlwAs7a84y3yoYdjl7nxWaxk4sif%2DsWT6FvLJQsCjc8hsiE%2BDHLoeaiiHtJ9LsmYDtKyT4vUcg3yA3b3Q%2B4ybejLBQRjlu9r4WtlxO3oaloE880Ujwq4TthnVWzzeWMdIKdacCnnYVI9Fc2H1RdxfTQtRHXEWWW2dCBe5e4BzYOD7i4DQBYPtA4lFM%2FcTY700t0%2B7etjSzC5fS8l%2B6IO3Ea393UWVAvmJkzfYj9wRapai4qMh9KdR%2BEiGsBkXnl7lfXz9o767QQPOA%3D%3D&trade_no=2019111522001490421000021393&auth_app_id=2016101400687743&version=1.0&app_id=2016101400687743&sign_type=RSA2&seller_id=2088102179599265&timestamp=2019-11-15+17%3A08%3A40' #第一部分的代码实际上是测签用的，这边就只是这样子，这个页面的话实际上就是异步请求返回回来的页面
    o = urlparse(return_url)
    query = parse_qs(o.query)
    processed_query = {}
    ali_sign = query.pop("sign")[0]

    alipay = AliPay(
        appid="2016101401111111",# 放入应用ID,需要改
        app_notify_url="http://"+IP+"/alipay/return/",#异步的请求接收接口，就是用户扫描后，没支付，然后再次打开支付宝去支付
        app_private_key_path="../trade/keys/private_2048",
        alipay_public_key_path="../trade/keys/alipay_key_2048",  # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
        debug=True,  # 默认False,
        return_url="http://"+IP+"/alipay/return/"
    )

    for key, value in query.items():
        processed_query[key] = value[0]
    print (alipay.verify(processed_query, ali_sign)) #这一步做的就是测签

    url = alipay.direct_pay(
        subject="20200515163741149",#改成自己的订单ID
        out_trade_no="20200515163741149",#随便写一个测试用
        total_amount=100,
        return_url="http://"+IP+"/alipay/return/"#支付完成后跳回的页面
    )#我们要把这个url拿到我们的alipay的url中
    re_url = "https://openapi.alipaydev.com/gateway.do?{data}".format(data=url)

    print(re_url)
```
上文就是支付宝支付的大概的接口，下面讲解如何将其与Django联系起来

#### 支付宝与Drf的联合使用
我们通过上面的分析，就可以发现我们即需要处理 GET 请求，也需要处理 POST 请求，这时我们在对应的app文件夹下的 views 中新建：

```python
class AlipayView(APIView):
    #让页面从新回到我们的网站
    def get(self, request):
        """
        处理支付宝的return_url返回
        :param request:
        :return:
        """
        processed_dict = {}
        for key, value in request.GET.items():
            processed_dict[key] = value

        sign = processed_dict.pop("sign", None)
        server_ip = "你的远程服务器的ip"
        alipay = AliPay(
            appid = "你的应用appid",
            app_notify_url = "http://" + server_ip + "/alipay/return/",
            app_private_key_path = private_key_path,
            alipay_public_key_path = ali_pub_key_path,  # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
            debug = True,  # 默认False,
            return_url = "http://" + server_ip + "/alipay/return/"
        )
        verify_re = alipay.verify(processed_dict, sign)
        #这里我们可以调试看一下，如果verify_re为真，说明验证成功
        if verify_re is True:
            #同步的支付宝不再含有支付成功状态信息
            response = redirect("/index/#/app/home/member/order")
            # response.set_cookie("nextPath","pay", max_age=3)
            return response
        else:
            response = redirect("/index/#/app/home/member/order")
            return response

    def post(self, request):
        """
        处理支付宝的notify_url，异步的，支付宝发过来的是post请求
        :param request:
        :return:
        """
        processed_dict = {}

        for key, value in request.POST.items():
            processed_dict[key] = value

        sign = processed_dict.pop("sign", None)
        server_ip = "你的远程服务器的ip"
        alipay = AliPay(
            appid = "你的appid",
            app_notify_url = "http://" + server_ip + ":8000/alipay/return/",
            app_private_key_path = private_key_path,
            alipay_public_key_path = ali_pub_key_path,  # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
            debug = True,  # 默认False,
            return_url = "http://" + server_ip + ":8000/alipay/return/"
        )
        verify_re = alipay.verify(processed_dict, sign)

        if verify_re is True:
            order_sn = processed_dict.get('out_trade_no', None)
            trade_no = processed_dict.get('trade_no', None)
            trade_status = processed_dict.get('trade_status', None)

            existed_orders = OrderInfo.objects.filter(order_sn=order_sn)
            for existed_order in existed_orders:
                order_goods = existed_order.goods.all()
                for order_good in order_goods:
                    goods = order_good.goods
                    goods.sold_num += order_good.goods_num
                    goods.save()

                existed_order.pay_status = trade_status
                existed_order.trade_no = trade_no
                existed_order.pay_time = datetime.now()
                existed_order.save()

            return HttpResponse("success")#我们要给阿里response一个success，否则它会一直给我们消息
```

然后在 urls 中新增

```python
re_path(r'^alipay/return/', AlipayView.as_view(), name="alipay"),
```

具体的使用见上即可，而相关的加密算法，看个乐呵即可，不是专门的网安工作人员，或者算法工程师，这边就可以跳过。


## 后端部署
关于后端部署，这边稍微讲解一下流程，原因就是之前已经讲解过了，这边不细说，后文的前端部署再进行详细讲解。

后端部署：

第一步：
先检查自己的python版本的安装，以及pip安装的是不是pip3，是否做了短链接 pip -V

第二步：
安装控制虚拟环境的包


第三步:
根据项目的require.txt下载对应的包


第四步：
pycharm远程连接，并且构建相对应的服务器编译器，这边不细说

第五步：
创建项目，并且上传或者编写项目

第六步：
安全组打开对应的测试端口，并测试是否可以进行连接。


大概流程见上，当然还有部分是代码内部需要进行修改的，比如如果使用mysql的话，需要修改成使用服务器的mysql的，并且数据也需要重新构造之类的。

## 前端部署

在前端的终端当中执行

```python
npm run build
```
然后会在dist文件夹下生成一些文件，这些文件就是你对应的前端文件的综合了，然后需要按照下面的方式把前端的文件进行存储存放。
![[image/5787aa7782f2547d71d5fb570f12278c_MD5.png]]
由于默认没有static文件，所以我们需要到setting当中创建相关的static的默认路径：

```python
STATICFILES_DIRS = (os.path.join(BASE_DIR, "static"), )
```
并且由于路径不相同，所以我们需要把前端的index的页面的src也进行修改一下:

```python
修改 index.html 中的 src="index.entry.js"变为 src="/static/index.entry.js
```

然后我们在 urls 下面加入：
```python
re_path(r'^index/',TemplateView.as_view(template_name="index.html"),name="index"),
```
如此部署之后，再启动就可以直接看到对应的地址了，需要访问index的页面。

## 缓存简述

### local缓存
参考官方文档：[链接](http://chibisov.github.io/drf-extensions/docs/#cacheresponsemixin)

```python
from rest_framework_extensions.cache.mixins import CacheResponseMixin
```

然后把 CacheResponseMixin 放到 GoodsListViewSet 的第一个类就可以，注意只对 list 和 retrieve这些生效，因为是 get 类型请求，而 post 因为用户要提交数据，所以没有缓存的必要。

然后把想要用到缓存的地方view方法前加上GoodsListViewSet，去继承这个类即可，一定要先继承这个类。

然后全局设置更新时间：

```python
REST_FRAMEWORK_EXTENSIONS = {
'DEFAULT_CACHE_RESPONSE_TIMEOUT': 60 * 15
}

```

个人私有数据不适合做缓存，公用数据适合做缓存，比如不需要登录的商品页面


### redis 缓存
如果使用上面的 local 缓存，那么我们软件重启以后就会失效

先下载下面的三个：

```python
pip install django-redis

sudo apt install redis-server
```

然后到setting当中添加

```python
CACHES = { "default": { "BACKEND": "django_redis.cache.RedisCache", "LOCATION": "redis://127.0.0.1:6379", "OPTIONS": { "CLIENT_CLASS": "django_redis.client.DefaultClient", }
}
}
```

然后就是具体的分布式集群的开发，需要同步更新缓存的地方了，这边先跳过，后面会再说。


## 具体的前后端的共同部署nginx和uwgix


这边建议去网上看别人的教程，博主这边弄的很乱，但是弄完了，不想重新下载，就不做教程推荐了
