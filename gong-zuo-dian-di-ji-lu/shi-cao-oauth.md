**具体的介绍文章写过了,没有借助框架,手动实现,记录一下实操代码,django 中使用**

**settings**
```python
OAUTH2_CLIENT_ID = "jIhzpdsfadsfbBEcr4uS"#已经修改过,请勿尝试
OAUTH2_CLIENT_SECRET = "GNy5pfTk24h61sdfasJ3AiCbX8P9RuQj"
OAUTH2_REDIRECT_URL = "http://test-app.localhost.com:8000/oauth2_callback/"
OAUTH2_AUTHORIZE_URL = "https://yops.bestpay.com.cn/o/authorize/"
INTERNAL_URL_PREFIX = "https://yops.bestpay.com.cn"
OAUTH2_TOKEN_URL = INTERNAL_URL_PREFIX + "/o/token/"
OAUTH2_USER_BASE_INFO_URL = INTERNAL_URL_PREFIX + "/public-api/current_user/base_info"
OAUTH2_USER_ALL_PERMISSIONS_URL = INTERNAL_URL_PREFIX + "/public-api/current_user/all_permissions"
#这个必须添加
AUTHENTICATION_BACKENDS = [
    'OAuth.backends.OAuth2LoginBackend',
    'django.contrib.auth.backends.ModelBackend',
]
```

**新建一个backends.py 文件,重写 django 自带的认证**
```python
# -*- coding: utf8 -*-
#!/bin/env python
import sys
reload(sys)
sys.setdefaultencoding('utf8')
import requests
import django.contrib.auth.backends
from django.contrib.auth import get_user_model
import logging

from django.conf import settings

logger = logging.getLogger(__name__)

class OAuth2LoginBackend(django.contrib.auth.backends.ModelBackend):
    """
    OAuth2 登录            logger.info("user:{0} 是已存在的用户".format(username))

    """

    def __init__(self):
        super(OAuth2LoginBackend, self).__init__()
        self.redirect_uri = settings.OAUTH2_REDIRECT_URL
        self.client_id = settings.OAUTH2_CLIENT_ID
        self.client_secret = settings.OAUTH2_CLIENT_SECRET
        self.authorize_url = settings.OAUTH2_AUTHORIZE_URL
        self.token_url = settings.OAUTH2_TOKEN_URL
        self.user_base_info_url = settings.OAUTH2_USER_BASE_INFO_URL
        self.user_all_permissions_url = settings.OAUTH2_USER_ALL_PERMISSIONS_URL

    def authenticate(self, authorization_code=None, **kwargs):
        # 使用Authorization_Code获取Access_Token
        if authorization_code is None:
            return None

        data = {
            "grant_type": "authorization_code",
            "code": authorization_code,
            "redirect_uri": self.redirect_uri,
            "client_id": self.client_id,
            "client_secret": self.client_secret,
        }
        # authorization_header_content = "{0}:{1}".format(self.client_id, self.client_secret)
        headers = {
            # "Authorization": "Basic {0}".format(base64.b64encode(authorization_header_content))
        }
        resp = requests.post(self.token_url, data=data, headers=headers)

        if str(resp.status_code) == "200":

            token_resp_dict = resp.json()

            access_token = token_resp_dict.get("access_token")
            print "ops 平台返回 access_token:",access_token
            token_type = token_resp_dict.get("token_type")
            logger.info("oauth2.0 D步骤执行完毕, access_token 为 {0}:{1}".format(token_type, access_token))

        else:
            logger.error("oauth2 登录失败, http status code: {0}".format(resp.status_code))
            logger.error(resp.text)
            # raise common.errors.OAuth2Error(
            #     "在oauth2 登录过程中, post token url返回结果不正常, http status code:{0}".format(resp.status_code))
            return None

        # 开始获取用户基础信息:
        user_base_info_dict = self.get_user_base_info(access_token=access_token)
        print '用户信息',user_base_info_dict
        # get_user_all_permissions_dict = self.get_user_all_permissions(access_token=access_token)
        logger.info("oauth2 登录成功, 获取用户基础信息成功, user_base_info_dict:{0}".format(str(user_base_info_dict)))

        username = user_base_info_dict.get("name")
        user, created = self.get_or_create_user(username=username)
        user.email = user_base_info_dict.get("email")
        user.first_name = user_base_info_dict.get("name_cn")
        user.save()

        if created:
            logger.info("user:{0} 首次登录系统, 为其自动创建账户".format(username))
        else:

            return user

    def get_user_base_info(self, access_token):
        """
        获取用户的基础信息
        :return:
        """
        oauth2_headers = {
            "Authorization": "Bearer {0}".format(access_token)
        }
        api_resp = requests.get(self.user_base_info_url, headers=oauth2_headers)
        return api_resp.json()

    def get_user_all_permissions(self, access_token):
        """
        用户所有权限信息
        :param access_token:
        :return:
        """
        oauth2_headers = {
            "Authorization": "Bearer {0}".format(access_token)
        }
        api_resp = requests.get(self.user_all_permissions_url, headers=oauth2_headers)
        return api_resp.json()

    def get_or_create_user(self, username):
        """
        在本系统的数据库中查找对应的用户, 找不到就创建一个
        :param username:
        :return: User
        """
        created = False
        UserModel = get_user_model()
        try:
            # user = UserModel._default_manager.get(username=username)
            user = UserModel._default_manager.get_by_natural_key(username)
        except UserModel.DoesNotExist:
            logger.debug("用户{0}未在本系统中注册, 为其自动新建一个用户".format(username))
            user = UserModel._default_manager.create_user(username=username)
            # 让这个用户无法使用密码登录
            user.set_unusable_password()
            user.is_active = True
            user.save()
            created = True
        return user, created
```

