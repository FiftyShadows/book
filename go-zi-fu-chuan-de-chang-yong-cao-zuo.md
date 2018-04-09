` fmt.Println(strings.Contains("seafood", "foo"))`true,字符串中是否包含，返回bool值

#####加入
```go
s := []string{"foo", "bar", "baz"}    fmt.Println(strings.Join(s, ", "))    //运行结果:foo, bar, baz
```
#####查找位置
```go
func Index(s, sep string) int功能：在字符串s中查找sep所在的位置，返回位置值，找不到返回-1示例代码：    fmt.Println(strings.Index("chicken", "ken"))    fmt.Println(strings.Index("chicken", "dmr"))    //运行结果:    //    4    //    -1
```
#####替换
```go
func Replace(s, old, new string, n int) string功能：在s字符串中，把old字符串替换为new字符串，n表示替换的次数，小于0表示全部替换示例代码：    fmt.Println(strings.Replace("oink oink oink", "k", "ky", 2))    fmt.Println(strings.Replace("oink oink oink", "oink", "moo", -1))    //运行结果:    //oinky oinky oink    //moo moo moo
```
#####分割
```go
func Split(s, sep string) []string功能：把s字符串按照sep分割，返回slice示例代码：    fmt.Printf("%q\n", strings.Split("a,b,c", ","))    fmt.Printf("%q\n", strings.Split("a man a plan a canal panama", "a "))    fmt.Printf("%q\n", strings.Split(" xyz ", ""))    fmt.Printf("%q\n", strings.Split("", "Bernardo O'Higgins"))    //运行结果:    //["a" "b" "c"]    //["" "man " "plan " "canal panama"]    //[" " "x" "y" "z" " "]    //[""]
```
#####去头尾
```go
func Trim(s string, cutset string) string功能：在s字符串的头部和尾部去除cutset指定的字符串示例代码：    fmt.Printf("[%q]", strings.Trim(" !!! Achtung !!! ", "! "))    //运行结果:["Achtung"]
```
#####去空格

```go
func Fields(s string) []string功能：去除s字符串的空格符，并且按照空格分割返回slice示例代码：    fmt.Printf("Fields are: %q", strings.Fields("  foo bar  baz   "))    //运行结果:Fields are: ["foo" "bar" "baz"]
```

#####字符串添加
```go
Append 系列函数将整数等转换为字符串后，添加到现有的字节数组中。示例代码：    str := make([]byte, 0, 100)    str = strconv.AppendInt(str, 4567, 10) //以10进制方式追加    str = strconv.AppendBool(str, false)    str = strconv.AppendQuote(str, "abcdefg")    str = strconv.AppendQuoteRune(str, '单')    fmt.Println(string(str)) //4567false"abcdefg"'单'
```

#####Format 系列函数把其他类型的转换为字符串。
```go
示例代码：   
 a := strconv.FormatBool(false)    b := strconv.FormatInt(1234, 10)    c := strconv.FormatUint(12345, 10)    d := strconv.Itoa(1023)    fmt.Println(a, b, c, d) //false 1234 12345 1023
```