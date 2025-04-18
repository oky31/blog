---
title: "The beauty of linked list"
datePublished: Fri Apr 18 2025 09:00:55 GMT+0000 (Coordinated Universal Time)
cuid: cm9mk7mmm000509k1gcwx6sva
slug: the-beauty-of-linked-list

---

Linked list adalah salah satu struktur data yang pertama aku pelajari setelah array, ketika itu aku sangat bingung dengan cara kerja struktur data ini, tetapi baru-baru ini setelah mempelajari ulang aku melihat sebuah keindahan di dalamnya .

Bayangkan Sebuah data yang memiliki pointer yang terhubung dengan data lain, sehingga menjadi sebuah rantai data yang saling terhubung, ini bisa menjadi solusi dari ketidak dinamis nya sebuah array, linked list membuat kita bisa menyimpan data tanpa ada keterbatasan maksimal panjang data yang akan di simpan

The beauty of linked list, mengutip hasil percakapan Lex Fridman & ThePrimeagen

> Yeah, let’s wax philosophical about a linked list. It is pretty profound. For people who don’t know, a node in a linked list doesn’t know anything about the world it’s in. It only knows about the thing it’s linked to, its neighbor. Maybe that’s symbolic. It’s a metaphor for all of us humans.

Dari kalimat di atas bahkan linked list pun bisa memiliki filosofi, setiap node tidak mengetahui ada node lain mereka hanya terhubung melalui pointer, dan menunggu untuk di akses

Linked list mengekspresikan sebuah solusi dari keterbatasan, menjadi hal yang sangat di dinamis, kita bisa menambah node baru di awal, di akhir dan di tengah tergantung kebutuhan, walaupun operasi paling berat itu memiliki kopleksitas O(n), tetapi menurutku itu tetap efisien.

Penampakan linkedlist

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744966811855/3267b4ce-7454-48e1-bfc0-3b608ae112ce.png align="center")