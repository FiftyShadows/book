Java当中有instanceof这样的关键字判断类型 Go当中自然也有相应的方法来判断类型 

即Comma-ok断言

写法为value, ok := em.(T)   如果确保em 是同类型的时候可以直接使用value:=em.(T)一般用于switch语句中下面将会讲解到

em代表要判断的变量 

T代表被判断的类型

value代表返回的值

ok代表是否为改类型



###em必须为initerface类型才可以进行类型断言

这样是错误的,报错`invalid type assertion: s.(string) (non-interface type string on left)`
```go
s := "BrainWu"
	if v, ok := s.(string); ok {
		fmt.Println(v)
	}
```
在这里只要是在声明时或函数传进来的参数不是interface类型那么做类型断言都是回报 non-interface的错误的



所以我们只能通过将s作为一个interface{}的方法来进行类型断言 如下代码所示
```go
s := "BrainWu"
	if v, ok := interface{}(s).(string); ok {
		fmt.Println(v)
	}
```
将s显示的转换为interface{}接口类型则可以进行类型断言了



 另外讲解 switch与类型断言的结合使用还是比较方便的 
 ```go
 package main

import (
	"fmt"
)

type Element interface {}

func main() {
	var e Element = 100
	switch value := e.(type) {
	case int:
		fmt.Println("int", value)
	case string:
		fmt.Println("string", value)
	default:
		fmt.Println("unknown", value)
	}
}
``` 