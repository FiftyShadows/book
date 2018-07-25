Cannot uninstall 'requests'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.

错误的原因是requests 默认版本为2.6.0，而geoip2需要2.9以上版本才支持，但是无法正常卸载2.6.0版本。通过google查找后，发现是pip10对包的管理存在变化。

通过如下方式强制重新安装requests，问题得到解决：

        pip install -I requests==2.9

