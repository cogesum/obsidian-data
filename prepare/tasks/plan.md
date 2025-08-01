Примеры задач:
package main

import "fmt"

func foo(arr []int) {
    arr = append(arr, 9, 9)
}

func main() {
    src := []int{1,2, 3, 4, 5}
    arr := make([]int, 3)
    copy(arr, src)
    // []int{ src[0], src[1]  }
    // []int{ src[0:3]... }
    // append([]int{}, src[0:3]...)
    (students := [...]string{"Ivan", "Anton", ""}).
    k := [2]int( slice  )     
    foo(arr)
    fmt.Println(src)
    fmt.Println(arr)
}
