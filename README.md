# Module 9 

## a. What is amqp?
AMQP adalah singkatan dari Advanced Message Queuing Protocol. AMQP merupakan protokol standar terbuka pada lapisan aplikasi yang digunakan untuk komunikasi pesan antar sistem. Protokol ini memungkinkan pengiriman pesan secara andal (reliable), terstruktur, dan aman, serta mendukung berbagai pola komunikasi seperti antrean pesan (message queue), publikasi dan langganan (publish/subscribe), serta transaksi. AMQP sering digunakan dalam arsitektur sistem terdistribusi atau mikroservis, dan biasanya diimplementasikan menggunakan broker pesan seperti RabbitMQ.

## b. What does it mean? `guest:guest@localhost:5672` , what is the first guest, and what is the second guest, and what is `localhost:5672` is for?
`guest:guest@localhost:5672` adalah bagian dari URI (Uniform Resource Identifier) yang digunakan untuk menghubungkan aplikasi ke server AMQP, seperti RabbitMQ. Bagian pertama `guest` sebelum tanda titik dua (`:`) adalah nama pengguna (username), sedangkan `guest` setelah tanda titik dua adalah kata sandi (password) yang digunakan untuk otentikasi. Kemudian, `localhost` menunjukkan bahwa server AMQP berjalan di komputer lokal, dan `5672` adalah nomor port standar yang digunakan oleh protokol AMQP untuk koneksi non-SSL. Jadi secara keseluruhan, URI ini berarti aplikasi akan mencoba terhubung ke server AMQP lokal pada port `5672` menggunakan kredensial `guest` sebagai username dan password. Perlu diketahui bahwa secara default, RabbitMQ hanya mengizinkan penggunaan akun `guest` dari `localhost` dan bukan dari komputer lain.

## Simulation slow subscriber

![image](https://github.com/user-attachments/assets/2f9f37c8-e2e3-40d7-99da-a5a9b939dc08)

Queued messages mencapai hingga 15 messages karena cargo run saja jalankan pada console publisher 4 kali dengan cepat. Karena publisher mengirim data 5 sekaligus dan saya mengirim data sebanyak 4 kali, maka dengan adanya `thread::sleep(ten_millis)` maka akan ada (4-1) * 5 data message yang queued.

## Reflection and Running at least three subscribers

![image](https://github.com/user-attachments/assets/3c47bc49-4c69-47de-a76e-df5c8eb6e7ba)

![image](https://github.com/user-attachments/assets/7cd605d6-5b42-4ddf-ac1b-7fac5d580abd)

Grafik pada RabbitMQ tampak seperti itu karena adanya lonjakan pengiriman pesan dari publisher dan proses konsumsi dari beberapa subscriber. Publisher dan subscriber mulai berjalan hampir bersamaan, namun publisher mengirim pesan lebih cepat daripada kecepatan subscriber dalam memprosesnya. Hal ini menyebabkan antrean sempat menumpuk sebelum akhirnya menurun kembali, karena subscriber memproses pesan satu per satu dengan jeda menggunakan `sleep(1000ms)`.

Beberapa hal yang dapat ditingkatkan dari kode publisher dan subscriber antara lain adalah dengan menerapkan method `get_handler_action` untuk mencegah terjadinya panic, serta mengurangi duplikasi kode pada publisher dengan memanfaatkan perulangan (`loop`).
