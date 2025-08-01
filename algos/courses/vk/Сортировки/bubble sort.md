- до тех пор, пока соседние элементы не в порядке, меняем их местами
```go
func BubbleSort(data []int) {  
    var sorted bool  
  
    for !sorted {  
       sorted = true  
       for j := 0; j < len(data)-1; j++ {  
          k := j + 1  
          if data[j] > data[k] {  
             data[j], data[k] = data[k], data[j]  
             sorted = false  
          }  
       }  
    }  
}
```