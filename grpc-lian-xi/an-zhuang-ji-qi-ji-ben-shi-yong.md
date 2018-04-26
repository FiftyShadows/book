```
brew tap grpc/grpc
brew install --with-plugins grpc
//编译器插件
go get -u github.com/golang/protobuf/{proto,protoc-gen-go}
go get -u google.golang.org/grpc //这个要自己去 github 上搜再装,参考我go 基础专栏里的文章(手动安装第三方库)
```
你也可以这样
```shell
cd /usr/local/go/path/src   

mkdir google.golang.org

cd google.golang.org/

git clone https://github.com/grpc/grpc-go

mv grpc-go/ grpc/
```

编译器姿势
`protoc --proto_path=IMPORT_PATH --cpp_out=DST_DIR --java_out=DST_DIR --python_out=DST_DIR --go_out=DST_DIR --ruby_out=DST_DIR --javanano_out=DST_DIR --objc_out=DST_DIR --csharp_out=DST_DIR path/to/file.proto`
-I 参数：指定import路径，可以指定多个-I参数，编译时按顺序查找，不指定时默认查找当前目录

- --go_out ：golang编译支持，支持以下参数

    - plugins=plugin1+plugin2 - 指定插件，目前只支持grpc，即：plugins=grpc

    - M 参数 - 指定导入的.proto文件路径编译后对应的golang包名(不指定本参数默认就是.proto文件中import语句的路径)

    - import_prefix=xxx - 为所有import路径添加前缀，主要用于编译子目录内的多个proto文件，这个参数按理说很有用，尤其适用替代一些情况时的M参数，但是实际使用时有个蛋疼的问题导致并不能达到我们预想的效果，自己尝试看看吧

    - import_path=foo/bar - 用于指定未声明package或go_package的文件的包名，最右面的斜线前的字符会被忽略

    - 末尾 :编译文件路径 .proto文件路径(支持通配符)
    
示例
`protoc -I . --go_out=plugins=grpc,Mfoo/bar.proto=bar,import_prefix=foo/,import_path=foo/bar:. ./*.proto`




```
example/
|—— hello/
    |—— client/
        |—— main.go   // 客户端
    |—— server/
        |—— main.go   // 服务端
|—— proto/
    |—— hello.proto   // proto描述文件
    |—— hello.pb.go   // proto编译后文件
```

hello.proto文件

```proto
syntax = "proto3"; // 指定proto版本

package proto;     // 指定包名

// 定义Hello服务
service Hello {
    // 定义SayHello方法
    rpc SayHello(HelloRequest) returns (HelloReply) {}
}

// HelloRequest 请求结构
message HelloRequest {
    string name = 1;
}

// HelloReply 响应结构
message HelloReply {
    string message = 1;
}
```
hello.proto文件中定义了一个Hello Service，该服务包含一个SayHello方法，同时声明了HelloRequest和HelloReply消息结构用于请求和响应。客户端使用HelloRequest参数调用SayHello方法请求服务端，服务端响应HelloReply消息。


编译生成.pb.go文件：

`protoc -I . --go_out=plugins=grpc:. ./hello.proto`

生成的.pb.go文件，按照.proto文件中的说明，包含服务端接口HelloServer描述，客户端接口及实现HelloClient，及HelloRequest、HelloResponse结构体，不要手动编辑该文件。


server/main.go

```go

func (h helloService) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
    resp := new(pb.HelloReply)
    resp.Message = "Hello " + in.Name + "."

    return resp, nil
}

func main() {
    listen, err := net.Listen("tcp", Address)
    if err != nil {
        grpclog.Fatalf("failed to listen: %v", err)
    }

    // 实例化grpc Server
    s := grpc.NewServer()

    // 注册HelloService
    pb.RegisterHelloServer(s, HelloService)

    grpclog.Println("Listen on " + Address)

    s.Serve(listen)
}
```

client/main.go

```go

package main

import (
    pb "go-grpc-practice/example/proto" // 引入proto包

    "golang.org/x/net/context"
    "google.golang.org/grpc"
    "google.golang.org/grpc/grpclog"
)

const (
    // Address gRPC服务地址
    Address = "127.0.0.1:50052"
)

func main() {
    // 连接
    conn, err := grpc.Dial(Address, grpc.WithInsecure())

    if err != nil {
        grpclog.Fatalln(err)
    }

    defer conn.Close()

    // 初始化客户端
    c := pb.NewHelloClient(conn)

    // 调用方法
    reqBody := new(pb.HelloRequest)
    reqBody.Name = "gRPC"
    r, err := c.SayHello(context.Background(), reqBody)
    if err != nil {
        grpclog.Fatalln(err)
    }

    grpclog.Println(r.Message)
}
```