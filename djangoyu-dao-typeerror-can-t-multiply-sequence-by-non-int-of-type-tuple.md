先`python manage.py makemigrations`再`python manage.py makemigrations`
遇到了`TypeError: can't multiply sequence by non-int of type 'tuple'`
用到的 mysql 驱动是 `mysql-connector==2.1.4`
好吧，这里，我试了N种办法，都没有解决，最后没办法，只能修改一下mysql驱动源码了。找到mysql驱动的安装位置里的一个叫做operations.py的文件。比如我的目录是这样的：E:\Python34\Lib\site-packages\mysql\connector\django，如下图这个文件： 
![](/assets/11111111.png)

有个`bulk_insert_sql`方法,修改
```python
def bulk_insert_sql(self, fields, num_values):
        # items_sql = "({0})".format(", ".join(["%s"] * len(fields)))
        # return "VALUES " + ", ".join([items_sql] * num_values)
        placeholder_rows_sql = (", ".join(row) for row in num_values)
        values_sql = ", ".join("(%s)" % sql for sql in placeholder_rows_sql)
        return "VALUES " + values_sql`
```
