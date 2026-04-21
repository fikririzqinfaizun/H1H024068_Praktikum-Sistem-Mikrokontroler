Percobaan 3A


iv.Pertanyaan Praktikum
1) Jelaskan proses dari input keyboard hingga LED menyala/mati!
2) Mengapa digunakan Serial.available() sebelum membaca data? Apa yang terjadi jika
baris tersebut dihilangkan?
3) Modifikasi program agar LED berkedip (blink) ketika menerima input '2' dengan kondisi
jika ‘2’ aktif maka LED akan terus berkedip sampai perintah selanjutnya diberikan dan
berikan penjelasan disetiap baris kode nya dalam bentuk README.md!
4) Tentukan apakah menggunakan delay() atau milis()! Jelaskan pengaruhnya terhadap
sistem

Jawab :

1. Alur Proses dari Input Keyboard hingga LED Menyala/Mati
- Input Keyboard:
Pengguna memasukkan karakter ('1' atau '0') melalui keyboard, kemudian menekan tombol Enter menggunakan tampilan Serial Monitor pada komputer.
- Pengiriman Data:
Data yang diketik akan dikirim oleh komputer dalam bentuk komunikasi serial melalui kabel USB menuju IC konverter USB-to-Serial pada Arduino, lalu diteruskan ke pin RX (Receive) pada mikrokontroler.
- Proses Pembacaan oleh Arduino:
Program akan menjalankan fungsi Serial.available() untuk mendeteksi apakah terdapat data yang masuk ke buffer. Jika data tersedia, maka fungsi Serial.read() digunakan untuk mengambil satu karakter dari buffer tersebut.
- Pengolahan Logika:
Karakter yang diterima kemudian diperiksa menggunakan percabangan if atau else if. Apabila nilai yang terbaca adalah '1', maka mikrokontroler akan menjalankan perintah digitalWrite(PIN_LED, HIGH).
- Respon Output (Eksekusi Fisik):
Pin digital 12 akan menghasilkan tegangan HIGH (sekitar 5V), sehingga arus listrik mengalir ke rangkaian LED dan membuat LED menyala. Sebaliknya, jika menerima input '0', maka pin akan berada pada kondisi LOW dan LED akan mati.
2. Serial.available() digunakan untuk memeriksa jumlah byte data yang telah tiba dan tersimpan di dalam buffer penerima serial mikrokontroler.
Jika dihilangkan: Perintah Serial.read() akan terus-menerus dieksekusi oleh fungsi loop() yang berjalan sangat cepat. Jika tidak ada data yang dikirim dari komputer, Serial.read() akan mengembalikan nilai -1. Arduino akan membuang siklus pemrosesan untuk terus mengevaluasi nilai -1 ini dengan kondisi if, yang sangat tidak efisien dan dapat menyebabkan error logika jika program tidak menangani nilai -1 dengan benar.
3. Modifikasi Program :
```
// Mendeklarasikan konstanta pin untuk LED pada pin digital 12 [cite: 121]
const int PIN_LED = 12;
// Variabel 'mode' menyimpan status atau perintah aktif saat ini, default diset '0' (OFF)
char mode = '0';
// Variabel 'lastMode' menyimpan status mode sebelumnya. 
// Diinisialisasi dengan 'x' (nilai sembarang) agar saat program pertama kali jalan, 
// sistem langsung mendeteksi adanya perubahan status.
char lastMode = 'x';

void setup() {
  // Memulai komunikasi serial dengan baud rate 9600 bps agar Arduino bisa bertukar data dengan komputer [cite: 126]
  Serial.begin(9600);
  
  // Mengatur pin digital 12 (PIN_LED) sebagai OUTPUT agar bisa mengirim tegangan ke LED [cite: 128]
  pinMode(PIN_LED, OUTPUT);
  
  // Menampilkan pesan instruksi ke layar Serial Monitor saat program pertama kali dihidupkan [cite: 127]
  Serial.println("Ketik 1 = ON, 0 = OFF, 2 = BLINK");
}

void loop() {
  // Mengecek apakah ada data yang masuk dan tersedia di buffer penerima serial [cite: 132]
  if (Serial.available() > 0) {
    // Membaca 1 karakter (byte) pertama dari data yang dikirim oleh pengguna [cite: 133]
    char data = Serial.read();

    // Memfilter input: Hanya memproses jika karakter yang diketik adalah '1', '0', atau '2'
    if (data == '1' || data == '0' || data == '2') {
      // Menyimpan input valid tersebut sebagai mode operasi yang baru
      mode = data;
    }
  }
  
  // Mengecek apakah mode saat ini berbeda dengan mode sebelumnya
  // Logika ini mencegah Serial Monitor dipenuhi (spam) oleh teks yang sama berulang-ulang
  if (mode != lastMode) {
    
    // Mencetak status ke Serial Monitor sesuai mode yang sedang aktif
    if (mode == '1') Serial.println("LED ON");
    else if (mode == '0') Serial.println("LED OFF");
    else if (mode == '2') Serial.println("LED BLINK");

    // Memperbarui nilai lastMode dengan mode saat ini, 
    // sehingga pesan di atas tidak akan dicetak lagi sampai pengguna mengubah mode.
    lastMode = mode;
  }

  // Mengeksekusi instruksi ke perangkat keras sesuai 'mode' yang sedang disimpan
  if (mode == '1') {
    // Mode ON: Memberikan tegangan HIGH (5V) ke pin LED agar menyala terus [cite: 141]
    digitalWrite(PIN_LED, HIGH);
  }
  else if (mode == '0') {
    // Mode OFF: Memberikan tegangan LOW (0V) ke pin LED agar mati terus [cite: 149]
    digitalWrite(PIN_LED, LOW);
  }
  else if (mode == '2') {
    // Mode BLINK: Membuat LED berkedip secara berulang
    digitalWrite(PIN_LED, HIGH); // Nyalakan LED [cite: 141]
    delay(500);                  // Tahan posisi menyala selama 500 milidetik (0.5 detik)
    digitalWrite(PIN_LED, LOW);  // Matikan LED [cite: 149]
    delay(500);                  // Tahan posisi mati selama 500 milidetik sebelum mengulang loop
  }
}
```
4. Menggunakan millis()

