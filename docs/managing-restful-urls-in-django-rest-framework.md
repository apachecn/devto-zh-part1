# 在 Django Rest 框架中管理 RESTful URLs

> 原文：<https://dev.to/enether/managing-restful-urls-in-django-rest-framework>

我们都学过 RESTful API 设计。很容易意识到这些端点

```
POST /products/1/delete
POST /products/1/update
GET /products/1 
```

不如

```
DELETE /products/1
PUT /products/1
GET /products/1 
```

你也可以想象，每个对象的多个 URL 很快堆积起来，如果我们引入更多像`/merchants/`、`/shops/`这样的对象，我们将很快管理大量的 URL，这会变得混乱。没有人想阅读一个 100 行的 *urls.py* 文件。

但是 Django Rest 框架不支持基于请求方法将同一个 URL 映射到不同的类视图。我们如何在我们的 *urls.py* 文件中用不同的方法映射这个 URL 呢？

让我们首先用错误的 URL 创建初始项目

# 初始项目

我们将有一个非常简单的项目，允许我们与产品对象进行交互。我们希望能够更新产品，获得有关它的信息，并删除它。

*models.py*

```
from django.db import models
from rest_framework import serializers

class Product(models.Model):
    name = models.CharField(max_length=500)
    price = models.DecimalField(decimal_places=2, max_digits=5)
    stock = models.IntegerField()

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = '__all__' 
```

*views.py*

```
from rest_framework.generics import DestroyAPIView, UpdateAPIView, RetrieveAPIView

from restful_example.models import Product, ProductSerializer

class ProductDestroyView(DestroyAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

class ProductUpdateView(UpdateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

class ProductDetailsView(RetrieveAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer 
```

还有我们丑陋的 *urls.py*

```
from django.conf.urls import url
from django.contrib import admin
from restful_example.views import ProductDestroyView, ProductUpdateView, ProductDetailsView

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^products/(?P<pk>\d+)/delete$', ProductDestroyView.as_view()),
    url(r'^products/(?P<pk>\d+)/update$', ProductUpdateView.as_view()),
    url(r'^products/(?P<pk>\d+)$', ProductDetailsView.as_view()),
] 
```

等等，别忘了考试！

*tests.py*

```
from rest_framework.test import APITestCase

from restful_example.models import Product, ProductSerializer

class ProductTests(APITestCase):
    def test_can_get_product_details(self):
        product = Product.objects.create(name='Apple Watch', price=500, stock=3)
        response = self.client.get(f'/products/{product.id}')
        self.assertEqual(response.status_code, 200)
        self.assertEqual(response.data, ProductSerializer(instance=product).data)

    def test_can_delete_product(self):
        product = Product.objects.create(name='Apple Watch', price=500, stock=3)
        response = self.client.delete(f'/products/{product.id}/delete')
        self.assertEqual(response.status_code, 204)
        self.assertEqual(Product.objects.count(), 0)

    def test_can_update_product(self):
        product = Product.objects.create(name='Apple Watch', price=500, stock=3)
        response = self.client.patch(f'/products/{product.id}/update', data={'name': 'Samsung Watch'})
        product.refresh_from_db()
        self.assertEqual(response.status_code, 200)
        self.assertEqual(product.name, 'Samsung Watch') 
```

运行我们的测试，你会看到这是可行的。

# 变得更加宁静

好了，现在是时候把我们的网址改成更合理的了。正如我们所说的，我们希望所有这些视图都指向一个确切的 URL，不同之处仅在于它们允许的方法。

这里的想法是有某种基本视图，url 上的请求被发送到该视图。这个视图的任务是找出哪个视图应该处理给定方法的请求，并把它发送到那里。

```
class BaseManageView(APIView):
    """
    The base class for ManageViews
        A ManageView is a view which is used to dispatch the requests to the appropriate views
        This is done so that we can use one URL with different methods (GET, PUT, etc)
    """
    def dispatch(self, request, *args, **kwargs):
        if not hasattr(self, 'VIEWS_BY_METHOD'):
            raise Exception('VIEWS_BY_METHOD static dictionary variable must be defined on a ManageView class!')
        if request.method in self.VIEWS_BY_METHOD:
            return self.VIEWS_BY_METHOD[request.method]()(request, *args, **kwargs)

        return Response(status=405) 
```

