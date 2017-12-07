####基本的if else
```go
func main() {
	x := 10
	if x < 5{
		fmt.Println("小于5")
	} else {
		fmt.Println("大于5")
	}
}
```
<br>
####一个目前目前不知道场景,感觉跟python的continue很像的goto
```go
func main() {
   /* 定义局部变量 */
   var a int = 10

   /* 循环 */
   LOOP: for a < 15 {
      if a == 12 {
         /* 跳过迭代 */
         a = a + 1
         goto LOOP
      }
      fmt.Printf("a的值为 : %d\n", a)
      a++     
   }  
}
//a的值为 : 10
//a的值为 : 11
//a的值为 : 13
//a的值为 : 14
```