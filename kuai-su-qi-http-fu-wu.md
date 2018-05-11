新建一个文件夹,文件夹里存好你要访问的文件,再 cd 到这目录里

python2 `python -m SimpleHTTPServer 8888`
python3 `python -m http.server 8888`


服务器上快速起服务访问 HTML文件

写个 r.sh 文件
```
python -m SimpleHTTPServer 8888 > 88.log 2>&1 &
```
再` sh ./r.sh`

访问 django 项目
`python3  manage.py runserver 0.0.0.0:80 > /dev/null 2>&1 &`