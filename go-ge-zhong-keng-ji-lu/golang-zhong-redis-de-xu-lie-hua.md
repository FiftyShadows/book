####存
```go
var product Product
product_json, _ := json.Marshal(product)
_, e := conn.Do("SET", key_name, product_json, "EX", "100000")
```
####取
```go
var info_map map[string]interface{}
valueGet, err := redis.Bytes(conn.Do("GET", id))
err = json.Unmarshal(valueGet, &info_map)
return info_map
```