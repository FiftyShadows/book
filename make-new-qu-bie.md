golang 中有两个内存分配机制 ：new和make，二者有明显区别．

new：用来初始化一个对象，并且返回该对象的首地址．其自身是一个指针．可用于初始化任何类型

make:返回一个初始化的实例，返回的是一个实例，而不是指针，其只能用来初始化：slice,map和channel三种类型
```go
package main

import (
	"fmt"
)

func main() {
	a := new([]int)
	fmt.Println(a) //输出&[]，ａ本身是一个地址  
	b := make([]int, 1)
	fmt.Println(b)//输出[0]，ｂ本身是一个slice对象，其内容默认为０  
}  
```