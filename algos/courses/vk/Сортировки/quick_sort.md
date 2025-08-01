---
tags:
  - algos
  - sort
  - program
---
- выбираем опорный элемент, все что меньше, переносим влево, все что больше, переносим вправо. Рекурсивно повторяем для каждой части.

Алгоритм:
- выбираем опорный элемент - pivot. 
- разбиваем массив in-place на меньшие и большие значения
- повторяем алгоритм для большей и меньшей части


```go 
func qsort(arr []int, left, right int) {  
    if left >= right {  
       return  
    }  
  
    l, r := left, right  
    pivot := arr[(left+right)/2]  
  
    for l <= r {  
       for pivot > arr[l] {  
          l++  
       }  
       for pivot < arr[r] {  
          r--  
       }  
  
       if l <= r {  
          arr[l], arr[r] = arr[r], arr[l]  
          l++  
          r--  
       }  
    }  
  
    if r > left {  
       qsort(arr, left, r)  
    }  
  
    if l < right {  
       qsort(arr, l, right)  
    }  
  
    return  
}
```

- 