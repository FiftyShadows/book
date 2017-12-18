先安装,不多讲了:http://www.django-rest-framework.org/#installation
<br>
实现如下效果
![](/assets/WX20171218-160815@2x.png)
#####urls 层
```python
from rest_framework.routers import DefaultRouter
from apps.foreignKeyQuery.views import CategoryListViewSet

router = DefaultRouter()
router.register(r'categorys', CategoryListViewSet, base_name="categorys")
```
#####serializers 层
```python
from rest_framework import serializers
from apps.foreignKeyQuery.models import Category

class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        # fields = "__all__"
        fields = ("category_name", "created_time", )
```
#####views 层
```python
class CategoryListViewSet(viewsets.ModelViewSet):

    queryset = Category.objects.all()
    serializer_class = CategorySerializer
```