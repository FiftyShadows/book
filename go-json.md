JSON输出的时候必须注意，只有导出的字段(首字母是大写)才会被输出，如果修改字段名，那么就会发现什么都不会输出，所以必须通过struct tag定义来实现。

- 字段的tag是"-"，那么这个字段不会输出到JSON
- tag中带有自定义名称，那么这个自定义名称会出现在JSON的字段名中
- tag中如果带有"omitempty"选项，那么如果该字段值为空，就不会输出到JSON串中
- 如果字段类型是bool, string, int, int64等，而tag中带有",string"选项，那么这个字段在输出到JSON的时候会把该字段对应的值转换成JSON字符串

```go
type IT struct {
    //Company不会导出到JSON中
    Company string `json:"-"`

    // Subjects 的值会进行二次JSON编码
    Subjects []string `json:"subjects"`

    //转换为字符串，再输出
    IsOk bool `json:"string"`

    // 如果 Price 为空，则不输出到JSON串中
    Price float64 `json:"price, omitempty"`
func main() {
    t1 := IT{Company: "itcast", Subjects: []string{"Go", "C++", "Python", "Test"}, IsOk: true}

    b, err := json.Marshal(t1)
    //json.MarshalIndent(t1, "", "    ")
    if err != nil {
        fmt.Println("json err:", err)
    }
    fmt.Println(string(b))
    //输出结果：{"subjects":["Go","C++","Python","Test"],"IsOk":"true","price":0}
}
```

####通过map生成JSON

```go
  // 创建一个保存键值对的映射
    t1 := make(map[string]interface{})
    t1["company"] = "itcast"
    t1["subjects "] = []string{"Go", "C++", "Python", "Test"}
    t1["isok"] = true
    t1["price"] = 666.666

    b, err := json.Marshal(t1)
    //json.MarshalIndent(t1, "", "    ")
    if err != nil {
        fmt.Println("json err:", err)
    }
    fmt.Println(string(b))
    //输出结果：{"company":"itcast","isok":true,"price":666.666,"subjects ":["Go","C++","Python","Test"]}

```

####json 解码
```go
type IT struct {
    Company  string   `json:"company"`
    Subjects []string `json:"subjects"`
    IsOk     bool     `json:"isok"`
    Price    float64  `json:"price"`
}

func main() {
    b := []byte(`{
    "company": "itcast",
    "subjects": [
        "Go",
        "C++",
        "Python",
        "Test"
    ],
    "isok": true,
    "price": 666.666
}`)

    var t IT
    err := json.Unmarshal(b, &t)
    if err != nil {
        fmt.Println("json err:", err)
    }
    fmt.Println(t)
    //运行结果：{itcast [Go C++ Python Test] true 666.666}

    //只想要Subjects字段
    type IT2 struct {
        Subjects []string `json:"subjects"`
    }

    var t2 IT2
    err = json.Unmarshal(b, &t2)
    if err != nil {
        fmt.Println("json err:", err)
    }
    fmt.Println(t2)
    //运行结果：{[Go C++ Python Test]}
}
```
