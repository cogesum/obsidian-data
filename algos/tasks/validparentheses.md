```go
package main  
  
import "fmt"  
  
// '()[]{}'  
// '((([)])))'  
func ValidParentheses(s string) bool {  
    hashT := map[rune]rune{  
       ')': '(',  
       ']': '[',  
       '}': '{',  
    }  
  
    stackSlice := make([]rune, 0)  
    for _, v := range s {  
       if b, ok := hashT[v]; ok {  
          stackLen := len(stackSlice)  
          if stackLen == 0 || stackSlice[stackLen-1] != b {  
             return false  
          }
  
          stackSlice = stackSlice[:stackLen-1]  
       } else {  
          stackSlice = append(stackSlice, v)  
       }  
    }  
  
    return len(stackSlice) == 0  
}  
  
// Thinking  
// 1. Подготовить map которая содержит ключем закрывающую скобку, а значением - открывающую. Сделаем стек на массиве, который будет  
// добавлять элемент, если он не найдет в мапе и удалять прошлый элемент, если найден его закрывающий  
// 2. Не учел идею, каким образом мы чистим стек. Если мы нашли закрывающую скобку, мы сравниваем ее с значением первым в стеке. Если все ок, то удаляем, если нет, то вовзрашаем true.  
// 3. При попытке вытащить невалидный слайс, я изначально поставил && вместо ||, но это приведет к панике, поскольку, два условия должны выполниться. А с или проверится только первое и выйдет.  
  
func main() {  
    fmt.Println(ValidParentheses("()"))     // true  
    fmt.Println(ValidParentheses("()[]{}")) // true  
    fmt.Println(ValidParentheses("(]"))     // false  
    fmt.Println(ValidParentheses("([)]"))   // false  
    fmt.Println(ValidParentheses("{[]}"))   // true  
    fmt.Println(ValidParentheses(""))       // true  
}
```