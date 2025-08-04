```go
func BinSearchIter(data []int, target int) int {  
    l, r := 0, len(data)-1  
    if target < data[l] || target > data[r] {  
       return -1  
    }  
  
    for l <= r {  
       mid := data[l+(r-l)/2]  
       if mid == target {  
          return mid  
       }  
       if mid > target {  
          r = mid - 1  
       } else {  
          l = mid + 1  
       }  
    }  
  
    return -1  
}
```