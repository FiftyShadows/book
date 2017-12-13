####数组和切片看起来跟python 的 list 很像,其实蛮有区别的,听说一般都用切片
```go
//数组的定义
var my_list1 = [4]int{11,22} //这个是数组,中括号里必须有...表示不定长度,或者指定一个长度
var my_list2 = [...]int{3:6} //赋值,第4个数字是6
var my_list3 = [2][3]int{
	{1,2,3},
	{4,5,6},
}//定义了一个二维数组
//两种切片的定义方式,推荐使用 make 的方法
var my_slice = make([]int,4,5)//切片的长度是4,容量是5
var my_slice2 = []int{1,2,3}
```
<br>
####要注意的是,[:]复制的是浅拷贝,也就是指向的还是原来的内存地址,所以修改原来切片的时候,复制过来的也会变,这个很重要,要复制一个不相干的,如下代码
```go
func main() {
	a := []int{1,2,3}
	b := a[:]
	a[0] = 10
	fmt.Println(b)
//你会发现 b 也变成了[10,2,3]
}
```
####怎么办呢
```go
a := []int{1,2,3}
b:=[]int{3,4,5,6}

c:=append([]int{},a...) //拷贝了一个 a
d := make([]int,len(b))
copy(d,b)//拷贝了一个 b
```
#####append用法示例
```go
func main() {
	a := []int{1,2,3}
	b:=[]int{3,4,5,6}
	c := append(a,b...)
	d:= append(a,5)
	fmt.Println(c) //[1 2 3 3 4 5 6]
	fmt.Println(d) //[1 2 3 5]
}

```
删除位于索引 i 的元素：`a = append(a[:i], a[i+1:]...)`

切除切片 a 中从索引 i 至 j 位置的元素：`a = append(a[:i], a[j:]...)`
<br>
#####copy的用法
```go
func main() {
	a:=[]int{1,2,3,4}
	b:=[]int{8,53,867,2435,54,64,5675,7}
	copy(a,b)
	fmt.Print(a) //[8 53 867 2435]
}

```
