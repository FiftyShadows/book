```go

package main

import "fmt"

type Student struct {
	name string
	age  int
}

func (stu *Student) SetName(name string) *Student {
	stu.name = name
	return stu
}

func (stu *Student) SetAge(age int) *Student {
	stu.age = age
	return stu
}

func (stu *Student) Print() {
	fmt.Printf("age:%d,name:%s", stu.age, stu.name)
}

func main(){
	stu := &Student{}
	stu.SetAge(12).SetName("shen").Print()
}
//age:12,name:shen
```