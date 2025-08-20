---
tags:
  - algos
  - sort
  - program
---
- Объединение отсортированных массивов (один из видом "разделяй и влавствуй")
#### Алгоритм
- делим массив пополам
- сортируем каждую половину независимо
- объединяем две отсортированные последовательности
- алгоритм слияния - два указателя

```go
func mergeSort(arr []int) []int {  
    if len(arr) < 2 {  
       return arr  
    }  
  
    mid := len(arr) / 2  
  
    left := arr[0:mid]  
    right := arr[mid:]  
  
    return merge(mergeSort(left), mergeSort(right))  
}  
  
func merge(l, r []int) []int {  
    res := make([]int, 0, len(r)+len(l))  
  
    first, second := 0, 0  
    for first < len(l) && second < len(r) {  
       if l[first] > r[second] {  
          res = append(res, r[second])  
       } else {  
          res = append(res, l[first])  
       }  
  
       first++  
       second++  
    }  
  
    if first < len(l) {  
       res = append(res, l[first:]...)  
    }  
  
    if second < len(l) {  
       res = append(res, l[second:]...)  
    }  
  
    return res  
}
```