Dampaknya terhadap sistem:
Apabila efek kedipan dibuat menggunakan delay(500), maka mikrokontroler akan berhenti sejenak (seolah “tertidur”) selama 500 ms. Pada waktu tersebut, Arduino tidak dapat langsung merespons atau membaca data baru dari Serial Monitor. Akibatnya, pengguna mungkin perlu menekan tombol seperti ‘0’ beberapa kali hingga tepat saat mikrokontroler kembali aktif agar perintah dapat diproses. Sebaliknya, jika menggunakan millis(), sistem akan bekerja secara asynchronous (non-blocking). Artinya, Arduino tetap dapat mengatur waktu kedipan LED tanpa menghentikan proses lain. Dengan begitu, mikrokontroler tetap siaga dan mampu memeriksa input dari Serial secara terus-menerus dalam interval waktu yang sangat cepat.


Percobaan 3B

3.6.4 Pertanyaan Praktikum
1) Jelaskan bagaimana cara kerja komunikasi I2C antara Arduino dan LCD pada rangkaian
tersebut!
2) Apakah pin potensiometer harus seperti itu? Jelaskan yang terjadi apabila pin kiri dan
pin kanan tertukar!
3) Modifikasi program dengan menggabungkan antara UART dan I2C (keduanya sebagai
output) sehingga:
- Data tidak hanya ditampilkan di LCD tetapi juga di Serial Monitor
- Adapun data yang ditampilkan pada Serial Monitor sesuai dengan table berikut:
ADC: 0 Volt: 0.00 V Persen: 0%
Tampilan jika potensiometer dalam kondisi diputar paling kiri
- ADC: 0 0% | setCursor(0, 0) dan Bar (level) | setCursor(0, 1)
- Berikan penjelasan disetiap baris kode nya dalam bentuk README.md!
4) Lengkapi table berikut berdasarkan pengamatan pada Serial Monitor

Jawab :

