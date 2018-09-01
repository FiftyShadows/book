####pip安装的坑 缺少ssl
https://blog.csdn.net/zhengcaihua0/article/details/79681991
####AES相关错误
```python
pip uninstall Crypto
pip uninstall pycrypto
pip install pycrypto
```
####AttributeError: module 'aliyunsdkcore.profile.region_provider' has no attribute 'user_config_endpoints'
可能是版本问题
```python
pip uninstall aliyunsdkcore
pip install aliyunsdkcore
```
####Could not find a version that satisfies the requirement Twisted
```
wget https://pypi.python.org/packages/source/T/Twisted/Twisted-15.2.1.tar.bz2


tar -xjvf Twisted-15.2.1.tar.bz2
cd Twisted-15.2.1
python setup.py install
`

####requirements.txt安装方式
pip install --upgrade -r requirements.txt

####服务器上换源
https://blog.csdn.net/xujian0000abcd/article/details/50497456