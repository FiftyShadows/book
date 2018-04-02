####多重继承如果出现了2个匿名字段里有一样的字段名,会出错
```go
type Car struct {
	Name string
	Age  int
}


type Car2 struct {
	Name string
}

type Train struct {
	Car
	Car2
	createTime time.Time
	int
}

func main(){
	var train Train
	train.int = 300
		//这里无法识别是哪个name
		//train.Name = "test"
		train.Car2.Name = "dfdd"
	fmt.Println(train)
	
}

```