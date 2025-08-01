```txt
Дана строка, состоящая из букв ‘X’, ‘Y’ и ‘O’.
Необходимо найти кратчайшее расстояние между буквами ‘X’ и ‘Y’,
либо вывести 0, если ‘X’ или ‘Y’ отсутствуют.
```


```go
func FindDistance(str string) int {  
    hashT := make(map[rune]int, 2)  
    minDist := math.MaxInt  
    for i, c := range str {  
       switch c {  
       case 'Y':  
          if v, ok := hashT['X']; ok {  
             minDist = min(minDist, abs(v-i))  
          }  
          hashT[c] = i  
       case 'X':  
          if v, ok := hashT['Y']; ok {  
             minDist = min(minDist, abs(v-i))  
          }  
          hashT[c] = i  
       }  
    }  
  
    if len(hashT) < 2 {  
       return 0  
    }  
  
    return minDist  
}

func abs(a int) int {  
    if a < 0 {  
       return -1 * a  
    }  
  
    return a  
}
```

// naive approche
```go
func FindDistance(str string) int {  
    if len(str) == 0 {  
       return 0  
    }  
  
    xArr := make([]int, 0, 4)  
    yArr := make([]int, 0, 4)  
  
    for i, c := range str {  
       if c == 'X' {  
          xArr = append(xArr, i)  
       }  
       if c == 'Y' {  
          yArr = append(yArr, i)  
       }  
    }  
  
    if len(xArr) == 0 || len(yArr) == 0 {  
       return 0  
    }  
  
    minDist := 1<<63 - 1  
    for _, indX := range xArr {  
       for _, indY := range yArr {  
          minDist = min(minDist, abs(indX-indY))  
       }  
    }  
  
    return minDist  
}  
  
func abs(a int) int {  
    if a < 0 {  
       return -1 * a  
    }  
  
    return a  
}
```