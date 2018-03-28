![](/assets/WX20180328-165818@2x.png)

github:https://github.com/matryer/bitbar

###文件名为mybitbar.20s.py,我放在了文档目录下
20s 是刷新频率
```python
#!/usr/bin/python
# -*- coding: utf8 -*-
#
# <bitbar.title>Title goes here</bitbar.title>
# <bitbar.version>v1.0</bitbar.version>
# <bitbar.author>沈嘉诚</bitbar.author>
# <bitbar.author.github>qq976739120</bitbar.author.github>
# <bitbar.desc>Short description of what your plugin does.</bitbar.desc>
# <bitbar.image>http://www.hosted-somewhere/pluginimage</bitbar.image>
# <bitbar.dependencies>python</bitbar.dependencies>
#


import urllib2
import json
json_ =json.loads(urllib2.urlopen("https://api.coinmarketcap.com/v1/ticker/?convert=CNY&limit=5").read())

for i in json_:
    print(i.get('symbol') +":" +"{:.2f}".format(float(i.get('price_cny'))))
```