---
title: "Struktur Folder Web App - Go"
datePublished: Sat Dec 09 2023 16:16:33 GMT+0000 (Coordinated Universal Time)
cuid: clpy9dcfl000008l7einx8344
slug: struktur-folder-web-app-go
tags: go, golang, go-cjffccfnf0024tjs1mcwab09t

---

Di sarankan untuk menentukan struktur folder di dalam sebuah project adalah di awal

**Saran** :

* don’t over-complicate things
    
* sebetulnya pola struktur aplikasi, tidak selalu bisa di samakan, itu tergantung dengan situasi
    
* jangan terlalu mem-perumit masalah, tambahkan struktur dan kompleksitas hanya jika di perlukan saja
    

Struktur yang di sarankan dan terbukti sukses di project go adalah :

```go
cmd/
	api-server/
		handlers.go
		main.go
internal/
	users/
	auth/
ui/
go.mod
```

* `cmd` directory akan berisi application-specific code untuk executable application di dalam project, kenapa application-specific karena di go kan hasil code di compile menjadi binary, nah nanti binary tersebut ketika di jalankan itu akan menjadi aplikasi spesifik, misal bisa menjadi web server, atau cli aplikasi
    
* `internal` berisi kode *non-application-specific* , menyimpan kode yang berpotensi dapat di gunakan kembali, seperti validation, model database SQL. kenapa *non-application-specific* karena code yang di dalam internal ini akan bisa digunakan untuk aplikasi spesifik seperti web, atau cli, atau lainya jadi bisa saja project kita memiliki 2 aplikasi atau 2 binary yaitu cli dan web api, tetapi ke 2 spesifik aplikasi ini akan bisa mengunakan kode yang sama misal, code model sql yang ada di dalam internal
    
* `ui` berisi user interface(UI) code seperti html, css, javascript
    

**Penting :**

* arti `internal` di go memiliki arti penting, paket apa pun yang berada di bawah direktori ini hanya dapat hanya dapat di import dengan kode di dalam induk direktori internal
    
* artinya bahwa setiap paket yang berada di internal hanya dapat di import dengan kode di dalam direktori proyek kita.
    
* berarti paket apapun di bawah internal tidak dapat diimpor dengan kode di luar proyek kita
    
* `cmd` adalah singkatan dari `command`
    

### Resource :

* [Project structure and organization](https://lets-go.alexedwards.net/sample/02.06-project-structure-and-organization.html)
    
* [Organizing a Go module](https://go.dev/doc/modules/layout#server-project)
    
* [Standard Go Project Layout (not official)](https://github.com/golang-standards/project-layout)