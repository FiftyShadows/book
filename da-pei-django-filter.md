django_filte 文档地址:https://django-filter.readthedocs.io/en/master/

####实现的效果如下
![](/assets/WX20171219-143642@2x.png)
![](/assets/WX20171219-143654@2x.png)

#####1. 首先记得 settings 里记得注册1`'django_filters'`
#####2. 再新建一个 filters 文件
```python
import django_filters
from .models import Article

class ArticleFilter(django_filters.rest_framework.FilterSet):
    """
    商品的过滤类
    """
    min = django_filters.NumberFilter(name='visit_num', help_text="最低价格", lookup_expr='gte')
    max = django_filters.NumberFilter(name='visit_num', lookup_expr='lte')
    key_words = django_filters.CharFilter(name='title', lookup_expr="contains")

    class Meta:
        model = Article
        fields = "__all__"

```
#####3. serializers.py 文件上写入
```python
class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = "__all__"
        # fields = ("category_name", "created_time", )


class ArticleSerializer(serializers.ModelSerializer):
    category = CategorySerializer(many=True)
    #这里要注意, category 是外键,要实例CategorySerializer
    class Meta:
        model = Article
        fields = "__all__"
```
#####4. views 里写入
```python
from apps.foreignKeyQuery.filters import ArticleFilter
from django_filters.rest_framework import DjangoFilterBackend
from rest_framework.pagination import PageNumberPagination
from rest_framework import filters

#这个是分页器
class ArticlePagination(PageNumberPagination):
    page_size = 1
    page_size_query_param = 'page_size'
    page_query_param = "page"
    max_page_size = 10


class ArticleListViewSet(mixins.ListModelMixin, mixins.RetrieveModelMixin, viewsets.GenericViewSet):
    """
    文章列表页, 分页， 搜索， 过滤， 排序
    """
    # throttle_classes = (UserRateThrottle, )
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    pagination_class = ArticlePagination
    
    filter_backends = (DjangoFilterBackend,filters.SearchFilter, filters.OrderingFilter)
    filter_class = ArticleFilter
    search_fields = ('title', 'content', 'author')
    ordering_fields = ('visit_num', 'collect_num')

```