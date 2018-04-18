玩 docker 的时候碰到的, mysql 容器用 navicate 死活连不上,可能是5.7的坑
一开始的初始化密码不能为远程提供
需要进去执行语句 update mysql.user set authentication_string = password('root') where user = 'root';