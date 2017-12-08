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