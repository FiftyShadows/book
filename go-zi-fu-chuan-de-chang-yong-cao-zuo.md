` fmt.Println(strings.Contains("seafood", "foo"))

#####加入
```go
s := []string{"foo", "bar", "baz"}
```
#####查找位置
```go
func Index(s, sep string) int
```
#####替换
```go
func Replace(s, old, new string, n int) string
```
#####分割
```go
func Split(s, sep string) []string
```
#####去头尾
```go
func Trim(s string, cutset string) string
```
#####去空格

```go
func Fields(s string) []string
```

#####字符串添加
```go
Append 系列函数将整数等转换为字符串后，添加到现有的字节数组中。
```

#####Format 系列函数把其他类型的转换为字符串。
```go

 a := strconv.FormatBool(false)
```