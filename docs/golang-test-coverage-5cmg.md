# Golang 测试覆盖率

> 原文：<https://dev.to/lumochift/golang-test-coverage-5cmg>

Tulisan ini adalah lanjutan dari [Golang Unit Test](https://dev.to/h4ckm03d/golang-unit-test-3fmh-temp-slug-2651667) . Kali ini membahas tentang *test coverage* , yaitu untuk mengetahui apakah unit test sudah memenuhi semua jalur logika dari sistem yang kita uji. Contoh sederhana sebagai berikut:

Suatu fungsi untuk menentukan nilai maksimal dari dua integer input.

Input didefinisikan dalam variabel `a` dan `b` .

Jika `a` lebih besar atau sama dengan `b` maka return `a` , sebaliknya jika `b` lebih besar maka return `b` .

Sample code sebagai berikut:

```
package main

func Max(a, b int) int {
    if a >= b {
        return a
    }

    return b
} 
```

Kemudian *unit test* sebagai berikut:

```
package main

import "testing"

func TestMax(t *testing.T) {
    if Max(1, 3) != 3 {
        t.Error("error, seharusnya 3")
    }

    // Jika pengecekan dibawah ini dihilangkan maka test coverage jadi 66.67%
    if Max(4, 3) != 4 {
        t.Error("error, seharusnya 4")
    }
} 
```

Jika *test coverage* dijalankan menggunakan perintah `go test --cover` di cli maka hasilnya sebagai berikut:

```
$ go test --cover
PASS
coverage: 100.0% of statements
ok github.com/h4ckm03d/blog-codes/test-coverage 0.002s 
```

Coba kita 审查*单元测试* diatas。

Fungsi Max mempunya 2 cabang logika dan terdiri dari 3 statement `if a >= b` , `return a` , dan `return b` .

`Max(1, 3)` ini menghasilkan nilai 3, jika dirunut dari fungsi Max akan melewati 2 statement `if a >= b` dan karena kondisi tidak terpenuh maka langsung ke `return b` . Jadi hanya tercover 2 statement sehingga test coverage jika pengecekan `Max(4,3)` dihilangkan maka hasilnya **66.67%** .

Karena pemanggilan fungsi `Max(4,3)` menghasilkan jalur yang berbeda dengan proses pada no.2 yaitu `if a >= b` dan `return a` . Maka semua cabang logika dilewati, karena itulah hasil *test coverage* keseluruhan jadi **100%** .

Semoga yang sedikit ini bisa membantu pemahaman tentang *test coverage* menggunakan golang. Sampai jumpa lagi di tulisan selanjutnya.