# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [✅] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [✅] Commit: `Create Subscriber model struct.`
    -   [✅] Commit: `Create Notification model struct.`
    -   [✅] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [✅] Commit: `Implement add function in Subscriber repository.`
    -   [✅] Commit: `Implement list_all function in Subscriber repository.`
    -   [✅] Commit: `Implement delete function in Subscriber repository.`
    -   [✅] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [✅] Commit: `Create Notification service struct skeleton.`
    -   [✅] Commit: `Implement subscribe function in Notification service.`
    -   [✅] Commit: `Implement subscribe function in Notification controller.`
    -   [✅] Commit: `Implement unsubscribe function in Notification service.`
    -   [✅] Commit: `Implement unsubscribe function in Notification controller.`
    -   [✅] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [✅] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [✅] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [✅] Commit: `Implement publish function in Program service and Program controller.`
    -   [✅] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [✅] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. Dalam case program BambangShop, menggunakan satu Model struct saja sudah cukup jika aplikasi hanya
memiliki satu cara atau satu jenis Subscriber. Namun, jika ke depannya Bambangshop memeiliki berbagai
tipe notifikasi seperti EmailSubscriber, PushNotificationSubscriber, dan WebhookSubscriber, sebuah 
interface atau trait in rust sangat diperulkan supaya program dapat menjalankan apa yang diperintahkan
tanpa mengetahui detail cara kerja dari masing-masing notifikasi.

2. Karena id in Program dan url in Subscriber bersifat unik, menggunakan Dashmap lebih baik daripada
menggunakan Vec (list). Jika menggunakan Vec, setiap kali ada penambahkan subscriber baru dan harus
mengecek apakah URL sudah ada agar tidak duplikat, menghapus, atau mencari subscriber tertentu, program
harus mengecek elemen satu per satu dari awal. Seiring bertambahnya jumlah data, ini akan menjadi sangat
lambat.

3. Kita tetap membutuhkan DashMap dan tidak bisa sekadar menggantinya dengan implementasi Singleton
Pattern.Singleton pattern hanya berfungsi untuk memastikan bahwa aplikasi memiliki satu instance variabel
global yang sama, namun pola ini sama sekali tidak menangani masalah thread safety. Karena compiler Rust
sangat ketat dalam mencegah data race, penerapan Singleton yang berisi data yang mutable akan langsung
ditolak saat kompilasi jika hanya menggunakan HashMap standar. Oleh karena itu, kita wajib tetap
menggunakan DashMap di dalam Singleton tersebut agar berbagai proses penambahan dan penghapusan
subscriber dapat berjalan bersamaan dengan aman tanpa menyebabkan error pada memori aplikasi.

#### Reflection Publisher-2
1. Kita perlu memisahkan Service dan Repository dari Model agar SRP atau Single Responsibility Principle
terpenuhi. Adanya pemisahan tersebut akan mempermudah kita karena menghilangkan fat model yang dapat
membuat kita pusing membaca sebuah file code yang panjang berisi segala program dan membuat testability
menjadi lebih mudah

2. Jika kita hanya menggunakan Model tanpa memisahkan lapisan Service dan Repository, program kita akan
menerapkan anti-pattern yang disebut Fat Model atau God Object. Kompleksitas kode akan melonjak karena
setiap entitas dipaksa mengambil alih tugas di luar fungsinya seperti, model Program harus mengatur akses
database dan merakit objek notifikasi, Subscriber dibebani eksekusi HTTP request, dan Notification harus
mengurus format data atau logging alih-alih sekadar menjadi wadah pesan murni (DTO). Akibat penggabungan
ini, interaksi antar model menjadi sangat saling bergantung (tightly coupled), rapuh terhadap perubahan,
tidak bisa diuji secara terisolasi, dan akhirnya membengkak menjadi spaghetti code. Oleh karena itu,
pemisahan arsitektur menjadi lapisan Model, Service, dan Repository sangat krusial sebagai pembatas
tegas agar setiap komponen hanya fokus mengerjakan satu tugas spesifiknya dengan rapi.

