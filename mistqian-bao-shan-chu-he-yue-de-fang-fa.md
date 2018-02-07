点击  开发-->开发者工具-->ethereum waleet
会跳出一个 console 台  ,先`CustomContracts.find().fetch()`然后复制`_ id`,
再`CustomContracts.remove('the_id_you_copied')`

如果全部删除的话
```
CustomContracts.find().fetch().map(
   function(m) { CustomContracts.remove(m._id);}
)
```