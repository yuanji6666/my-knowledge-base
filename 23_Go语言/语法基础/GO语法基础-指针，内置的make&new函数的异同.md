# GO中的指针

go中的指针就是**数据的地址**，与C语言不同的是它不能进行偏移和运算（体现了GO简洁的设计哲学）

# new & make

## new（）函数

```
new(Type) *Type
```

new 会根据Type**开辟内存**空间，并赋予其为**对应Type**的**零值**，然后将这块数据的**指针**返回

```
func main() {
	a := new([3]*int)
	fmt.Printf("%T",a)
	fmt.Println()
	fmt.Println(*a)
}

Output:

*[3]*int
[<nil> <nil> <nil>]
```


## make（）函数

- make函数只能分配slice，map，chan
- 返回的是类型本身（**他们本身就是引用类型，所以也没必要返回指针了**）

## 异同点

- 同 ：都用来分配内存
- 异：返回值，适用类型，初始化