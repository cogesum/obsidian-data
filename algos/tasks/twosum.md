```go
package prepare  
  
// [2, 7, 11, 15] 9  
  
// {2:7, 7:2, 11:-2, 15:-6}  
  
// {7:0, 2:1, }  
  
func TwoSum(arr []int, target int) []int {  
    hashT := make(map[int]int)  
    for i, v := range arr {  
       comp := target - v  
       if idx, ok := hashT[comp]; ok {  
          return []int{idx, i}  
       }  
  
       hashT[v] = i  
    }  
  
    return []int{-1, -1}  
}  
  
// 1. Я думал, надо найти первое значение, поэтому ищу разницу, но выхожу сразу, как только найду их, а нужно найти все вхождения.  
// 2. Все таки размышления были в верную сторону. В ключе мы храним разницу между таргетом и текущем значении, а в value храним индекс текущей разницы  
// по формуле {target-v:i}.  
// 3. Видимо, размышлял не в ту сторону. Ошибка заключалась в том, что я заполнял таблицу некорректно, все проще. В ключ кладем текущее значение, в value индекс.  
// и, к примеру, в момент итерации мы высчитываем `complement := target - num` и это и есть искомое value, которое мы заложили раньше.  
  
func TwoSum(arr []int, target int) []int {  
    hashT := make(map[int]int)  
  
    for i, num := range arr {  
       // write diff  
       complement := target - num  
       if idx, ok := hashT[complement]; ok {  
          return []int{idx, i}  
       }  
  
       hashT[num] = i  
    }  
  
    return []int{-1, -1}  
}  
```