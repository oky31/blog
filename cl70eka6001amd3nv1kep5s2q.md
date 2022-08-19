## Go (Map struktur Data)

- adalah implementasi dari has table,
- has table adalah struktur data  sepasang key/value  yang tidak ter-urut
- deklarasi `map[K]V` dimana `K` adalah tipe data dari key dan `V` adalah tipe data dari value
- membuat map dengan build-in function
    
    ```go
    ages := make(map[string]int) // map dari string ke integer
    ```
    
- membuat map literal
    
    ```go
    ages := map[string]int{
    	"alice": 31,
      "charlie": 34,
    }
    ```
    
- membuat empty map
    
    ```go
    ages := map[string]int{}
    ```
    
- element dari map bukanlah sebuah variable, karna itu element tersebut tidak memliki alamat memori
- ketika mengakses element map dengan key yang tidak ada maka tidak akan error melaikan akan mengembalikan nilai default dari tipe data element tersebut, karena itu untuk melakukan pengecekan element ada di dalam map bisa sebagai berikut :
    
    ```go
    ages := map[string]int{"rudi": 21, "tama": 30}
    
    if _, ok := ages["oky"];  !ok {
    	fmt.Println("age oky not found in map")
    }
    
    ```
    
- ketika menghapus element map dengan key yang tidak ada, maka tidak akan terjadi error
- map tidak bisa di bandingkan ( ==, ≠) cara satu2nya untuk membandingkan adalah dengan mengunakan perulangan, lalu bandingkan `key` dan `value` nya
- map adalah *reference types* seperti  pointer atau slices, jadi ketika mendeklarasi kan map dengan seperti ini
    
    ```go
    var ages map[string]int
    ages["rudi"] = 20
    fmt.Println(ages)
    ```
    
    maka akan terjadi error `panic: assignment to entry in nil map` karena `ages` itu tidak mereferences ke map yang sudah di inisialisasikan, untuk menginisialisasikan map gunakan fungsi `make`
    
    ```go
    ages = make(map[string]int)
    ages["rudi"] = 20
    fmt.Println(ages)
    ```
    
    contoh di atas itu melakukan 2 langkah yaitu `allocates` dan `initializes` has map struktur data
    

**Operasi di map**

- mengakses map  dengan mengunakan key

```go
ages := map[string]int{"rudi": 21, "tama": 30}
ages["tama"]
```

- menghapus map mengunakan build-in fungsi `delete`

```go
ages := map[string]int{"rudi": 21, "tama": 30}
delete(ages, "tama")
```

- untuk melakukan perulangan, mengunakan `range base` for loop

```go
ages := map[string]int{"rudi": 21, "tama": 30}

for name, age := range ages {
	fmt.Printf("Name : %v\nAge : %v\n", name, age)
}
```

**Map tidak aman untuk pengunaan konkurensi**

- ketika mengakses map di operasi yang *concurrent* seperti `goroutine` maka harus melalui mekanisme *synchronization*

**Source :**

- [Go maps in action](https://go.dev/blog/maps)
- The Go Programming Language → Composite types → Maps