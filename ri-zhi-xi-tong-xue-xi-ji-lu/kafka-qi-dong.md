#### 打开 zookeeper
```
cd /usr/local/Cellar/kafka/1.0.0
./bin/zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties &
```
#### 打开 kafka
```
cd /usr/local/Cellar/kafka/1.0.0

./bin/kafka-server-start /usr/local/etc/kafka/server.properties &
```
#### go 写个 topic

```go 
package main

import (
	"github.com/Shopify/sarama"
	"fmt"
	"strings"
	"time"
)

func main() {
	config := sarama.NewConfig()
	config.Producer.RequiredAcks = sarama.WaitForAll
	config.Producer.Partitioner = sarama.NewRandomPartitioner
	config.Producer.Return.Successes = true
	msg := &sarama.ProducerMessage{}
	msg.Topic = "nginx_log"
	msg.Value = sarama.StringEncoder("this is a good test,my message is shen")
	client,err := sarama.NewSyncProducer(strings.Split("localhost:9092", ","),config)
	 //client, err := sarama.NewSyncProducer([]string{"192.168.179.130:9092"}, config)
	if err != nil{
		fmt.Println("producer close err:",err)
		return
	}
	defer client.Close()

	for{
		pid,offset,err := client.SendMessage(msg)
		if err != nil{
			fmt.Println("send message failed,",err)
			return
		}
		fmt.Printf("pid:%v offset:%v\n",pid,offset)
		time.Sleep(2*time.Second)
	}
}
```

####启动一个消费者查看

```
./bin/kafka-console-consumer --bootstrap-server localhost:9092 --topic nginx_log --from-beginning
```

<br>
###读日志文件并且将读到的日志文件推送到kafka


```go
package main

import (
    "github.com/hpcloud/tail"
    "fmt"
    "time"
)

func main() {
    filename := "/Users/zhaofan/go_project/src/go_dev/13/tailf/my.log"
    tails,err := tail.TailFile(filename,tail.Config{
        ReOpen:true,
        Follow:true,
        Location:&tail.SeekInfo{Offset:0,Whence:2},
        MustExist:false,
        Poll:true,
    })

    if err !=nil{
        fmt.Println("tail file err:",err)
        return
    }

    var msg *tail.Line
    var ok bool
    for true{
        msg,ok = <-tails.Lines
        if !ok{
            fmt.Printf("tail file close reopen,filenam:%s\n",tails,filename)
            time.Sleep(100*time.Millisecond)
            continue
        }
        fmt.Println("msg:",msg.Text)
    }
}
```

###日志库的使用

```go
package main

import (
    "github.com/astaxie/beego/logs"
    "encoding/json"
    "fmt"
)

func main() {
    config := make(map[string]interface{})
    config["filename"] = "/Users/zhaofan/go_project/src/go_dev/13/log/logcollect.log"
    config["level"] = logs.LevelTrace
    configStr,err := json.Marshal(config)
    if err != nil{
        fmt.Println("marshal failed,err：",err)
        return
    }
    logs.SetLogger(logs.AdapterFile,string(configStr))
    logs.Debug("this is a debug,my name is %s","stu01")
    logs.Info("this is a info,my name is %s","stu02")
    logs.Trace("this is trace my name is %s","stu03")
    logs.Warn("this is a warn my name is %s","stu04")
}
```