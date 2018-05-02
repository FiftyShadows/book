####json
```go
import "encoding/json"
func ToMap(in2 interface{}) map[string]interface{} {
	m := make(map[string]interface{})
	inrec, _ := json.Marshal(in2)
	json.Unmarshal(inrec, &m)
	return m
}
```
####reflect
```go
func Struct2Map(obj interface{}) map[string]interface{} {
	t := reflect.TypeOf(obj)
	v := reflect.ValueOf(obj)
	var data = make(map[string]interface{})
	for i := 0; i < t.NumField(); i++ {
		data[t.Field(i).Name] = v.Field(i).Interface()
	}
	return data
}

```
####
github.com/fatih/structs
```go
m := structs.Map(structs)
```