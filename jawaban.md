
# JAWABAN UTS PRAKTIK NETWORK SECURITY

## Identitas Mahasiswa

- NIM: 24552011342
- Kasus: 9

### Perhitungan Kasus
Jumlah digit NIM:
2 + 4 + 5 + 5 + 2 + 0 + 1 + 1 + 3 + 4 + 2 = 29
Hasil perhitungan:
29 mod 10 = 9
Dengan demikian, kasus yang dikerjakan adalah Kasus 9.
---

## Tugas 1 – Docker dan Volume Persistence
### Hasil Praktik
File `laporan-k9.txt` berhasil disimpan menggunakan Docker Volume bernama `vol-k9`. File tetap tersedia setelah container yang menggunakannya dihapus.
Pengujian komunikasi antarkontainer juga berhasil. Container attacker dapat mengakses layanan web pada container target dan menerima halaman HTML Nginx.
### Penjelasan
Docker Volume digunakan untuk menyimpan data di luar sistem file sementara container. Oleh karena itu, data tetap tersedia meskipun container dihapus dan dapat digunakan kembali oleh container lain.

### Bukti
- Tugas1_01_Volume.png
- Tugas1_03_Uji_Persistensi.png
- Tugas1_06_Uji_Komunikasi.png

---

## Tugas 2 – Kriptografi

### Hasil Praktik
Enkripsi dilakukan menggunakan AES-256-CBC dengan salt dan PBKDF2. Kata sandi yang digunakan adalah `kunci-k9-2026`.
Hash SHA-256 file asli dan file hasil dekripsi menunjukkan nilai yang sama:
`0ea8b72db74392b5f782e5a126350aa5acebf370b389b7478faa6340672ec2ef`
Sertifikat digital dibuat menggunakan RSA 2048-bit dengan informasi:
- Subject: `CN=k9.lab.local`
- Issuer: `CN=k9.lab.local`
- Masa berlaku: 20 November 2026 – 20 Desember 2026

### Penjelasan
AES-256-CBC merupakan algoritma enkripsi simetris yang menggunakan kunci 256-bit. PBKDF2 digunakan untuk menghasilkan kunci dari kata sandi dengan proses derivasi berulang.
SHA-256 digunakan untuk memeriksa integritas file. Kesamaan hash menunjukkan bahwa isi file hasil dekripsi sesuai dengan file asli.
Sertifikat yang dibuat merupakan self-signed certificate karena Subject dan Issuer memiliki identitas yang sama. Artinya, sertifikat ditandatangani sendiri dan bukan oleh Certificate Authority (CA) tepercaya.

### Bukti
- Tugas2_01_Hash_SHA256_Asli.png
- Tugas2_02_Enkripsi_AES256.png
- Tugas2_03_Dekripsi_dan_Hash.png
- Tugas2_04_Sertifikat_Digital.png

---

## Tugas 3 – ARP Spoofing dan Mitigasi

### Hasil Praktik
Pengujian dilakukan pada jaringan Docker terisolasi `lab-arp-k9`.
Alamat IP yang digunakan:
- Korban: `172.19.0.2`
- Gateway: `172.19.0.3`
- Penyerang: `172.19.0.4`
MAC address gateway sebelum spoofing:
`6e:43:24:5e:2f:70`
Saat simulasi ARP spoofing, pemetaan IP gateway berubah menjadi MAC address penyerang:
`56:8f:ee:a8:9a:49`
Setelah mitigasi menggunakan static ARP, pemetaan gateway dikembalikan ke MAC address sebenarnya dan menjadi entri permanen.

### Penjelasan
ARP digunakan untuk menemukan MAC address berdasarkan alamat IP dalam jaringan lokal.
ARP spoofing terjadi ketika penyerang mengirimkan informasi ARP palsu sehingga perangkat korban mengaitkan IP tertentu dengan MAC address milik penyerang.
Mitigasi dilakukan dengan menggunakan static ARP agar pemetaan alamat IP dan MAC address gateway tidak mudah berubah akibat pesan ARP palsu.

### Bukti
- Tugas3_01_ARP_Sebelum.png
- Tugas3_02_ARP_Setelah_Spoofing.png
- Tugas3_03_ARP_Static_Mitigation.png

---

## Tugas 4 – Analisis WPA2 Handshake

### Hasil Praktik
Analisis dilakukan menggunakan `aircrack-ng` terhadap file `samples/handshake-k9.cap` dan wordlist yang disediakan.
Hasil pemeriksaan menunjukkan bahwa file capture tidak dikenali sebagai capture Wi-Fi 802.11 yang valid. Aircrack-ng menampilkan pesan bahwa file bukan capture 802.11 reguler. Pemeriksaan menggunakan tcpdump juga menunjukkan link type Ethernet (EN10MB).
Oleh karena itu, proses analisis handshake WPA2 tidak dapat dilanjutkan. Hasil ini tidak dapat dinyatakan sebagai password ditemukan maupun `KEY NOT FOUND`, karena file capture yang diberikan tidak sesuai dengan format yang diperlukan.

