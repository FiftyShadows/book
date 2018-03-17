```go
Package model
type student struct {       Name stirng ,  
 Age int}func NewStudent(name string, age int) *student {return &student{       Name:name,       Age:age,}}Package mainS := new (student)S := model.NewStudent(“tony”, 20)
```