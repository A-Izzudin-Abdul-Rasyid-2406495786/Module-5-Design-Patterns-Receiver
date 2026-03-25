# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
1. In this tutorial, we used RwLock<> to synchronise the use of Vec of Notifications. Explain why
it is necessary for this case, and explain why we do not use Mutex<> instead?

Penggunaan RwLock<Vec<Notification>> diperlukan karena aplikasi Receiver sering mengalami operasi pembacaan data secara simultan (misalnya saat menampilkan daftar notifikasi di banyak tab browser) namun hanya sesekali melakukan penulisan saat notifikasi baru masuk. RwLock memungkinkan banyak thread untuk membaca data secara bersamaan tanpa saling mengunci (multiple readers), yang jauh lebih efisien daripada Mutex yang hanya mengizinkan satu thread saja baik untuk membaca maupun menulis (single access). Jika kita menggunakan Mutex, performa aplikasi akan menurun karena proses pembacaan data yang pasif akan dipaksa mengantre satu per satu, sehingga menghambat responsivitas aplikasi saat menerima banyak permintaan baca sekaligus.

2. In this tutorial, we used lazy_static external library to define Vec and DashMap as a “static”
variable. Compared to Java where we can mutate the content of a static variable via a
static function, why did not Rust allow us to do so?

Rust tidak mengizinkan mutasi variabel statis secara bebas seperti di Java karena Rust mengedepankan prinsip memory safety dan pencegahan data race di tingkat kompilasi. Di Java, mengubah variabel statis dari fungsi statis dalam lingkungan multi-threaded dapat menyebabkan ketidakkonsistenan data jika tidak disinkronisasi secara manual oleh pemrogram. Rust mencegah risiko ini dengan memaksa variabel statis yang dapat diubah (mutable) untuk dibungkus dalam mekanisme sinkronisasi seperti RwLock atau Mutex melalui lazy_static, sehingga kompilator dapat menjamin bahwa hanya satu akses tulis atau banyak akses baca yang terjadi pada satu waktu, memastikan keamanan memori tanpa celah runtime error.

#### Reflection Subscriber-2
1. Have you explored things outside of the steps in the tutorial, for example: src/lib.rs? If not,
explain why you did not do so. If yes, explain things that you have learned from those other
parts of code.

Ya, saya sempat mengeksplorasi bagian di luar langkah tutorial, seperti file src/lib.rs. Dari eksplorasi tersebut, saya belajar bagaimana aplikasi mengelola status dan konfigurasi global, seperti inisialisasi APP_CONFIG (mengambil dari .env) dan REQWEST_CLIENT (klien HTTP untuk melakukan permintaan jaringan) menggunakan lazy_static. Pendekatan ini sangat efisien karena variabel-variabel tersebut dimuat di awal (startup) dan dapat diakses dengan aman oleh berbagai thread dari modul manapun (seperti di NotificationService) tanpa perlu di-pass sebagai parameter secara terus-menerus. Selain itu, file tersebut juga memperlihatkan cara mendefinisikan struktur error handling (custom error) dan respons standar untuk framework Rocket.

2. Since you have completed the tutorial by now and have tried to test your notification system
by spawning multiple instances of Receiver, explain how Observer pattern eases you to plug
in more subscribers. How about spawning more than one instance of Main app, will it still be
easy enough to add to the system?

Pola desain Observer sangat mempermudah penambahan instans Subscriber (Receiver) baru karena menciptakan sistem yang loosely coupled (ketergantungan rendah). Main app (Publisher) tidak perlu mengetahui detail bagaimana Receiver bekerja; ia hanya butuh mendaftarkan URL endpoint dan mengirimkan data melalui HTTP POST. Kita bisa menyalakan puluhan Receiver baru dan sistem akan tetap berjalan lancar. Namun, jika kita menyalakan lebih dari satu instans Main app (Publisher), sistem saat ini tidak akan berjalan dengan baik. Hal ini karena Main app menyimpan daftar subscriber di memori lokal aplikasi menggunakan DashMap. Jika ada dua instans Main app, keduanya akan memiliki memori (daftar subscriber) yang terpisah dan tidak sinkron. Agar bisa menskalakan Main app, kita harus memindahkan penyimpanan DashMap ke sistem basis data terpusat (seperti Redis atau PostgreSQL) yang bisa diakses oleh semua instans Main app.

3. Have you tried to make your own Tests, or enhance documentation on your Postman
collection? If you have tried those features, tell us whether it is useful for your work (it can be
your tutorial work or your Group Project)

Ya, saya telah mencoba mengeksplorasi fitur Tests (skrip otomatis) dan dokumentasi di Postman. Fitur Tests sangat berguna karena memungkinkan saya menulis skrip JavaScript sederhana untuk memvalidasi response API secara otomatis (misalnya, mengecek apakah status code adalah 201 atau apakah body json sesuai), sehingga saya tidak perlu mengeceknya secara manual setiap kali melakukan perubahan kode. Fitur dokumentasi di Postman juga terbukti akan sangat krusial untuk proyek grup (Group Project) nanti, karena fitur ini secara instan menghasilkan halaman web yang menampilkan cara kerja API, parameter yang dibutuhkan, dan contoh responsnya. Ini menjadi "kontrak" komunikasi yang sangat jelas antara tim Backend dan Frontend agar integrasi dapat berjalan mulus tanpa miskomunikasi. Namun saya juga akan menggunakan Bruno karena Bruno terbilang cukup ringan dan menjadi validasi 2 aplikasi sekaligus yaitu postman dan Bruno. Saya akan menggunakan keduanya.