这个简单的类需要我们继承它并定义一个名为`VIEWS_BY_METHOD`的类变量。一个保存我们的方法名及其适当的处理程序的字典。
使用这个基类，为我们的产品模型创建 ManageView 类很简单:

```
class ProductManageView(BaseManageView):
    VIEWS_BY_METHOD = {
        'DELETE': ProductDestroyView.as_view,
        'GET': ProductDetailsView.as_view,
        'PUT': ProductUpdateView.as_view,
        'PATCH': ProductUpdateView.as_view
    } 
```

这里值得一提的是，任何[权限类](http://www.django-rest-framework.org/api-guide/permissions/)都必须在单独的视图中定义，如果放在`ManageView`中将不起作用

我们需要快速编辑我们的**URL . py**的`urlpatterns`

```
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^products/(?P<pk>\d+)$', ProductManageView.as_view()),  # this now points to the manage view
] 
```

让我们也测试一下这个新视图
**tests.py**

```
class ProductManageViewTests(APITestCase):
    def test_method_pairing(self):
        self.assertEqual(len(ProductManageView.VIEWS_BY_METHOD.keys()), 4)  # we only support 4 methods
        self.assertEqual(ProductManageView.VIEWS_BY_METHOD['DELETE'], ProductDestroyView.as_view)
        self.assertEqual(ProductManageView.VIEWS_BY_METHOD['GET'], ProductDetailsView.as_view)
        self.assertEqual(ProductManageView.VIEWS_BY_METHOD['PUT'], ProductUpdateView.as_view)
        self.assertEqual(ProductManageView.VIEWS_BY_METHOD['PATCH'], ProductUpdateView.as_view)

    def test_non_supported_method_returns_405(self):
        product = Product.objects.create(name='Apple Watch', price=500, stock=3)
        response = self.client.post(f'/products/{product.id}')
        self.assertEqual(response.status_code, 405) 
```

我们改变以前测试的 URL 来使用新的，我们可以看到它们通过了

```
Creating test database for alias 'default'...
...
----------------------------------------------------------------------
Ran 3 tests in 0.037s

OK
Destroying test database for alias 'default'... 
```

瞧，我们有相同的功能，但在一个网址！

# 总结

我们所做的是创建一个主视图，它根据请求方法将请求分派给适当的视图。我相信，当你想用不同的基于类的视图来处理每个方法时，这是在 DRF 处理每个 URL 的多个方法的正确方法。

然而，这不是我们这个简单例子的最佳情况。对于有些过于普通的视图(比如我们的，内部没有定制逻辑)或功能视图，有路由器和视图集

# 奖励-路由器和视图集

因为 DRF 太棒了，他们为我们提供了在一个模型上用几行代码定义所有基本操作的能力。使用视图集类，我们可以定义我们的创建、读取、更新和销毁逻辑，而无需编写任何代码。

*views.py*

```
from rest_framework import viewsets
class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer 
```

我们需要使用路由器类在我们的 urls.py 中定义 URL

```
from rest_framework.routers import DefaultRouter
router = DefaultRouter(trailing_slash=False)
router.register(r'products', restful_views.ProductViewSet)

urlpatterns = [
    url(r'^admin/', admin.site.urls),
]

urlpatterns += router.urls 
```

信不信由你，这个视图集拥有我们上面实现的所有功能，另外还有一些附加功能，比如创建一个产品(POST /products)或获取所有产品的列表(GET /products)

视图集还允许您覆盖视图并创建自定义视图。这允许我们为一个 URL 创建函数视图，而不是基于类的视图。对于我们的例子和其他简单的项目来说，它们绝对是惊人的，但是对于管理多个基于类的、内部具有定制逻辑的非泛型视图来说，却是不尽人意的。