```go
func GetSum(n int) int {
	if n == 0 {
		return 0
	}
	return n + GetSum(n - 1)
} 
```

```go
func GetLength(arr []int, n int) int {  // n = 0
	if n == -1 {
		return 0
	}
	return 1 + GetLength(arr, n - 1)
} 
```

```go
func GetTail(arr []int, n int) int { // n = 0
	if n == len(arr) - 1 {
		return arr[n] 
	}
	
	return GetTail(arr, n + 1)
} 
```

```go
func ArraysAreEqual(arr1, arr2 []int, n1, n2 int) bool {
	if arr1[n1] != arr[n2] { return false }
	if len(arr1)-1 > n1 && len(arr2)-1 <= n2 { return false }
	if len(arr1)-1 <= n1 && len(arr2)-1 > n2 { return false }

	return ArraysAreEqual(arr1, arr2, n1+1, n2+1)
} 
```

```go
func GetProduct(arr []int, n int) bool {
	if n == -1 {
		return 1
	}
	return arr[n] * GetFactorial(arr, n - 1)
} 

```

```go
func GetFactorial(n int) bool {
	if n < 2 {
		return 1
	}
	return n * GetFactorial(n - 1)
} 
```

```go
func GetFibonacci(n int) int {
	if n <= 1 {
		return 0
	}
	return GetFibonacci(n - 1) + GetFibonacci(n - 2)
} 
```

```go
func GetPower(base, power int) int {
	if power == 0 {
		return 1
	}
	if power & 1 {
		return base * GetPower(base, power - 1)
	}

	return GetPower(base * base, power / 2)
}
```