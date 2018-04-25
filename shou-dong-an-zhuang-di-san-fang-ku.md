有些库例如 swag 怎么都装不上,手动安装.
首先克隆 swag 库到 src/github 文件夹内,还是缺各种依赖.
到 gopath 位置下新建golang.org/x,然后缺的各种依赖在这里找 github.com/golang?tab=repositories,再克隆到 x 里去
,克隆完 cd 到 swag 执行` go install github.com/swaggo/swag`
我也上传了下载好的包,网盘地址