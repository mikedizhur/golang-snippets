# What this is
I'm stupid and also forgetful. So I will collect random implementations of simple stuff and my comments just so I can reference it later.

# Table of contents<a name="toc"></a>
1. [Input](#input)
2. [Random](#random)

# Main
## Input<a name="input"></a>
```
import (
	"bufio"
	"os"
)

func main() {
	reader := bufio.NewReader(os.Stdin)
	line, err := reader.ReadString('\n')
	if err != nil {
		panic(err)
	}
}
```

### Description

Most basic input can be achieved with `fmt.Scan`, but bufio is the only way (that I know of) to get the entire line into one variable. Then you can convert with fmt.Sscanf(string, "%wtv", wtv) or anything else.

### Yapping

Actual example why I don't like fmt.Scan or fmt.Scanf:
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

Oppsie. Catching an error and calling panic won't stop that either and tbh idk what would (I'm not sure what causes it in the first place). So just read to string and then use `fmt.Sscan()`

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

Update: idk changed my mind since then but wtv

## Random<a name="random"></a>
```
package main

import (
	"fmt"
	"math/rand/v2"
)

func main() {
	fmt.Println(rand.IntN(100))

	var seed1, seed2 uint64
	seeder := rand.NewPCG(seed1, seed2)
	random := rand.New(seeder)

	fmt.Println(random.IntN(100))
}
```

### Description
Works completely fine by just calling functions right out of the library, but we do not know the seed.

If we want to set the seed, we create a "PCG" with 2 uint64 values. Using that, we'll create the thing we'll be actually using to generate values.

### Yapping
PCG stands for "permuted congruential generator" apparently. After having glanced at www.pcg-random.org, I have gained no further understanding, so I'll leave mathematicians to themselves.

I don't really get why we need 2 seed values? There are no 128 bit integers in Go so whatever.

I do like how easy it actually is though, compared to C especially. C++ isn't that much harder but a whole lot less readable. In python I don't really remember how to set seed, but it might be like one less line, so overall Go is pretty chill in this area.
