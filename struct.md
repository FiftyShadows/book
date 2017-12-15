####golang中的类和 python 的类挺相似的
#####基础的例子
```go
package main

import "fmt"

// 声明一个新的类型
type person struct {
	name string
	age int
}

// 比较两个人的年龄，返回年龄大的那个人，并且返回年龄差
// struct也是传值的
func Older(p1, p2 person) (person, int) {
	if p1.age>p2.age {  // 比较p1和p2这两个人的年龄
		return p1, p1.age-p2.age
	}
	return p2, p2.age-p1.age
}

func main() {
	//第一种赋值方法,先初始化
	var tom person
	// 赋值初始化
	tom.name, tom.age = "Tom", 18
	
	// 第二种赋值方法,两个字段都写清楚的初始化
	bob := person{age:25, name:"Bob"}

	// 第三种赋值方法,按照struct定义顺序初始化值
	paul := person{"Paul", 43}

	tb_Older, tb_diff := Older(tom, bob)
	tp_Older, tp_diff := Older(tom, paul)
	bp_Older, bp_diff := Older(bob, paul)

	fmt.Printf("Of %s and %s, %s is older by %d years\n",
		tom.name, bob.name, tb_Older.name, tb_diff)

	fmt.Printf("Of %s and %s, %s is older by %d years\n",
		tom.name, paul.name, tp_Older.name, tp_diff)

	fmt.Printf("Of %s and %s, %s is older by %d years\n",
		bob.name, paul.name, bp_Older.name, bp_diff)
}
```
<br>
#####初始化技巧
```go
type person struct {
	name string
	age int
}


func main() {

	bob := person{age:25, name:"Bob"}
	fmt.Println(bob) //25
	A(bob)  //16
	fmt.Println(bob) // 25
	//可以看到bob的age并没有真正改变
}

func A(per person){
	per.age = 16
	fmt.Println(per)
}
//----------------------------------------------------------------
//初始化的时候取出地址	
func main() {

	bob := &person{age:25, name:"Bob"}
	fmt.Println(bob) 
	A(bob)  
	fmt.Println(bob) 
}

func A(per *person){
	per.age = 16
	fmt.Println(per)
}
//&{Bob 25}
//&{Bob 16}
//&{Bob 16}

```
<br>
 
#####匿名字段,跟继承蛮像的
```go
package main

import "fmt"

type Skills []string

type Human struct {
	name string
	age int
}
type Student struct{
	Human
	weight int
	age int  //碰到同样的字段名怎么办呢
	skills Skills //自定义的切片类型
}

func main() {
	shen := Student{Human{"jiacheng",22},65,23,[]string{"play games"}}
	fmt.Println(shen.Human.age,shen.age)
	//调用方式
	// 22   23
	fmt.Println(shen.skills)
	//[play games]
}

```
<br>
####method  类似python 的类方法,这样理解就很容易了
```go
type Rectangle struct {
	width, height float64
} //一个类

func (r Rectangle) area() float64 {
	return r.width * r.height
}  //类方法

func main() {
	a :=Rectangle{12,2} //实例化一个类

	fmt.Println("Area of r1 is: ", a.area())//调用类方法
}
```
<br>
####再看下用方法怎么改变属性,这个跟切片和字典不一样,传递的是值的副本,要带上指针
```go
//可以看到,需要带上指针才能改变值
type Rectangle struct {
	width, height int
}


func (r Rectangle) no_pointer() {
	r.width = 10
}

func (r *Rectangle) pointer() {
	r.width = 5
}

func main() {
	a :=Rectangle{12,2}
	a.no_pointer()
	fmt.Println("no_pointer", a)
	a.pointer()
	fmt.Println("poionter", a)
	//no_pointer {12 2}
	//poionter {5 2}
}
```
<br>
####再来看下怎么继承后的重写
```go
type Human struct {
	name string
	age int
	phone string
}

type Student struct {
	Human //匿名字段
	school string
}

type Employee struct {
	Human //匿名字段
	company string
}

//Human定义method
func (h *Human) SayHi() {
	fmt.Printf("Hi, I am %s you can call me on %s\n", h.name, h.phone)
}

//Employee的method重写Human的method
func (e *Employee) SayHi() {
	fmt.Printf("Hi, I am %s, I work at %s. Call me on %s\n", e.name,
		e.company, e.phone) //Yes you can split into 2 lines here.
}

func main() {
	mark := Student{Human{"Mark", 25, "222-222-YYYY"}, "MIT"}
	sam := Employee{Human{"Sam", 45, "111-888-XXXX"}, "Golang Inc"}

	mark.SayHi()
	sam.SayHi()
}
```