**Рекурсия** — это метод решения задач, при котором функция вызывает сама себя для решения подзадачи меньшего размера. Рекурсивная функция должна обязательно содержать два ключевых элемента:
- base case - тогда, когда выходит из рекурсии
- recursice case - часть функции, которая вызывает саму себя с измененными аргументами, для приблежения ее к базовому случаю. Каждый рекурсивный вызов должен уменьшать размер задачи. 
- 
Пример расчета факториала
```go
func factorial(n) int {
	if n <= 1 {
		return 1
	}
	
	return n * factorial(n-1)
}
```

Типы рекурсий:
- direct recursion - При прямой рекурсии функция вызывает саму себя непосредственно
```go
func countdown(n) {
	if n <= 0 {
		return
	}
	
	fmt.Println(n)
	countdown(n - 1)
}
```
- indirect recursion - При косвенной рекурсии функция A вызывает функцию B, которая в свою очередь вызывает функцию A.
```go
func isEven(n int) bool {
	if n == 0 {
		return true
	}

	return isOdd(n - 1)
} 

func isOdd(n int) bool {
	if n == 0 {
		return false
	}

	return isEven(n-1)
}
```

- Tail Recursion - это особый случай, когда рекурсивный вызов является последней операцией в функции.
```go
func factorialTail(n, acc int) int {
	if n <= 0 {
		return acc
	}

	return factorialTail(n, acc * n)
}
```