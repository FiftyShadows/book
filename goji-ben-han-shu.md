###go的函数不支持嵌套 ,重载,默认参数
####goto 函数
```go
package main
import "fmt"
func main() {
   /* local variable definition */
   var a int = 10
   /* do loop execution */
   LOOP: for a < 20 {
      if a == 15 {
         /* skip the iteration */
         a = a + 1
         goto LOOP
      }
      fmt.Printf("value of a: %d\n", a)
      a++     
   }  
}
```
<br>

####跟 python 不一样,多条件选择多了个switch, 跟 js 的用法一样`fallthrough`可以强制执行 case 后面的代码
```go
i := 10
switch i {
case 1:
	fmt.Println("i is equal to 1")
case 2, 3, 4:
	fmt.Println("i is equal to 2, 3 or 4")
case 10:
	fmt.Println("i is equal to 10")
default:
	fmt.Println("All I know is that i is an integer")
}
```

<br>
####在go中,要把函数作为一个参数传递到一个函数中,需要先指定.
```go
package main

import "fmt"

type testInt func(int) bool // 声明了一个函数类型

func isOdd(integer int) bool {
	if integer%2 == 0 {
		return false
	}
	return true
}

func isEven(integer int) bool {
	if integer%2 == 0 {
		return true
	}
	return false
}

func filter(slice []int, f testInt) []int {
	var result []int
	for _, value := range slice {
		if f(value) {
			result = append(result, value)
		}
	}
	return result
}

func main(){
	slice := []int {1, 2, 3, 4, 5, 7}
	fmt.Println("slice = ", slice)
	odd := filter(slice, isOdd)    // 函数当做值来传递了
	fmt.Println("Odd elements of slice are: ", odd)
	even := filter(slice, isEven)  // 函数当做值来传递了
	fmt.Println("Even elements of slice are: ", even)
}
```
<br>
####go的defer函数,后定义的先调用,可以再return之后改变结果,如下 可以看到,结果是acb
```go
fmt.Println("a")
defer fmt.Println("b")
defer fmt.Println("c")
//a
//c
//b
```
<br>

####go的匿名函数
```go
package main

import "fmt"

func main() {
	i := 0
	str := "mike"

	//方式1
	f1 := func() { //匿名函数，无参无返回值
		//引用到函数外的变量
		fmt.Printf("方式1：i = %d, str = %s\n", i, str)
	}

	f1() //函数调用

	//方式1的另一种方式
	type FuncType func() //声明函数类型, 无参无返回值
	var f2 FuncType = f1
	f2() //函数调用

	//方式2
	var f3 FuncType = func() {
		fmt.Printf("方式2：i = %d, str = %s\n", i, str)
	}
	f3() //函数调用

	//方式3
	func() { //匿名函数，无参无返回值
		fmt.Printf("方式3：i = %d, str = %s\n", i, str)
	}() //别忘了后面的(), ()的作用是，此处直接调用此匿名函数

	//方式4, 匿名函数，有参有返回值
	v := func(a, b int) (result int) {
		result = a + b
		return
	}(1, 1) //别忘了后面的(1, 1), (1, 1)的作用是，此处直接调用此匿名函数， 并传参
	fmt.Println("v = ", v)

}

```
<br>
####go的闭包函数
```go
func main() {
	f := closure(10)
	fmt.Println(f(1)) //11
	fmt.Println(f(2)) //12
}

func closure(x int) func(int) int {
	return func(y int) int {
		return x + y
	}
}
```