3. Saya saat ini baru explore postman hanya sebagian kecil dari aplikasi namun adanya postman ini sangat
membantu saya dalam mengerjakan BambangShop ini karena memungkinkan saya untuk berinteraksi langsung
dengan server tanpa perlu membuat antarmuka pengguna terlebih dahulu. Melalui Postman, saya dapat
melakukan simulasi request yang presisi pada endpoint seperti POST /notification/subscribe, menguji
pengiriman payload JSON yang berisi URL dan nama, serta memvalidasi balasan server secara instan melalui
indikator seperti status code 201 Created. Lebih jauh lagi, untuk Group Project atau future software
engineering projects, fitur-fitur Postman seperti Collections dan Workspaces sangat membantu dalam
merangkum semua skenario API menjadi "dokumentasi hidup" yang mudah dibagikan ke seluruh anggota tim.
Selain itu, fitur Environment Variables menyederhanakan transisi pengujian antar lingkungan server,
sementara Mock Servers sangat menyelamatkan alur kerja tim karena memungkinkan pengembang frontend
langsung bekerja menggunakan respons tiruan tanpa harus menunggu backend selesai 100%. Terakhir, fitur
Automated Testing juga mempermudah proses penjaminan mutu dengan menjalankan skrip otomatis untuk
memastikan stabilitas status dan format respons API secara berkelanjutan. 

#### Reflection Publisher-3
1. pada tutorial case, saya menggunakan variasi push model

2. Jika BambangShop menggunakan variasi Pull Model, keuntungannya terletak pada fleksibilitas, keamanan,
dan efisiensi ukuran payload awal. Subscriber memiliki kendali penuh untuk hanya menarik informasi yang 
benar-benar mereka butuhkan, sehingga tidak terbebani oleh penerimaan data JSON yang besar atau tidak
relevan dari server. Selain itu, keamanan data lebih terjamin karena detail produk tidak langsung
disebarkan secara terbuka melalui jaringan; subscriber harus secara proaktif melakukan request ke API 
BambangShop untuk mendapatkan data spesifik tersebut. Dari sisi server BambangShop, pendekatan ini
meringankan beban komputasi dan bandwidth awal karena server cukup memancarkan sinyal notifikasi singkat
tanpa harus merakit dan mengirimkan seluruh detail produk ke ribuan subscriber sekaligus.

Di sisi lain, kerugian dari Pull Model adalah tingginya risiko inefisiensi jaringan dan menurunnya 
tingkat kecepatan informasi real-time. Ketika server mengirimkan sinyal pembaruan, ribuan subscriber
bisa saja merespons secara serentak dengan mengirimkan request tarikan data ke server di detik yang sama,
yang berpotensi memicu lonjakan trafik mendadak (bottleneck atau thundering herd) hingga membuat server
kewalahan. Selain itu, karena adanya langkah tambahan di mana subscriber harus meminta data secara
terpisah setelah menerima sinyal, penyampaian informasi menjadi tertunda dan tidak seinstan Push Model.
Secara teknis, implementasinya juga jauh lebih rumit bagi kedua belah pihak; developer BambangShop harus
menyiapkan dan merawat endpoint API tambahan khusus untuk melayani tarikan data, sementara subscriber
harus menulis logika ekstra untuk melakukan request susulan tersebut.

3. Jika kita tidak menggunakan multi-threading, program akan mengeksekusi pengiriman notifikasi secara
sekuensial atau satu per satu, yang langsung memicu masalah performa. Karena pengiriman request HTTP POST
melalui jaringan memakan waktu, antrean proses ini akan memblokir program utama dan sistem harus
menunggu respons dari subscriber pertama secara utuh sebelum bisa mengirim pesan ke subscriber
selanjutnya. Akibatnya, admin yang memicu aksi tersebut akan terjebak menatap layar loading yang panjang,
dan aplikasi akan kehilangan skalabilitasnya. Bahkan, jika hanya ada satu URL subscriber yang bermasalah
atau mati, ribuan antrean subscriber di belakangnya akan ikut tertunda menunggu proses timeout, yang
pada akhirnya dapat melumpuhkan keseluruhan sistem saat jumlah pengguna semakin masif.