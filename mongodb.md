####可能很多理解上的错误,尽量只贴代码,用的是pymongo
#####一张表类似excel的一个sheet
**show dbs  查看所有的数据库
use XXX 使用哪个库
show collections 看这个库下的表
db.xxxx.find() xxxx 是其中的表名**

```python
import pymongo

client = pymongo.MongoClient('localhost',27017)

walden = client['walden']#给数据库名称,相当与一个excel文件的文件名 左边的是python的对象
sheet_tab = walden['sheet_tab']#一个表
sheet_tab.insert_one({'name':'shen',"age":22})

```