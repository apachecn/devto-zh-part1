# 结构体

> 原文：<https://dev.to/lumochift/struct-4c8c>

Dalam pemrograman berorientasi object tentunya kita pasti mengenal apa itu *class* , *enkapsulasi* , *inheritance* , *polimorfisme* , dan lain sebagainya. Apakah semuanya masih bisa kita pakai dalam *golang* ? Tentu tidak, tapi sebelum membahas lebih lanjut tentang konsep OOP sebaiknya kita perlu mengetahui tipe data dasar untuk OOP di golang yaitu *struct* .

# *结构*

## TL；博士；医生

*   tidak ada `class` , adanya `struct`
*   tidak ada `inheritance` , tapi menggunakan komposisi
*   tidak ada `konstruktor` , tapi menggunakan `return [pointer] function`
*   public access modifier menggunakan nama fungsi/peubah huruf besar
*   public access modifier menggunakan nama fungsi/peubah huruf kecil

*Struct* merupakan tipe data dasar yang digunakan untuk menggantikan fungsi dari *class* di bahasa pemrograman lain seperti C#, Java, C++, dll. Perlu kita tahu bahwa konsep OOP golang merupakan versi sederhana dari OOP itu sendiri. Mengapa sederhana? Karena dalam golang tidak ada konsep `inheritance` tapi lebih pada `composition` . Jadi menghilangkan konsep *inheritance* untuk mengurangi kompleksitas dari suatu `struct` dan jika ingin menggunakan `struct` lain bisa menggunakan konsep komposisi. Untuk lebih jelasnya seperti contoh kode dibawah ini.

```
type Persegi struct{
    S float64
}

type Kubus struct{
    Alas Persegi
    Tinggi float64
} 
```

## Fungsi dalam 结构

Dalam contoh diatas dalam bahasa pemrograman seperti Java, kelas kubus bisa didefinisikan sebagai turunan dari `Persegi` sedangkan dalam `golang` menggunakan tipe data `Persegi` untuk membuat suat peubah sebagai penyusun `struct Kubus` . Untuk method seperti contoh dibawah ini.

```
// contoh public method dalam struct Persegi
func(p *Persegi) Luas() float64{
    return p.S * p.S
}

// contoh private method dalam struct Persegi
func(p *Persegi) jumlahSisi() int {
    return 4
}

func(k *Kubus) Volume() float64 {
    return k.Alas.Luas() * k.Tinggi
} 
```

Penggunaan peubah `p` dalam `func(p *Persegi)` boleh diganti dengan apa saja, tentunya harus sesuai dengan konteks agar tidak membingungkan saat *ngoding* . Peubah `p` ini berfungsi sebagai `this` jika kita melihat dari sisi `Java` atau `C#` .

## 访问修饰符

Apa yang membedakan `access modifier` dalam kedua fungsi diatas? Iyap, benar sekali perbedaan huruf kapital nama fungsi. Didalam `golang` penggunaan **huruf kapital** merepresentasikan **public** akses sedangkan nama fungsi yang **diawali** dengan **huruf kecil** merepresentasikan **private** akses.

## Konstruktor

Lalu bagaimana untuk inisialisasi `struct` ? Perlu diketahui didalam golang juga tidak ada konstruktor. Jika tidak ada konstruktor bagaimana untuk inisialisasi `struct` ? Pastinya sudah ada cara tersendiri untuk inisialisasi `struct` yaitu menggunakan fungsi yang mengembalikan nilai [pointer](https://dev.to/h4ckm03d/pointer-4nin-temp-slug-4199032) dari suatu `struct` . Lebih jelasnya seperti berikut.

```
func NewPersegi(s float64) *Persegi{
    return &Persegi{S: s}
} 
```

Berikut contoh lengkap kode terkait dengan `struct` persegi.

```
package main

import (
    "fmt"
)

type Persegi struct {
    S float64
}

func (p *Persegi) Luas() float64 {
    return p.S * p.S
}

// contoh private method dalam struct Persegi
func (p *Persegi) jumlahSisi() int {
    return 4
}

func NewPersegi(s float64) *Persegi {
    return &Persegi{S: s}
}

func main() {
    a := NewPersegi(4)
    fmt.Println(a.Luas())
} 
```

## 结构丹指针

Dalam contoh fungsi diatas menggunakan [pointer](https://dev.to/h4ckm03d/pointer-4nin-temp-slug-4199032) untuk deklarasi `func(p *Persegi)` ,apa bedanya jika tidak menggunakan [pointer](https://dev.to/h4ckm03d/pointer-4nin-temp-slug-4199032) ? Kita gunakan eksperimen untuk mengubah nilai `S` dalam `struct` `Persegi` , satu menggunakan [pointer](https://dev.to/h4ckm03d/pointer-4nin-temp-slug-4199032) satunya tidak menggunakan. Berikut contoh kodenya.

```
func (p *Persegi) UbahS(s float64){
    p.S = s
}

func (p Persegi) UbahS2(s float64) {
     p.S = s
}

func main() {
    a := NewPersegi(4)
    fmt.Println(a.Luas(), a.S)
    a.UbahS(5)
    fmt.Printf("ubah dengan [pointer] %f\n", a.S)

    a.UbahS2(10)
    fmt.Printf("ubah tanpa [pointer] %f\n", a.S)
}
// Hasilnya
// 16 4
// ubah dengan [pointer] 5.000000
// ubah tanpa [pointer] 5.000000

// Program exited. 
```

Jika penasaran hasilnya, anda bisa mencoba sendiri [contoh](https://play.golang.org/p/0UKYzn6R_A) di play.golang.org. Dari contoh diatas bahwa fungsi yang menggunakan [pointer](https://dev.to/h4ckm03d/pointer-4nin-temp-slug-4199032) akan berpengaruh ke `struct` asal sedangkan fungsi `UbahS2` tidak berpengaruh pada nilai `S` karena jika tanpa menggunakan [pointer](https://dev.to/h4ckm03d/pointer-4nin-temp-slug-4199032) maka akses peubah `p` merupakan `copy struct` bukan sebagai reference dari `struct` .

Sampai jumpa di tulisan selanjutnya…