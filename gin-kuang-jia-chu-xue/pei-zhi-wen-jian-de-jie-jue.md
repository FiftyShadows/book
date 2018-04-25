一个超赞的 ini 文件库
地址  https://ini.unknwon.io/

我的app.int文件
```ini
RUN_MODE = debug



[server]
HTTP_PORT = 8000
READ_TIMEOUT = 60
WRITE_TIMEOUT = 60

[database]
TYPE = mysql
USER = root
PASSWORD = 63387748sjc
HOST =127.0.0.1:3306
NAME = golang_test
#TABLE_PREFIX = blog_
```
这样就可以读到我的配置文件
```go 
var Cfg *ini.File

var err error
Cfg, err = ini.Load("conf/app.ini")
if err != nil {
log.Fatalf("Fail to parse 'conf/app.ini': %v", err)
}

LoadBase()
LoadServer()

func LoadBase() {
	RunMode = Cfg.Section("").Key("RUN_MODE").MustString("debug")
}

func LoadServer() {
	sec, err := Cfg.GetSection("server")
	if err != nil {
		log.Fatalf("Fail to get section 'server': %v", err)
	}

	RunMode = Cfg.Section("").Key("RUN_MODE").MustString("debug")

	HTTPPort = sec.Key("HTTP_PORT").MustInt(8000)
}


var (
		err error
		dbType, dbName, user, password, host string
	)

	sec, err := setting.Cfg.GetSection("database")
	if err != nil {
		log.Fatal(2, "Fail to get section 'database': %v", err)
	}

	dbType = sec.Key("TYPE").String()
	dbName = sec.Key("NAME").String()
	user = sec.Key("USER").String()
	password = sec.Key("PASSWORD").String()
	host = sec.Key("HOST").String()

```