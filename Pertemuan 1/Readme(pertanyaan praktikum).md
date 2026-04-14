1.5.4 Pertanyaan Praktikum:
1. Pada kondisi apa program masuk ke blok if?
2. Pada kondisi apa program masuk ke blok else?
3. Apa fungsi dari perintah delay(timeDelay)?
4. Jika program yang dibuat memiliki alur mati → lambat → cepat → reset (mati),
ubah menjadi LED tidak langsung reset → tetapi berubah dari cepat → sedang →
mati dan berikan penjelasan disetiap baris kode nya dalam bentuk README.md!
jawaban :
1. Program masuk ke blok if saat nilai timeDelay <= 100.
2. Program masuk ke blok else saat timeDelay > 100.
3. Fungsi delay(timeDelay) adalah untuk:
  -Mengatur lama waktu LED menyala dan mati
  -Menentukan kecepatan kedipan LED
4. (terdapat di Readme Source Code1.md)


1.6.4 Pertanyaan Praktikum
1. Gambarkan rangkaian schematic 5 LED running yang digunakan pada percobaan!
2. Jelaskan bagaimana program membuat efek LED berjalan dari kiri ke kanan!
3. Jelaskan bagaimana program membuat LED kembali dari kanan ke kiri!
4. Buatkan program agar LED menyala tiga LED kanan dan tiga LED kiri secara bergantian
dan berikan penjelasan disetiap baris kode nya dalam bentuk README.md!
jawaban :
1. Terdapat di -> Readme (Dokumentasi Praktikum).md
2. Efek ini dibuat oleh loop: 
   for (int ledPin = 2; ledPin < 8; ledPin++)
   Cara kerjanya:
   Program menyalakan LED satu per satu dari pin kecil ke besar
   Urutan:
   2 → 3 → 4 → 5 → 6 → 7
   Setiap LED:
   Dinyalakan
   Tunggu (delay)
   Dimatikan
   Hasilnya terlihat seperti LED berjalan ke kanan
3. Dibuat dengan loop kebalikannya:
   for (int ledPin = 7; ledPin >= 2; ledPin--)
   Cara kerja:
   LED dinyalakan dari pin besar ke kecil
   Urutan:
   7 → 6 → 5 → 4 → 3 → 2
   Hasil:
   LED terlihat bergerak balik ke kiri
4. (terdapat di Readme Source Code2.md)
