```go
package modle

type student struct {
	Name string
	Age  int
}

func NewStudent(name string, age int) *student {
	return &student{
		Name: name,
		Age:  age}
}

packge main
S := new (student)
S := model.NewStudent("tony", 20)

```