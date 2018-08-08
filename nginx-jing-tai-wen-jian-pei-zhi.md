```conf
server{
    listen 7001;
    server_name 0.0.0.0;
    
    root /Users/jay/Desktop/item;
    location ~ ^/(upload|static)/ 
        {
            root   /Users/jay/Desktop/item/dist;
            index  index.html;
        }
}

```
访问0.0.0.0:70001/dist/index.html即可
```
 jay@jaydeMacBook-Pro  ~  cd /Users/jay/Desktop/item
 jay@jaydeMacBook-Pro  ~/Desktop/item  tree
.
├── css
│   ├── authentication.css
│   ├── common.css
│   ├── footer.css
│   ├── header.css
│   ├── index.css
│   ├── introduce.css
│   ├── login.css
│   └── userpage.css
├── dist
│   ├── index.html
│   ├── introduce.html
│   ├── operation
│   │   ├── login.html
│   │   ├── mailClick.html
│   │   ├── phoneClick.html
│   │   ├── reg.html
│   │   ├── resetSelect.html
│   │   └── resetpsw.html
│   ├── pic.html
│   └── userpage
│       ├── bindmail.html
│       ├── bindphone.html
│       ├── firstCertified.html
│       ├── idCard.html
│       ├── modify-mail.html
│       ├── modify-phone.html
│       ├── passport.html
│       └── userpage.html
├── imgs
│   ├── Group\ 347.png
│   ├── Repeat\ Grid\ 1.svg
│   ├── Shape.svg
│   ├── Subtraction\ 3.svg
│   ├── Superscript.svg
│   ├── banner.png
│   ├── fairy-share.png
│   ├── favicon.ico
│   ├── favicon.svg
│   ├── head-img.svg
│   ├── head-portrait.png
│   ├── logo.png
│   ├── mail.svg
│   ├── notice.png
│   ├── phone.svg
│   ├── positive.svg
│   ├── qr-code.svg
│   └── vcode.svg
├── js
│   ├── api.js
│   ├── helper.js
│   ├── index.js
│   ├── lib
│   │   ├── echarts.js
│   │   ├── jquery-3.3.1.js
│   │   └── jquery-3.3.1.min.js
│   ├── request.js
│   └── verification.js
└── src
    └── include
        ├── css
        ├── footer.html
        ├── header.html
        └── js
            ├── footer.js
            └── header.js
```