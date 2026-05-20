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
4. Keyword volatile digunakan supaya compiler selalu mengambil nilai terbaru dari variabel ledState, karena nilainya dapat berubah kapan saja di dalam ISR. Jika tidak menggunakan volatile, compiler bisa menganggap nilai variabel tetap sama sehingga jalannya program berpotensi tidak sesuai dengan yang diharapkan.
5. modifikasi :
   
   a. rising :
 ```
  #include <Arduino.h>

volatile bool ledState = false;

// ISR
void tombolInterrupt() {
  ledState = !ledState;
}

void setup() {
  pinMode(13, OUTPUT);
  pinMode(2, INPUT_PULLUP);

  attachInterrupt(
    digitalPinToInterrupt(2),
    tombolInterrupt,
    RISING
  );
}

void loop() {
  digitalWrite(13, ledState);
}
  ```

Cara Kerja:
Interrupt akan aktif ketika sinyal berubah dari LOW ke HIGH. Pada rangkaian dengan INPUT_PULLUP, kondisi ini terjadi saat tombol dilepas.
Perilaku LED:
LED berubah kondisi ketika tombol dilepas, bukan saat tombol ditekan. Setiap tombol dilepas, ISR akan dijalankan dan status LED akan di-toggle.


  b. change :
```
  #include <Arduino.h>

volatile bool ledState = false;

void tombolInterrupt() {
  ledState = !ledState;
}

void setup() {
  pinMode(13, OUTPUT);
  pinMode(2, INPUT_PULLUP);

  attachInterrupt(
    digitalPinToInterrupt(2),
    tombolInterrupt,
    CHANGE
  );
}

void loop() {
  digitalWrite(13, ledState);
}
   ```
Cara Kerja:
Interrupt akan dipicu setiap kali terjadi perubahan kondisi sinyal, baik saat berubah dari HIGH ke LOW maupun dari LOW ke HIGH.
Perilaku LED:
Kondisi LED dapat berubah sebanyak dua kali, yaitu ketika tombol ditekan dan saat tombol dilepaskan. Akibatnya, LED bisa tampak berkedip lebih cepat karena ISR dijalankan pada kedua perubahan sinyal tersebut.

  c. low :
 ```
  #include <Arduino.h>

volatile bool ledState = false;

void tombolInterrupt() {
  ledState = !ledState;
}

void setup() {
  pinMode(13, OUTPUT);
  pinMode(2, INPUT_PULLUP);

  attachInterrupt(
    digitalPinToInterrupt(2),
    tombolInterrupt,
    LOW
  );
}

void loop() {
  digitalWrite(13, ledState);
}
  ```
Cara Kerja:
Interrupt akan tetap berjalan selama pin berada dalam kondisi LOW. Pada konfigurasi INPUT_PULLUP, kondisi LOW muncul ketika tombol ditekan.
Perilaku LED:
Ketika tombol terus ditekan, ISR akan dipanggil secara berulang sehingga kondisi LED berubah sangat cepat. Akibatnya, LED terlihat tidak stabil atau terus berkedip karena interrupt tetap aktif selama sinyal LOW masih terbaca.

----------------------------------------------------------------------------------------------------

**6.6.4 Pertanyaan Praktikum**
1. Jelaskan bagaimana fungsi millis() bekerja pada program tersebut!
2. Apa perbedaan utama antara delay() dan millis()?
3. Mengapa metode millis() disebut non-blocking?
4. Modifikasi program agar:
• LED pertama berkedip setiap 1 detik
• LED kedua berkedip setiap 500 ms
• Tanpa menggunakan delay()
Berikan penjelasan setiap baris program dalam bentuk README.md

Jawab :
1. Fungsi millis() digunakan untuk memperoleh waktu sejak Arduino mulai menyala dalam satuan milidetik. Pada program, nilai currentMillis dibandingkan dengan previousMillis untuk mengecek apakah interval waktu yang ditentukan sudah terpenuhi. Jika selisih waktunya telah mencapai 1000 ms, maka status LED akan diubah.
2. delay() akan menghentikan seluruh proses program selama durasi tertentu sehingga program tidak bisa menjalankan proses lainnya pada saat itu. Sementara itu, millis() bekerja secara non-blocking karena hanya mengecek perbedaan waktu tanpa menghentikan eksekusi program utama.
3. Hal tersebut karena metode millis() tidak menghentikan jalannya program. Saat menunggu interval waktu tertentu, program masih bisa menjalankan proses lain secara bersamaan sehingga sistem menjadi lebih responsif serta lebih efisien.
4. Modifikasi :
```
#include <Arduino.h> 

// ===================== LED 1 =====================

// Menyimpan waktu terakhir LED 1 berubah kondisi
unsigned long previousMillis1 = 0;

// Interval kedip LED 1 = 1000 ms (1 detik)
const long interval1 = 1000;

// Menyimpan status LED 1 (ON/OFF)
bool ledState1 = false;


// ===================== LED 2 =====================

// Menyimpan waktu terakhir LED 2 berubah kondisi
unsigned long previousMillis2 = 0;

// Interval kedip LED 2 = 500 ms
const long interval2 = 500;

// Menyimpan status LED 2 (ON/OFF)
bool ledState2 = false;


// ===================== SETUP =====================

void setup() {

  // Pin 13 digunakan sebagai output untuk LED 1
  pinMode(13, OUTPUT);

  // Pin 12 digunakan sebagai output untuk LED 2
  pinMode(12, OUTPUT);
}


// ===================== LOOP =====================

void loop() {

  // Mengambil waktu saat ini sejak Arduino menyala
  unsigned long currentMillis = millis();


  // ===================== LED 1 =====================

  // Mengecek apakah selisih waktu sudah mencapai 1 detik
  if (currentMillis - previousMillis1 >= interval1) {

    // Menyimpan waktu terakhir LED berubah kondisi
    previousMillis1 = currentMillis;

    // Mengubah status LED (ON menjadi OFF / OFF menjadi ON)
    ledState1 = !ledState1;

    // Menuliskan status LED ke pin 13
    digitalWrite(13, ledState1);
  }


  // ===================== LED 2 =====================

  // Mengecek apakah selisih waktu sudah mencapai 500 ms
  if (currentMillis - previousMillis2 >= interval2) {

    // Menyimpan waktu terakhir LED berubah kondisi
    previousMillis2 = currentMillis;

    // Mengubah status LED (ON menjadi OFF / OFF menjadi ON)
    ledState2 = !ledState2;

    // Menuliskan status LED ke pin 12
    digitalWrite(12, ledState2);
  }
}
```
