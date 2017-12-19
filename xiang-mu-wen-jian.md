####models文件serializers和 views 我会从简单到难讲,不在这里写了
```python
class User(models.Model):
	account = models.CharField(max_length=15, verbose_name='账号', unique=True)
	password = models.CharField(max_length=15, verbose_name='密码')
	SEX_CHOICE = {'1': '男', '2': '女'}
	nickname = models.CharField(max_length=20, verbose_name='昵称',null=True,blank=True)
	address = models.CharField(max_length=60, verbose_name='地址',null=True,blank=True)
	age = models.IntegerField(verbose_name='作者年纪')
	sex = models.CharField(choices=SEX_CHOICE.items(), verbose_name='性别',max_length=10)
	created_time = models.DateTimeField(auto_now_add=True, verbose_name='用户创建时间')
	head_img = models.ImageField(upload_to='head_img',null=True,blank=True)

	def __str__(self):
		return self.nickname

	class Meta:
		db_table = 'author'
		managed = True
		verbose_name = '作者'
		verbose_name_plural = verbose_name


class Category(models.Model):

	category_name = models.CharField(max_length=40, verbose_name='分类名')
	created_time = models.DateTimeField(auto_now_add=True, verbose_name='分类创建时间')

	def __str__(self):
		return self.category_name

	class Meta:
		db_table = 'category'
		managed = True
		verbose_name = '分类'
		verbose_name_plural = verbose_name


class Article(models.Model):
	title = models.CharField(max_length=25, unique=True, verbose_name='标题')

	category = models.ManyToManyField(Category, verbose_name='文章分类')
	author = models.ForeignKey(User, related_name='user_author', verbose_name='文章作者')

	content = models.TextField(verbose_name='文章内容')
	created_time = models.DateField(auto_now_add=True, verbose_name='文章创建时间')
	visit_num = models.IntegerField(verbose_name='浏览量')
	collect_num = models.IntegerField(verbose_name='收藏量')

	def __str__(self):
		return self.title

	class Meta:
		ordering = ['visit_num']
		db_table = 'article'
		managed = True
		verbose_name = '文章'
		verbose_name_plural = verbose_name


class Comment(models.Model):
	commenter = models.ForeignKey(User, max_length=20, null=True, verbose_name='评论者')
	comment_content = models.TextField(verbose_name='评论内容')
	created_time = models.DateTimeField(auto_now_add=True, verbose_name='评论创建时间')
	article = models.ForeignKey(Article, verbose_name='评论所属文章')

	def __str__(self):
		return self.comment_content[0:8]

	class Meta:
		db_table = 'comment'
		managed = True
		verbose_name = '评论'
		verbose_name_plural = verbose_name
```
<br>
