# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---
<details>
<summary><b>Abous This Project</b></summary>

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
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.
</details>

<details>
<summary><b>Reflections</b></summary>

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

<details>
<summary><b>Reflection Publisher 1</b></summary>

#### Reflection Publisher-1

1. Dalam desain pattern Observer, interface atau trait dalam Rust digunakan untuk memastikan bahwa semua subscriber memiliki metode yang sama untuk menerima pembaruan dari subject (atau observable). Dalam konteks BambangShop, Subscriber hanyalah sebuah struct sederhana yang menyimpan variabel url dan name. Saat ini, Subscriber tidak memiliki metode yang berinteraksi langsung dengan event dari observer pattern. Jadi sekarang, repository hanya menyimpan, menghapus, dan mengambil daftar subscriber, tetapi tidak ada mekanisme update otomatis ketika ada perubahan, atau bahasa database-nya adalah derrived attribute.  

2. > Pertama, Vec adalah struktur data linear, di mana elemen disimpan secara berurutan dalam memori. Ini memiliki beberapa keterbatasan jika digunakan untuk memastikan uniknya id dan url. Misalnya adalah kompleksitas pencarian adalah O(n) untuk mengecek apakah suatu id atau url sudah ada, karena kita perlu mengiterasi seluruh Vec, begitu juga untuk penghapusan. Jadi, Karena Vec bekerja dengan indeks, ini lebih cocok untuk penyimpanan sederhana yang tidak membutuhkan lookup cepat berdasarkan kunci unik.  

   > Oleh karena itu, DashMap diperlukan. DashMap<K, V> adalah thread-safe concurrent HashMap, yang bekerja dengan prinsip key-value store. Dalam konteks uniknya id pada Product dan url pada Subscriber, DashMap memberikan beberapa keuntungan, misalnya kompleksitas pencarian O(1), karena DashMap menggunakan hashing. Lalu penyimpanan dijamin unik, karena bila ada duplikat, yang baru menggantikan yang lama, atau bisa saja ditolak sesuai implementasi.

3. Dalam konteks Rust dan design patterns, ada dua aspek utama yang perlu dipertimbangkan, yaitu keamanan dalam lingkungan multi-threaded dan efisiensi dalam penyimpanan dan akses data. Dan di sini, DashMap adalah implementasi concurrent HashMap, yang memungkinkan banyak thread membaca dan menulis tanpa perlu mengunci seluruh struktur data. Jadi, jika hanya satu thread yang mengakses SUBSCRIBERS, maka Singleton Pattern bisa digunakan dengan Mutex<HashMap>. Tetapi, jika banyak thread membaca dan menulis ke SUBSCRIBERS, DashMap tetap lebih baik karena lebih efisien dan tidak menyebabkan global locking seperti Mutex. Dan di sini, Rust sudah memastikan thread safety dengan compiler-nya, tapi pemilihan struktur data tetap penting untuk performa dan efisiensi.


</details>

<details>
<summary><b>Reflection Publisher 2</b></summary>

#### Reflection Publisher-2

1. Sebenarnya, dalam Model-View-Controller (MVC), Model bertanggung jawab untuk menyimpan data dan juga mengimplementasikan business logic. Namun, dalam praktiknya, banyak aplikasi skala besar memisahkan Model menjadi Service dan Repository untuk meningkatkan struktur, fleksibilitas, dan maintainability. Terlebih lagi, kita sudah memperlajari prinsip S.O.L.I.D., dimana S, yang berarti Single Responsibility Principle, menganjurkan kita untuk membuat satu kelas yang menangani satu fungsi. Bila model menangani semuanya, prinsip ini dilanggar. Dan bila prinsip ini dilanggar,  Open/Closed Principle (OCP) juga jadi sulid dipenuhi, karena kita sulid untuk memperluas kode tanpa mengubah kode lama. Oleh karena itu, kita perlu memisahkan Model menjadi Service dan Repository.

2. Jika kita hanya menggunakan Model tanpa memisahkan Service dan Repository, maka interaksi antara Product, Subscriber, dan Notification akan meningkatkan kompleksitas kode secara drastis. Mari kita bayangkan, jika kita hanya menggunakan Model, maka objek Model tersebut berlaku sebagai God-Object, yaitu objek yang menangani segala hal. Dalam proyek ini, Jika kita hanya menggunakan Model, maka Product, Subscriber, dan Notification harus menangani segala aspek, termasuk penyimpanan data (database query),business logic (validasi, aturan bisnis), dan integrasi API eksternal. Ini jelas bisa menjadi masalah, dimana perubahan kecil bisa saja menyebabkan error di semua aplikasi. Maka, perlu dilakukan pemisahan menjadi Repository dan Service.

3. Ya, saya telah mengeksplorasi lebih lanjut tentang Postman dan menemukan bahwa alat ini sangat membantu dalam menguji API aplikasi apapun yang saya sedang kembangkan. Postman ini membantu saya dalam mengirim request dan melihat respons dengan mudah. Postman juga bisa menguji endpoint dengan data yang berbeda. Jadi, kesimpulannya, Postman adalah alat yang sangat berguna dalam pengembangan API karena mempermudah pengujian, debugging, dan dokumentasi API

</details>

<details>
<summary><b>Reflection Publisher 3</b></summary>

#### Reflection Publisher-3

</details>
</details>