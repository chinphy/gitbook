### m的n次方


```go

    func main() {
        res := 1
	m := 5
	n := 3
	for n != 0 {
	    if n&1 == 1 {
		res *= m
	    }
	    m *= m
	    n = n >> 1
	}
	fmt.Println(res)
    }
```