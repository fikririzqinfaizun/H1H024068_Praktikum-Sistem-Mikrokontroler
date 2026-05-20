**6.5.4 Pertanyaan Praktikum**
1. Jelaskan proses bagaimana tombol dapat mengubah kondisi LED menggunakan
interrupt!
2. Apa fungsi attachInterrupt() pada program tersebut?
3. Mengapa pada ISR tidak disarankan menggunakan delay() dan Serial.print()?
4. Apa fungsi keyword volatile pada variabel ledState?
5. Pada percobaan digunakan mode interrupt FALLING. Modifikasikan program
menggunakan mode interrupt lain (RISING, CHANGE, atau LOW) kemudian:
• Jelaskan perbedaan cara kerja masing-masing mode interrupt tersebut
• Analisis perubahan perilaku LED yang terjadi pada setiap mode
• Sertakan source code dan penjelasan program dalam bentuk README.md

Jawab :
1. Saat push button ditekan, kondisi sinyal pada pin 2 Arduino berubah dari HIGH menjadi LOW karena konfigurasi yang digunakan adalah INPUT_PULLUP. Perubahan dari HIGH ke LOW tersebut memicu interrupt dengan mode FALLING, sehingga Arduino segera mengeksekusi ISR tombolInterrupt(). Pada bagian ISR, nilai variabel ledState dibalik menggunakan operator toggle (!), sehingga status LED berubah, baik dari menyala menjadi mati maupun sebaliknya. Setelah proses ISR selesai dijalankan, program kembali ke fungsi loop() dan kondisi LED diperbarui berdasarkan nilai terbaru dari ledState.
2. Fungsi attachInterrupt() dipakai untuk mengaitkan pin interrupt dengan ISR (Interrupt Service Routine) tertentu. Dalam program ini, attachInterrupt() menghubungkan pin 2 ke fungsi tombolInterrupt() dengan mode FALLING, sehingga ISR akan otomatis dieksekusi setiap kali tombol ditekan dan sinyal berubah dari HIGH ke LOW.
3. Hal tersebut terjadi karena saat ISR sedang dijalankan, program utama akan dihentikan sementara. Jika menggunakan delay(), proses sistem dapat tertahan lebih lama, sedangkan Serial.print() memerlukan komunikasi serial yang cukup lambat untuk diproses. Akibatnya, interrupt lain bisa mengalami keterlambatan penanganan dan respons sistem menjadi kurang optimal.
4. 
