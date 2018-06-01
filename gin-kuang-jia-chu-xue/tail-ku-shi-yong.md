根据日志文件处理

`go get github.com/hpcloud/tail`

```go
package main

import (
	"github.com/hpcloud/tail"
	"fmt"
	"time"
)

func main() {
	filename := "/Users/admin/Desktop/11111.log"
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