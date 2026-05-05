Pertanyaan praktikum percobaan 1:
1. Apa fungsi perintah analogRead() pada rangkaian praktikum ini?
2. Mengapa diperlukan fungsi map() dalam program tersebut?
3. Modifikasi program berikut agar servo hanya bergerak dalam rentang 30° hingga 150°, meskipun       potensiometer tetap memiliki rentang ADC 0–1023. Jelaskan program pada file README.md

Jawab :
1. analogRead() dipakai buat membaca nilai dari potensiometer yang berupa sinyal analog, terus diubah jadi angka digital supaya bisa diproses sama Arduino.
2. Fungsi map() dibutuhkan karena nilai dari ADC itu 0–1023, sedangkan servo cuma butuh 0–180 derajat. Jadi map() ini dipakai buat nyesuain nilainya biar servo bisa bergerak sesuai dengan putaran potensiometer.
3.
```
#include <Servo.h> // library untuk servo motor

Servo myservo; // membuat objek servo

// ===================== PIN SETUP =====================
// Tentukan pin yang digunakan untuk potensiometer dan servo
const int potensioPin = A0;   // isi pin analog input (contoh A0)
const int servoPin = 9;      // isi pin digital untuk servo (PWM)

// ===================== VARIABEL =====================
// Variabel untuk menyimpan data ADC dan sudut servo
int pos = 0; // isi dengan tipe data dan inisialisasi awal
int val = 0; // isi dengan tipe data dan inisialisasi awal

void setup() {

  // Hubungkan servo ke pin yang sudah ditentukan
  myservo.attach(servoPin); // isi dengan servoPin

  // Aktifkan komunikasi serial untuk monitoring
  Serial.begin(9600); // isi baud rate (contoh 9600)

}

void loop() {

  // ===================== PEMBACAAN ADC =====================
  // Baca nilai dari potensiometer (rentang 0–1023)
  val = analogRead(potensioPin); // isi dengan potensioPin

  // ===================== KONVERSI DATA =====================
  // Ubah nilai ADC menjadi sudut servo (0–180 derajat)
  pos = map(val,
             0,   // isi nilai minimum ADC
             1023,   // isi nilai maksimum ADC
             30,   // isi sudut minimum servo jadi 30
             150);  // isi sudut maksimum servo jadi 150

  // ===================== OUTPUT SERVO =====================
  // Gerakkan servo sesuai hasil mapping
  myservo.write(pos); // isi dengan variabel sudut

  // ===================== MONITORING DATA =====================
  // Tampilkan data ADC dan sudut servo ke Serial Monitor
  Serial.print("ADC Potensio: ");
  Serial.print(val); // isi variabel ADC

  Serial.print(" | Sudut Servo: ");
  Serial.println(pos); // isi variabel sudut

  // ===================== STABILISASI =====================
  // Delay untuk memberi waktu servo bergerak stabil
  delay(1000); // isi dalam milidetik
}
```
Pada program awal, sudut servo diatur dari 0° sampai 180°. Namun pada modifikasi ini, fungsi map() diubah sehingga outputnya dibatasi hanya dari 30° hingga 150°. Jadi walaupun nilai ADC dari potensiometer tetap penuh (0–1023), servo tidak akan bergerak sampai ke posisi paling ujung. Pembatasan ini bertujuan supaya gerakan servo lebih aman dan tidak terlalu terbebani, sehingga bisa mengurangi risiko kerusakan pada bagian mekaniknya.


Pertanyaan praktikum percobaan 2:
1. Jelaskan mengapa LED dapat diatur kecerahannya menggunakan fungsi analogWrite()!
2. Apa hubungan antara nilai ADC (0–1023) dan nilai PWM (0–255)?
3. Modifikasilah program berikut agar LED hanya menyala pada rentang kecerahan sedang, yaitu hanya    ketika nilai PWM berada pada rentang 50 sampai 200. Jelaskan program pada file README.md.

Jawab :
1. LED bisa diatur tingkat kecerahannya karena analogWrite() menghasilkan sinyal PWM. Cara kerjanya itu dengan mengatur berapa lama sinyal dalam kondisi HIGH dan LOW. Kalau sinyal HIGH lebih lama, daya yang masuk ke LED juga lebih besar jadi terlihat lebih terang. Sebaliknya, kalau durasi HIGH lebih sedikit, LED jadi lebih redup.
2. Hubungannya itu searah (linier). Jadi kalau nilai ADC makin besar, nilai PWM juga ikut naik. Dampaknya, LED akan makin terang. Sebaliknya, kalau nilai ADC kecil, PWM juga kecil dan LED jadi lebih redup.
3.
```
#include <Arduino.h> // library dasar Arduino (tidak wajib diubah)

// ===================== PIN SETUP =====================
// Tentukan pin yang digunakan untuk potensiometer dan LED PWM
const int potPin = A0;   // isi dengan pin analog (contoh A0)
const int ledPin = 9;   // isi dengan pin digital PWM (contoh 9)

// ===================== VARIABEL =====================
// Variabel untuk menyimpan hasil pembacaan dan konversi PWM
int nilaiADC = 0;  // isi dengan nilai awal (default 0)
int pwm = 0;       // isi dengan nilai awal (default 0)

void setup() {

  // ===================== OUTPUT SETUP =====================
  // Atur pin LED sebagai output
  pinMode(ledPin, 9);

  // ===================== SERIAL MONITOR =====================
  // Aktifkan komunikasi serial untuk melihat data pembacaan
  Serial.begin(9600); // isi baud rate (contoh 9600)
}

void loop() {

  // ===================== PEMBACAAN SENSOR =====================
  // Baca nilai analog dari potensiometer (rentang 0–1023)
  nilaiADC = analogRead(potPin); // isi dengan potPin

  // ===================== PEMROSESAN DATA (SCALING) =====================
  // Ubah nilai ADC (0–1023) menjadi nilai PWM (0–255)
  pwm = map(nilaiADC,
            0,   // isi nilai minimum ADC
            1023,   // isi nilai maksimum ADC
            50,   // isi PWM minimum
            200);  // isi PWM maksimum

  // ===================== OUTPUT PWM =====================
  // Kirim sinyal PWM ke LED (mengatur kecerahan)
  analogWrite(ledPin, pwm); // isi dengan variabel PWM

  // ===================== MONITORING DATA =====================
  // Tampilkan data ADC dan PWM ke Serial Monitor
  Serial.print("ADC: ");
  Serial.print(nilaiADC); // isi variabel ADC

  Serial.print(" | PWM: ");
  Serial.println(pwm); // isi variabel PWM

  // ===================== STABILISASI SISTEM =====================
  // Delay untuk menstabilkan pembacaan dan tampilan data
  delay(100); // isi dalam milidetik (contoh 50)
}
```
Pada program awal, nilai ADC diubah ke rentang PWM 0–255, sehingga LED bisa dalam kondisi mati total atau sangat terang. Namun pada modifikasi ini, fungsi map() disesuaikan menjadi rentang 50 hingga 200. Akibatnya, LED hanya menyala pada tingkat kecerahan tertentu, tidak sampai benar-benar mati ataupun terlalu terang. Pembatasan ini dilakukan agar perubahan cahaya lebih stabil, enak dilihat, dan tidak terlalu ekstrem.
