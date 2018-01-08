最方便的方法是使用是这个库:https://github.com/martinblech/xmltodict
但是除非批量处理,如果只是处理单个文件的话引入一个库很不明智,领导也不会同意你这样做,我自己封装了一个,你只要复制代码就能使用.
我的 github:https://github.com/qq976739120/xmlTodict
```python
# -*- coding: utf8 -*-
from collections import defaultdict
def etree_to_dict(t):
    d = {t.tag: {} if t.attrib else None}
    children = list(t)
    if children:
        dd = defaultdict(list)
        for dc in map(etree_to_dict, children):
            for k, v in dc.iteritems():
                dd[k].append(v)
        d = {t.tag: {k:v[0] if len(v) == 1 else v for k, v in dd.iteritems()}}
    if t.attrib:
        d[t.tag].update(('@' + k, v) for k, v in t.attrib.iteritems())
    if t.text:
        text = t.text.strip()
        if children or t.attrib:
            if text:
              d[t.tag]['#text'] = text
        else:
            d[t.tag] = text
    return d
#-----------------------------------------------------------
from xml.etree import cElementTree as ET
e = ET.XML('''
<root>
  <e />
  <e>text</e>
  <e name="value" />
  <e name="value">text</e>
  <e> <a>text</a> <b>text</b> </e>
  <e> <a>text</a> <a>text</a> </e>
  <e> text <a>text</a> </e>
</root>
''')
from pprint import pprint
d = etree_to_dict(e)
pprint(d)
# {'root': {'e': [None,
#                 'text',
#                 {'@name': 'value'},
#                 {'#text': 'text', '@name': 'value'},
#                 {'a': 'text', 'b': 'text'},
#                 {'a': ['text', 'text']},
#                 {'#text': 'text', 'a': 'text'}]}}
#-----------------------------------------------------------
# dictToxml
try:
  basestring
except NameError:  # python3
  basestring = str
def dict_to_etree(d):
    def _to_etree(d, root):
        if not d:
            pass
        elif isinstance(d, basestring):
            root.text = d
        elif isinstance(d, dict):
            for k,v in d.items():
                assert isinstance(k, basestring)
                if k.startswith('#'):
                    assert k == '#text' and isinstance(v, basestring)
                    root.text = v
                elif k.startswith('@'):
                    assert isinstance(v, basestring)
                    root.set(k[1:], v)
                elif isinstance(v, list):
                    for e in v:
                        _to_etree(e, ET.SubElement(root, k))
                else:
                    _to_etree(v, ET.SubElement(root, k))
        else: assert d == 'invalid type'
    assert isinstance(d, dict) and len(d) == 1
    tag, body = next(iter(d.items()))
    node = ET.Element(tag)
    _to_etree(body, node)
    return ET.tostring(node)
pprint(dict_to_etree(d))
```
