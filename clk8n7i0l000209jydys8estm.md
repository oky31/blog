---
title: "Journy #1 - Create User"
datePublished: Tue Jul 18 2023 18:43:23 GMT+0000 (Coordinated Universal Time)
cuid: clk8n7i0l000209jydys8estm
slug: journy-1-create-user
tags: go, http, test-driven-development

---

Module yang pertama dibuat adalah user management, karena di sini saya akan mempelajari banyak hal terutama tentanang autentikasi.

## Feature

1. user bisa melakukan pendaftaran dengan data form berikut :
    
    * nama depan (required)
        
    * nama belakang (required)
        
    * email (required)
        
    * alamat (required)
        
    * username (required)
        
    * password (required)
        
2. user bisa melakukan update profile
    
3. user bisa melakukan login dengan input username & password
    
4. user bisa melakukan pembuatan toko
    
5. user bisa melakukan update informasi toko
    

## Schema (data model)

untuk tahap awal skema pemodelan data di database tidak tetap bisa berubah berdasarkan kebutuhan yang terfikir nanti. mungkin untuk di awal hanya memiliki 2 tabel saja yaitu **users** & **stores**

untuk detail column/field akan di tulis di google sheet

%[https://docs.google.com/spreadsheets/d/1PVOIm1WH-33m05A7__g6NO5kSq94jEubNuWvsmDg8AU/edit#gid=0] 

## Pemilihan Http Router

karena di mulai dengan membuat form pendaftan, maka hal yang pertama saya lalukan adalah memilih `http` router, tanpa banyak fikir panjang saya memilih http package yang ada go, karena hanya inggin repot saja he he he.

## Dimulai dari test

Untuk pertama saya akan mulai dengan membuat test untuk http handler form create user, code nya sebagai berikut :

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
	res := httptest.NewRecorder()

	createUserHandler := CreateUserHandler{}
	createUserHandler.ServeHTTP(res, req)

	assertHttpStatus(t, res.Code, http.StatusCreated)
}
```

untuk membuat handler login saya mengimplementasi Handler interface yang ada di http package. dan code nya sebagai berikut :

```go
type Login struct{}

func (l Login) ServeHTTP(w http.ResponseWriter, r *http.Request) {

	var payload data.LoginPayload

	dec := json.NewDecoder(r.Body)
	dec.Decode(&payload)

	w.Write([]byte("This is login page"))
}
```

code di atas pasti tidak akan pass pada saat test. tugas selanjutnya adalah membuat test di atas pass. akan di lanjut ke tulisan berikutnya karena sadah malam dan ngantuk.