```go
import "container/list"  
  
type Stack struct {  
    list *list.List  
}  
  
func NewStack() *Stack {  
    return &Stack{list: list.New()}  
}  
  
func (s *Stack) Push(val rune) {  
    s.list.PushBack(val)  
}  
  
func (s *Stack) Pop() rune {  
    item := s.list.Back()  
    val := item.Value.(rune)  
    s.list.Remove(item)  
  
    return val  
}  
  
func (s *Stack) Clean() {  
    for s.list.Len() > 0 {  
       s.list.Remove(s.list.Back())  
    }  
}  
  
func (s *Stack) IsEmpty() bool {  
    return s.list.Len() == 0  
}  
  
func isPalindromicStack(s string) bool {  
    stack := NewStack()  
    for _, el := range s {  
       stack.Push(el)  
    }  
  
    for _, el := range s {  
       if el != stack.Pop() {  
          return false  
       }  
    }  
  
    return true  
}  
  
func isPalindromicPointers(s string) bool {  
    l, r := 0, len(s)-1  
  
    for l < r {  
       if s[l] != s[r] {  
          return false  
       }  
       l++  
       r--  
    }  
  
    return true  
}
```