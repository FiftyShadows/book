**具体的介绍文章写过了,记录一下实操代码,django 中使用**

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