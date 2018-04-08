```go

package main

import "fmt"

type Humaner interface {
	SayHi()
}

type Student struct {
	//学生
	name  string
	score float64
}

//Student实现SayHi()方法
func (s *Student) SayHi() {
	fmt.Printf("Student[%s, %f] say hi!!\n", s.name, s.score)
}

type Teacher struct {
	//老师
	name  string
	group string
}

//Teacher实现SayHi()方法
func (t *Teacher) SayHi() {
	fmt.Printf("Teacher[%s, %s] say hi!!\n", t.name, t.group)
}

type MyStr string

//MyStr实现SayHi()方法
func (str MyStr) SayHi() {
	fmt.Printf("MyStr[%s] say hi!!\n", str)
}

//普通函数，参数为Humaner类型的变量i
func WhoSayHi(i Humaner) {
	i.SayHi()
}

func main() {
	s := &Student{"mike", 88.88}
	t := &Teacher{"yoyo", "Go语言"}
	var tmp MyStr = "测试"

	s.SayHi()   //Student[mike, 88.880000] say hi!!
	t.SayHi()   //Teacher[yoyo, Go语言] say hi!!
	tmp.SayHi() //MyStr[测试] say hi!!

	//多态，调用同一接口，不同表现
	WhoSayHi(s)   //Student[mike, 88.880000] say hi!!
	WhoSayHi(t)   //Teacher[yoyo, Go语言] say hi!!
	WhoSayHi(tmp) //MyStr[测试] say hi!!

	x := make([]Humaner, 3)
	//这三个都是不同类型的元素，但是他们实现了interface同一个接口
	x[0], x[1], x[2] = s, t, tmp
	for _, value := range x {
		value.SayHi()
	}
	/*
Student[mike, 88.880000] say hi!!
Teacher[yoyo, Go语言] say hi!!
MyStr[测试] say hi!!
	*/
}
```