### Penjelasan 4-Way Handshake
1. Message 1: Access Point mengirimkan ANonce kepada client.
2. Message 2: Client mengirimkan SNonce dan MIC. Informasi tersebut digunakan untuk memperoleh PTK.
3. Message 3: Access Point mengirimkan informasi GTK yang dilindungi serta MIC kepada client.
4. Message 4: Client mengirimkan konfirmasi bahwa proses handshake telah selesai.
Proses tersebut digunakan untuk membentuk dan mengonfirmasi kunci sesi antara Access Point dan client.

### Bukti
- Tugas4_01_Aircrack_Error.png
- Tugas4_02_Capture_Invalid.png

---

## Tugas 5 – ICMP dengan Scapy dan Analisis Smurf

### Hasil Praktik
Pengujian ICMP dilakukan menggunakan Scapy pada jaringan Docker terisolasi.
Paket Echo Request dikirim dari `172.19.0.5` menuju `172.19.0.2`.
Hasil pengujian menunjukkan:
- TTL: `64`
- ICMP Type: `0` (Echo Reply)

### Penjelasan
ICMP Echo Request digunakan untuk menguji keterjangkauan suatu host, sedangkan Echo Reply merupakan balasan dari host tujuan.
Serangan Smurf secara konsep memanfaatkan alamat sumber yang dipalsukan menjadi alamat korban dan tujuan berupa alamat broadcast. Jika banyak host memberikan balasan, korban dapat menerima banyak paket sekaligus.
Simulasi ini hanya menjelaskan konsep Smurf dan tidak menjalankan serangan terhadap jaringan nyata.

### Bukti
- Tugas5_01_Pengujian_ICMP_Scapy.png

---

## Tugas 6 – SYN Flood dan TCP Three-Way Handshake

### Hasil Praktik
Pengujian dilakukan pada jaringan Docker internal yang terisolasi.
Jumlah koneksi berstatus SYN_RECV:
- Sebelum pengujian: `0`
- Saat pengujian: `1`
Hasil tcpdump menangkap paket SYN (`[S]`), SYN-ACK (`[S.]`), dan ACK (`[.]`). Pengujian layanan web juga menghasilkan respons HTTP `200 OK`.

### Penjelasan
TCP menggunakan proses three-way handshake untuk membangun koneksi:
1. SYN: Client meminta untuk memulai koneksi.
2. SYN-ACK: Server menerima permintaan dan mengirimkan konfirmasi.
3. ACK: Client mengonfirmasi balasan server sehingga koneksi terbentuk.
SYN flood merupakan serangan yang mengirim banyak permintaan SYN sehingga server dapat dipenuhi koneksi setengah terbuka dan kehabisan sumber daya.
Jumlah SYN_RECV dapat berubah sesuai waktu pengamatan dan kondisi jaringan. Pada pengujian ini, jumlah yang teramati meningkat dari 0 menjadi 1.

### Bukti
- Tugas6_01_Sebelum_SYN_Flood.png
- Tugas6_02_Saat_SYN_Flood.png
- Tugas6_03_TCP_Three_Way_Handshake.png

---

## Tugas 7 – Simulasi DNS Exfiltration

### Hasil Praktik
NIM dikonversi menjadi hexadecimal:
`3234353532303131333432`
Nilai tersebut digunakan sebagai label subdomain:
`3234353532303131333432.k9.exfil.lab`
Server DNS memberikan respons `NOERROR` dengan alamat `10.0.0.99`. Log DNS memperlihatkan query dari alamat `172.20.0.2`.
Analisis mendeteksi label dengan panjang 22 karakter sebagai pola yang mencurigakan.
Setelah nilai hexadecimal didekodekan, hasilnya kembali menjadi:
`24552011342`

### Penjelasan
DNS exfiltration merupakan teknik penyalahgunaan query DNS untuk membawa data melalui nama domain atau subdomain.
Dalam simulasi ini, NIM diubah menjadi hexadecimal lalu ditempatkan pada label subdomain. Log DNS diperiksa untuk mengidentifikasi pola label yang panjang dan mencurigakan.
Hasil decoding menunjukkan bahwa data yang dikirim sesuai dengan NIM. Pengujian dilakukan pada jaringan Docker internal yang terisolasi, bukan pada domain atau jaringan eksternal.

### Bukti
- Tugas7_01_Query_DNS.png
- Tugas7_02_Log_DNS.png
- Tugas7_03_Analisis_DNS.png
- Tugas7_04_Hasil_Decode_NIM.png

---

## Kesimpulan
Berdasarkan praktik UTS Kasus 9, pengujian berhasil dilakukan pada Docker Volume, kriptografi, ARP spoofing dan mitigasinya, ICMP, TCP, serta simulasi DNS exfiltration.
Setiap praktik dilakukan pada lingkungan laboratorium yang disiapkan untuk pembelajaran.
Khusus Tugas 4, file capture yang tersedia tidak valid sebagai capture Wi-Fi 802.11 sehingga analisis WPA2 handshake tidak dapat dilakukan. Kendala tersebut dicatat sesuai hasil pemeriksaan dan tidak dibuat kesimpulan yang tidak didukung oleh bukti.
Secara keseluruhan, praktik memberikan pemahaman mengenai cara kerja layanan jaringan, mekanisme keamanan, contoh serangan, serta beberapa langkah mitigasinya.
