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

### 交换两个数


```go

func main() {
    x := 1
    y := 2

    x = x ^ y   // （1）
    y = x ^ y   // （2） x ^ y ^ y
    x = x ^ y   // （3） x ^ y ^ x ^ y ^ y

    fmt.Println("x->", x)
    fmt.Println("y->", y)
}
```