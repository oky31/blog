---
title: "Journey #2 - Create User"
datePublished: Sun Aug 13 2023 16:36:16 GMT+0000 (Coordinated Universal Time)
cuid: cll9o46cs00000ajwhxjh5lmz
slug: journey-2-create-user
tags: go-cjffccfnf0024tjs1mcwab09t

---

Dari journey #1 saya telah membuat test sederhana untuk `handlerCreateUser` , tetapi test tersebut ketika dijalankan akan gagal, karena code yang di tulis di `handlerCreateUser` belum sesuai dengan kebutuhanya.

oh iya ada hal yang lupa saya tambahkan di test yaitu mengirim header `Content-Type` dengan nilai `application/json` karena data yang di kirim di dalam payload adalah json, untuk detailnya berikut :

```go
func TestCreateUser(t *testing.T) {

	payload := data.CreateUserPayload{
		FirstName: "Oky",
		LastName:  "Saputra",
		Email:     "oky@gmail.com",
		Address:   "Jl. Perjalanan panjang",
		UserName:  "oky55",
		Password:  "12345",
	}

	bytesPayload, err := json.Marshal(payload)
	if err != nil {
		t.Errorf("Error in marshal json %v", err)
	}

	req := httptest.NewRequest(http.MethodPost, "/v1/user/create", bytes.NewReader(bytesPayload))
	req.Header.Set("Content-Type", "application/json")

	res := httptest.NewRecorder()

	createUserHandler := CreateUserHandler{}
	createUserHandler.ServeHTTP(res, req)

	assertHttpStatus(t, res.Code, http.StatusCreated)
}
```

Agar test di atas bisa berhasil dijalankan maka pada saat implementasi nya, kita membutuhkan validasi `Content-Type` detailnya berikut :

```go
type CreateUserHandler struct{}

func (cuh *CreateUserHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {

	if r.Header.Get("Content-Type") == "" {
		message := "Content-Type header required"
		http.Error(w, message, http.StatusUnsupportedMediaType)
		return
	}

	if r.Header.Get("Content-Type") != "" {
		value, _ := header.ParseValueAndParams(r.Header, "Content-Type")
		if value != "application/json" {
			message := "Content-Type header is not application/json"
			http.Error(w, message, http.StatusUnsupportedMediaType)
			return
		}
	}

	w.WriteHeader(http.StatusCreated)
}
```

* disini melakukan validasi `Content-Type` yang dikirim tidak boleh kosong
    
* ketika `Content-Type` tidak kosong, maka harus melakukan validasi terhadap tipe Content , dan nilainya wajib `application/json`
    

Harusnya dengan code di atas, testing untuk handler akan berhasil ketika di jalankan, ini adalah tahapan awal di mana, saya hanya melakukan 2 validasi di test yaitu content type & http status code, untuk selanjutnya saya akan menyimpan data yang dikirim melalui payload ke database.

Bersambung di journey#4 ....