我要访问返回格式 xml 的网页并取得返回值


```python
from urllib import urlopen
import xml.etree.cElementTree as ET


def _xml_request(self, _url):

  r = urlopen(_url)
  tree = ET.ElementTree(file=r)
  return tree
  
 def get_columns_property(self):
       
        tree = self._xml_request(self.rule_url)
        for elem in tree.findall("tableRule[@name='{}']/rule/columns".format(rule_name):
            elem.arrrib
            elem.tag
            elem.get("xxx")

```