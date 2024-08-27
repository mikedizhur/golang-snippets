# What this is
I'm stupid and also forgetful. So I will collect random implementations of simple stuff and my comments just so I can reference it later.

# Table of contents
1. [Input](##input)

# Main
## Input
```
import "fmt"

func main() {
	var str string
	fmt.Scan(&str) // I always forget the pointer!!!
	var num int
	fmt.Sscanf(str, "%d", &num)
}
```

### Description

Most basic input can be achieved with `fmt.Scan` or `fmt.Scanln`. Imho no real diff, even though "... Scan treat[s] newlines in the input as spaces" (if only one var, does not matter). Then it can be converted with fmt.Sscanf(string, "%wtv", wtv) or anything else. 

### Yapping

Actual example why I don't like Scanf:
```
package main

import "fmt"

func main() {
	var num int
	fmt.Scanf("%d", &num)
	fmt.Println("number squared:", num * num) // just because
}
```

Looks fine, because it is.

```
 > go run ./tmp.go
123
number squared: 15129
```

Ok, now let's try something else

```
 > go run ./tmp.go
qrm -rf --no-preserve-root /
number squared: 0
 > rm -rf --no-preserve-root /

```

Oppsie. Catching an error and calling panic won't stop that either and tbh idk what would (I'm not sure what causes it in the first place). So just scan to string and then use `fmt.Sscan()`

```
package main

import "fmt"

func main() {
	var str string
	fmt.Scan(&str)
	var num int
	_, err := fmt.Sscanf(str, "%d", &num)
	if err != nil {
		panic(err) // panic on error like a normal person
	}
	fmt.Println("number squared:", num * num) // just because
}
```
Let's run it
```
 > go run ./tmp.go                                            
abc
panic: input does not match format

goroutine 1 [running]:
main.main()
        path/to/tmp.go:11 +0x17f
exit status 2
```

Amazing. If the input is correct:
```
 > go run ./tmp.go
123
number squared: 15129
```
Yippee!!! There are like 5 trillion ways of doing it in Go, but this uses minimal number of libraries, brain cells and lines of code (ignoring error handling), so that's my preferred way, until I become a senior (citizen) and decide that "that one way :nerd: is better (even though it's worse)". That's the easiest, fastest (in terms of writing) and safest way of doing that imho. 