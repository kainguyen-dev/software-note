# Golang Tour

## 1. Public and private

Go lang do not have public and private, a name is exported if it have first letter capitalized. 

## 2. Function

```go
import "fmt"

func add(x int, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}
```

Function in go can return any number of result.

```go
func swap(x, y string) (string, string) {
	return y, x
}
```
The swap function returns two strings. 

Name return function
```go
func name_function() (x, y int) {
    x = 3
    y = 4
    return
} 
```

## 3. Variable
```go
var x int = 1
```
Short variable declarations
```go
k := 3
```

## 4. For Loop

```go
sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
```

## 5. If

```go
if x < 0 {
		return sqrt(-x) + "i"
	}
```

## 6. Defer
Defer function do not execute until the surround function return.

## 7. Pointer

```go
func main() {
	i, j := 42, 2701

	p := &i             // Pointer to i
	fmt.Println(*p)     // Print value of *p
	*p = 21     
	fmt.Println(i)  

	p = &j         
	*p = *p / 37   
	fmt.Println(j) 
}
```

## 8. Struct
```go
type Vertex struct {
    X int
    Y int
}

func main() {
	v := Vertex{1, 2}
	v.X = 4
	fmt.Println(v.X)
}
```

## 9. Array
Array in go have fix size
```go
    primes := [6]int{2, 3, 5, 7, 11, 13}
```

## 10. Slice
Slice in go have flexible type, we declare slice by don't specify size of array.
```go
    q := []{1, 2, 3}
    q = append(q, 10)
```

## 11. Map
```go
    mapV = make(map[string]int)
    mapV["test"] = "result"
```

## 12. Function as value
Function in go is value they can be passed around.

## TO DO: make an small app that used all of these feature