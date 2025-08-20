```go
func BinSearchRec(data []int, target int) int {  
    l, r := 0, len(data)-1  
  
    if target < data[l] || target > data[r] {  
       return -1  
    }  
  
    return binSearch(data, l, r, target)  
}  
  
func binSearch(data []int, l, r, target int) int {  
    if l > r {  
       return -1  
    }  
  
    mid := data[l+(r-l)/2]  
    if mid == target {  
       return mid  
    }  
    if mid > target {  
       return binSearch(data, l, mid-1, target)  
    }  
  
    return binSearch(data, mid+1, r, target)  
}
```