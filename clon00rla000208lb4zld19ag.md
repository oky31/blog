---
title: "Journey #3 - Create User"
datePublished: Mon Nov 06 2023 14:29:39 GMT+0000 (Coordinated Universal Time)
cuid: clon00rla000208lb4zld19ag
slug: journey-3-create-user
tags: go, backend, Go

---

Di journey #2, telah membuat test case untuk `CreateUserHandler` , untuk melengkapi handler tersebut akan kita mulai dari beberapa tahap berikut

#### Pemilihan Database

karena app ini akan berbasis sql maka akan mengunakan sql driver yang di rekomendasikan, bisa lihat di sini [https://github.com/golang/go/wiki/SQLDrivers](https://github.com/golang/go/wiki/SQLDrivers)

setelah berfikir yang tidak terlalu panjang akhirnya saya memutuskan untuk mengunakan sqlite dengan driver [**go-sqlite3**](https://github.com/mattn/go-sqlite3)

kenpa memilih sqlite karena ringan dan berjalan di local file system.

#### Decode http body

karena body http itu adalah stream, ada beberapa hal yang harus di perhatikan dalam melakukan decode yaitu :

1. Gunakan fungsi `json.NewDecoder()` karena body adalah stream
    
    ```go
    json.NewDecoder(r.Body).Decode(&p)
    ```
    
2. Ketika terjadi error di method `Decode()` tidak sepenuhnya kesalahan client, bisa saja `json.InvalidUnmarshalError` , jika ini terjadi berarti ini kesalahan di app kita, maka di sarankan untuk error di masukan ke log dan mengirim `500 Internal Server Error` ke client
    
3. hasil error dari Decode() tidak di sarankan untuk di kirim ke client, karena kita meng ekspos informasi yang detail ke client, selain itu pesan yang di sampaikan juga tidak deskriptif (hanya di mengerti programmer)
    
4. client bisa saja mengirim field yang tidak diharapkan, atau field yang tidak ada di struct saat di decode, biasanya ini akan menjadi silent error, untuk menghindari itu tambahkan validasi `DisallowUnknownFields()`
    
5. tambahkan limitasi body request agar menghindarkan client mengirim file yang sangat besar dengan cara menambahkan `http.MaxBytesReader()` function
    
6. tambahkan pengecekan `Content-Type: application/json` agar mengaransi hanya file json yang dikirim dan cukup dengan mengecek header saja tidak perlu melanjutkan ke Decode body
    
7. decoder stream `json.NewDecoder()` di design untuk decode stream JSON Object seperti `{"Name": "Bob"}{"Name": "Carol": "Age": 54}` . pastikan beri notifkasi ke user untuk hanya mengirim satu object json saja
    

Contoh Code :

```go
if r.Header.Get("Content-Type") == "" {
	message := "Content-Type header required"
	http.Error(w, message, http.StatusUnsupportedMediaType)
	cuh.logger.Error(message)
	return
}

if r.Header.Get("Content-Type") != "" {
	value, _ := header.ParseValueAndParams(r.Header, "Content-Type")
	if value != "application/json" {
		message := "Content-Type header is not application/json"
		http.Error(w, message, http.StatusUnsupportedMediaType)
		cuh.logger.Error(message)
		return
	}
}

r.Body = http.MaxBytesReader(w, r.Body, size1MB)

dec := json.NewDecoder(r.Body)
dec.DisallowUnknownFields()

var payload CreateUserPayload
err := dec.Decode(&payload)
if err != nil {
	var syntaxError *json.SyntaxError
	var unmarshalTypeError *json.UnmarshalTypeError

	switch {

	case errors.As(err, &syntaxError):
		msg := fmt.Sprintf("Request body contains badly-formed JSON (at position %d)", syntaxError.Offset)
		http.Error(w, msg, http.StatusBadRequest)

	case errors.Is(err, io.ErrUnexpectedEOF):
		msg := "Request body contains badly-formed JSON"
		http.Error(w, msg, http.StatusBadRequest)

	case errors.As(err, &unmarshalTypeError):
		msg := fmt.Sprintf("Request body contains an invalid value for the %q field (at position %d)", unmarshalTypeError.Field, unmarshalTypeError.Offset)
		http.Error(w, msg, http.StatusBadRequest)

	case strings.HasPrefix(err.Error(), "json: unknown field "):
		fieldName := strings.TrimPrefix(err.Error(), "json: unknown field ")
		msg := fmt.Sprintf("Request body contains unknown field %s", fieldName)
		http.Error(w, msg, http.StatusBadRequest)

	case errors.Is(err, io.EOF):
		msg := "Request body must not be empty"
		http.Error(w, msg, http.StatusBadRequest)

	case err.Error() == "http: request body too large":
		msg := "Request body must not be larger than 1MB"
		http.Error(w, msg, http.StatusRequestEntityTooLarge)

	default:
		log.Print(err.Error())
		http.Error(w, http.StatusText(http.StatusInternalServerError), http.StatusInternalServerError)
		}

	return
}
```

#### **Struktur Directory & membuat layer arsitektur**

Untuk sementa layer akan di bagi menjadi 3 yaitu

* http handler
    
* bussines logic (services)
    
* data source (repository)
    

ini hanya layer sederhana di mana :

* handler hanya bisa mengakses servcies
    
* services hanya bisa mengakses data source untuk kebutuhan businnes logic
    
* datasource (repository) , layar yang berinteraksi dengan database seperti penulisan SQL bisa di sini
    

untuk sementara saya tidak akan mebuat struktur folder berdasarkan layer tersebut, semua di satukan dalam satu folder saja

#### **Format Response**

Agar response http standar saya membuat standar response sebagai berikut

* | field name | tipe data | deskripsi |
    | --- | --- | --- |
    | errors | array of object | field ini hanya muncul ketika terjadi error saja, berisi detail dari eror yang terjadi |
    | message | string | berisi pesan yang menampilkan tentang request tersebut |
    | data | object atau array of object | berisi data yang akan di gunakan oleh consumer |
    
* response success
    
    ```json
    {
       "message" : "success create user",
       "data": {
          "id" : 1
       }
    }
    ```
    
* response errors
    
    ```json
    {
    	 "message" : "Internal Server Error",
    }
    ```
    
    ```json
    {
       "errors" : [
          {"username": "username can't empty"}
       ],
       "message" : "error validasi",
    }
    ```
    
* membuat implementasi response json
    
    ```go
    type ResBody struct {
    	ErrorRes interface{} `json:"errors,omitempty"`
    	Message  string      `json:"message"`
    	Data     interface{} `json:"data,omitempty"`
    }
    ```
    
    ```go
    resp := ResBody{
    		Message: "success create user",
    		Data: map[string]interface{}{
    			"id": userId,
    		},
    	}
    
    	respJson, err := json.Marshal(resp)
    	if err != nil {
    		log.Print(err.Error())
    		http.Error(w, http.StatusText(http.StatusInternalServerError), http.StatusInternalServerError)
    		return
    	}
    
    	w.Header().Set("Content-Type", "application/json")
    	w.WriteHeader(http.StatusCreated)
    	w.Write(respJson)
    ```
    

Untuk Detail source code sekarang sudah bisa di lihat di gitub [https://github.com/oky31/fake-marketplace-rest-api/tree/journey-3](https://github.com/oky31/fake-marketplace-rest-api/tree/journey-3)

### Referensi :

* [https://www.alexedwards.net/blog/how-to-properly-parse-a-json-request-body](https://www.alexedwards.net/blog/how-to-properly-parse-a-json-request-body)