1. Protokol I2C bekerja dengan konsep Master-Slave. Pada rangkaian ini, Arduino berperan sebagai Master (pengendali utama), sedangkan LCD berfungsi sebagai Slave (perangkat penerima). Proses komunikasi hanya memanfaatkan dua jalur kabel, yaitu SDA (data) yang terhubung ke pin A4 dan SCL (clock) yang terhubung ke pin A5. Arduino akan memulai komunikasi dengan mengirimkan sinyal clock sekaligus alamat I2C milik LCD, misalnya 0x27 seperti pada inisialisasi LiquidCrystal_I2C lcd(0x27, 16, 2);. Setelah LCD memberikan respons, Arduino akan melanjutkan dengan mengirimkan perintah, seperti proses inisialisasi, mengaktifkan backlight, atau mengatur posisi kursor. Selain itu, Arduino juga dapat mengirimkan data berupa karakter ASCII, misalnya hasil pembacaan sensor, untuk kemudian ditampilkan pada layar LCD.
2. Pin tengah pada potensiometer (wiper) harus dihubungkan ke pin analog Arduino (A0), karena bagian ini yang menghasilkan tegangan berubah-ubah sesuai dengan posisi putaran. Jika posisi pin kiri dan kanan tertukar, rangkaian tetap aman dan tidak mengalami kerusakan. Namun, arah perubahan nilai akan menjadi kebalikan. Misalnya, jika sebelumnya putaran ke kanan (searah jarum jam) membuat nilai ADC meningkat hingga mendekati 1023 (sekitar 5V), maka setelah pin VCC dan GND ditukar, putaran ke arah yang sama justru akan menurunkan nilai ADC mendekati 0 (GND).
3. Modifikasi Program :
```
#include <Wire.h>               // Library untuk komunikasi I2C [cite: 191]
#include <LiquidCrystal_I2C.h>  // Library untuk modul LCD I2C [cite: 192]
#include <Arduino.h>            // Library standar Arduino [cite: 193]

// Inisialisasi LCD pada alamat 0x27 (atau 0x20) dengan ukuran 16 kolom dan 2 baris [cite: 195, 196]
LiquidCrystal_I2C lcd(0x27, 16, 2);

const int pinPot = A0; // Pin analog untuk potensiometer [cite: 198]

void setup() {
  Serial.begin(9600); // Memulai komunikasi serial UART pada baud rate 9600 [cite: 201]

  lcd.init();         // Inisialisasi modul LCD [cite: 205]
  lcd.backlight();    // Menyalakan lampu latar LCD [cite: 207]
}

void loop() {
  // 1. Membaca nilai analog dari potensiometer (0-1023) [cite: 212]
  int nilaiADC = analogRead(pinPot);

  // 2. Kalkulasi Data Tambahan
  // Menghitung tegangan (Volt) dengan rumus (ADC / 1023) * 5V
  float volt = (nilaiADC * 5.0) / 1023.0;
  
  // Mengonversi nilai ADC menjadi persentase (0-100%)
  int persen = map(nilaiADC, 0, 1023, 0, 100);
  
  // Menentukan panjang bar (level) untuk baris kedua LCD (0-16 karakter) [cite: 217]
  int panjangBar = map(nilaiADC, 0, 1023, 0, 16);

  // 3. Tampilan ke Serial Monitor (UART) [cite: 301]
  // Format: ADC: [nilai] | Persen: [persen]% | Volt: [volt] V
  Serial.print("ADC: ");
  Serial.print(nilaiADC);
  Serial.print(" | Persen: ");
  Serial.print(persen);
  Serial.print("% | Volt: ");
  Serial.print(volt);
  Serial.println(" V");

  // 4. Tampilan ke LCD (I2C) [cite: 227-253]
  // Baris 1: Menampilkan nilai ADC dan Persentase
  lcd.setCursor(0, 0); // [cite: 229, 305]
  lcd.print("ADC:");
  lcd.print(nilaiADC);
  lcd.print(" ");
  lcd.print(persen);
  lcd.print("%   "); // Spasi tambahan untuk membersihkan sisa karakter sebelumnya 

  // Baris 2: Menampilkan Bar Level [cite: 237, 305]
  lcd.setCursor(0, 1);
  for (int i = 0; i < 16; i++) {
    if (i < panjangBar) {
      lcd.print((char)255); // Karakter blok penuh untuk level bar [cite: 245]
    } else {
      lcd.print(" ");       // Karakter kosong jika level belum tercapai [cite: 249]
    }
  }

  delay(250); // Delay singkat untuk stabilitas pembacaan dan tampilan [cite: 256]
}
```
4. Tidak akurat, tapi hampir mendekati nilai yang diminta
<img width="756" height="202" alt="image" src="https://github.com/user-attachments/assets/f3b8a65b-7a7f-4506-84fe-a1bfc6313d23" />