**views**
```python
# -*- coding: utf8 -*-

from django.shortcuts import render,redirect
import logging
import io
import logging
import datetime
from django.views.generic import View
from django.contrib.auth import authenticate, login, logout
from django.http import HttpResponseRedirect, JsonResponse, HttpResponse
from OAuth.models import Token
from django.core.cache import cache
from rest_framework.views import APIView
from django.contrib.auth.models import User

logger = logging.getLogger(__name__)


# Create your views here.
class OAuth2CallbackView(View):
    def get(self, request, *args, **kwargs):
        print '进入 get 方法'
        logger.info("收到来自 auth server 的 callback, 开始 oauth2.0 D步骤")
        code = request.GET.get("code")
        print '授权码:',code
        if code is None:
            print '授权码不存在'
            raise AttributeError("code can NOT be None")

        user = authenticate(authorization_code=code)

        if user:
            logger.info("user:{0} 登录成功".format(user.username))
            login(request, user)
            # one_time_key = self.save_token_key_in_cache(user=user)
            # logger.info("user:{0} 通过 oauth2 登录成功".format(user.username))
            # return HttpResponseRedirect("/index?code={}".format(one_time_key))
            return HttpResponseRedirect("/index")
        else:
            # raise common.errors.OAuth2Error("OAuth2 登录失败")
            logger.info("登录失败")
            return HttpResponseRedirect("/login")

    def save_token_key_in_cache(self, user):
        token = self.get_access_token(user=user)
        print "Token表token:",token
        # 随机产生一个 key
        one_time_key = token.generate_key()
        cache_key = "access_token_read_key:{0}".format(one_time_key)
        cache.set(cache_key, token.key, timeout=120)
        return one_time_key

    def get_access_token(self, user):
        """
        按照用户返回 access token , 如果这个 key 时间过期了,自动更新 key
        :param user:
        :return:
        """
        token, created = Token.objects.get_or_create(user=user)
        if not created and (token.created < datetime.datetime.now() - datetime.timedelta(days=7)):
            # 当这个 key 的时间超过7天后, 需要删除并重建
            logger.info("用户 {0} 的 token 已存活超过7天期限, 不再安全, 重新生成 key ".format(user.username))
            token.delete()
            # 新创建一个 token 给这个用户
            token = Token(user=user)
            token.key = token.generate_key()
            token.save()
        return token


class OAuth2GetTokenView(View):
    """

    """
    def get(self, request, *args, **kwargs):
        print  'token 函数'
        one_time_key = request.GET.get("one_time_key")
        token_key = cache.get("access_token_read_key:{0}".format(one_time_key))
        cache.delete(token_key)
        return JsonResponse({"token": token_key})


def login_func(request):
    return render(request, 'login.html')

def logout_func(request):
    logout(request)
    return HttpResponseRedirect("/login")

def index(request):

    # one_time_key = request.GET.get("code")
    # token_key = cache.get("access_token_read_key:{0}".format(one_time_key))
    #
    # user_id = Token.objects.get(key=str((token_key))).user_id
    # user_name = User.objects.get(id = user_id).first_name


    return render(request,'index.html',locals())
```

**urls**

```python
from django.conf.urls import url
from django.contrib import admin
from OAuth.views import *
from django.views.generic import RedirectView

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^$', RedirectView.as_view(url='login.html')),
    url(r'oauth2_callback/$',OAuth2CallbackView.as_view()),
    # url(r'^$', RedirectView.as_view(url='/static/src/_index.html')),
    url(r'api/get_token/$', OAuth2GetTokenView.as_view()),
    url(r'login/$', login_func,name='login'),
    url(r'logout/$', logout_func,name='logout'),
    url(r'index/$', index, name='index'),

]
```