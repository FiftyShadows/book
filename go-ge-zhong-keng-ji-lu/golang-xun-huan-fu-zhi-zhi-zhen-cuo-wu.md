####学校教的 c 全部还回去了


m[stu.Name]=&stu实际上一致指向同一个指针， 最终该指针的值为遍历的最后一个struct的值拷贝。 就像想修改切片元素的属性：

```go
func pase_student() {
    m := make(map[string]*student)
    stus := []student{
        {Name: "zhou", Age: 24},
        {Name: "li", Age: 23},
        {Name: "wang", Age: 22},
    }
    // 错误写法
    for _, stu := range stus {
        m[stu.Name] = &stu
    }

    for k,v:=range m{
        println(k,"=>",v.Name)
    }

    // 正确
    for i:=0;i<len(stus);i++  {
        m[stus[i].Name] = &stus[i]
    }
    for k,v:=range m{
        println(k,"=>",v.Name)
    }
}

```