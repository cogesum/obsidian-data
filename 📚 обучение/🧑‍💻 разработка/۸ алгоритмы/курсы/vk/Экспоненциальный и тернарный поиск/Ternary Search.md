**Тернарный поиск** - разбиение последовательности на три части.
- `left` - первый элемент
- `right` - последний элемент
- `m1 = left + (right - left)/3`
- `m2 = right - (right-left)/3`
Проверяем `m1` и `m2` на равенство искомому значению.
- Если m1 меньше искомого значения, а m2 больше, тогда сдвигаем левую и правую границы `left = m1+1`, `right = m2-1` и так далее

Общая сложность будет: O(log(n)) по основанию 3.

#### Реализация рекурсией
```go
func RecPath(arr []int, target int) int {  
    if len(arr) == 0 {  
       return -1  
    }  
    return ternarySearch(arr, target, 0, len(arr)-1)  
}  
  
func ternarySearch(data []int, target, l, r int) int {  
    if l > r {  
       return -1  
    }  
  
    m1 := l + (r-l)/3  
    m2 := r - (r-l)/3  
  
    if data[m1] == target {  
       return m1  
    }  
    if data[m2] == target {  
       return m2  
    }  
  
    if target < data[m1] {  
       return ternarySearch(data, target, l, m1-1)  
    }  
    if target > data[m2] {  
       return ternarySearch(data, target, m2+1, r)  
    }  
  
    return ternarySearch(data, target, m1+1, m2-1)  
}
```

#### Итеративный
```go
func IterPath(arr []int, target int) int {  
    if len(arr) == 0 {  
       return -1  
    }  
      
    l, r := 0, len(arr)-1  
  
    for l <= r {  
       m1 := l + (r-l)/3  
       m2 := r - (r-l)/3  
  
       if arr[m1] == target {  
          return m1  
       }  
       if arr[m2] == target {  
          return m2  
       }  
  
       if arr[m1] > target {  
          r = m1 - 1  
       } else if arr[m2] < target {  
          l = m2 + 1  
       } else if arr[m1] < target && target < arr[m2] {  
          r = m2 - 1  
          l = m1 + 1  
       }  
    }  
  
    return -1  
}
```