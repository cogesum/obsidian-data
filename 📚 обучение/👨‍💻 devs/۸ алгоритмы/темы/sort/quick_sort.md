---
tags:
  - algos
  - program
  - sort
---

- изначально выбирает опорный элемент (pivot)
- делить на два подмассива - один меньше pivot, другой больше
- затем рекурсивно эти подмассивы делятся дальше

 ![[Pasted image 20250706120233.png]]
	 - **Выбор опорного элемента (pivot selection).** Как уже упоминалось, использование median-of-three или случайного элемента уменьшает вероятность худшего сценария.
	- **Tail Call Optimization (TCO)**. Оптимизация рекурсии путем отказа от рекурсивного вызова для одной из частей массива (например, более короткой) позволяет избежать глубоких стеков вызовов и, как следствие, переполнения стека.
	- **Гибридные алгоритмы.** На практике часто используется комбинация Quick Sort и других алгоритмов, таких как Insertion Sort, для небольших подмассивов. Эта техника снижает количество рекурсивных вызовов и уменьшает накладные расходы.
	- **Параллельная сортировка.** Если необходимо работать с большими объемами данных, можно распределить вычисления на несколько ядер процессора или кластеров, реализовав параллельную версию Quick Sort.


### Поиск k-ой последовательности
```go
// choose pivot  
// go to the end pivot  
// iterate by all arr[left:right] filter by pivot by store variable  
// return pivot to store place  
// return store like pivot index  
func partition(arr []int, left, right int) int {  
    pivotIdx := left + (right-left)/2  
    pivot := arr[pivotIdx]  
    arr[pivotIdx], arr[right] = arr[right], arr[pivotIdx]  
    // itearte by arr  
    store := left  
    for i := left; i < right; i++ {  
       if arr[i] < pivot {  
          arr[i], arr[store] = arr[store], arr[i]  
          store++  
       }  
    }  
    // move pivot to store palce  
    arr[store], arr[right] = arr[right], arr[store]  
  
    return store  
}
  
func FindKthOrderStatitstic(arr []int, k int) int {  
    l, r := 0, len(arr)-1  
    for l <= r {  
       pivotIdx := partition(arr, l, r)  
       if pivotIdx == k {  
          return arr[k]  
       }  
  
       if pivotIdx > k {  
          r = pivotIdx - 1  
       } else {  
          l = pivotIdx + 1  
       }  
    }  
  
    return -1  
}
```