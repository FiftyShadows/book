123 反转之后是 321。
900 反转之后是 9。

python
```python
class Solution:
    """
    @param number: A 3-digit number.
    @return: Reversed number.
    """
    def reverseInteger(self, number):
        # write your code here
        before_str= str(number)
        after_str=before_str[::-1]
    
        return int(after_str)
```

golang字符串比较麻烦,取余数操作,比如785
```go
func reverseInteger (number int) int {
    var num int
	for number != 0 {
		num = num*10 + number%10
		number  = number / 10
	}

	return num
}
```