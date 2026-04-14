2.5.4 Pertanyaan Praktikum

1. Gambarkan rangkaian schematic yang digunakan pada percobaan!
2. Apa yang terjadi jika nilai num lebih dari 15?
3. Apakah program ini menggunakan common cathode atau common anode? Jelaskan alasanya!
4. Modifikasi program agar tampilan berjalan dari F ke 0 dan berikan penjelasan disetiap baris kode nya dalam bentuk README.md!

Jawab :
1. <img width="516" height="675" alt="image" src="https://github.com/user-attachments/assets/ffad7aa5-b870-428b-bedc-e7fab5e6dda7" />
2. Program akan mengalami kesalahan karena di dalam kode hanya tersedia pola lampu untuk 16 karakter, yaitu dari angka 0 hingga huruf F. Ketika Arduino mencoba membaca data ke-16 atau lebih, sistem akan mengambil data yang tidak valid dari memori (acak). Hal ini menyebabkan tampilan pada Seven Segment menjadi tidak teratur, bahkan berpotensi membuat mikrokontroler mengalami crash.
3. Program ini pakai tipe Common Anode (Anoda Umum) karena pada tipe ini, lampu LED baru mau menyala kalau dikasih listrik 0 Volt (atau LOW). Didalam kode, ada tanda seru (!) di perintah !digitPattern[num][i]. Tanda seru ini tugasnya membalikkan perintah: yang tadinya 1 (menyala) diubah jadi 0 (LOW) supaya lampunya benar-benar menyala sesuai tipe Common Anode.
4.
```
#include <Arduino.h>

const int segmentPins[8] = {7, 6, 5, 11, 10, 8, 9, 4};

// Daftar pola untuk angka 0 sampai F
byte digitPattern[16][8] = {
  {1,1,1,1,1,1,0,0}, // 0
  {0,1,1,0,0,0,0,0}, // 1
  {1,1,0,1,1,0,1,0}, // 2
  {1,1,1,1,0,0,1,0}, // 3 
  {0,1,1,0,0,1,1,0}, // 4
  {1,0,1,1,0,1,1,0}, // 5
  {1,0,1,1,1,1,1,0}, // 6
  {1,1,1,0,0,0,0,0}, // 7
  {1,1,1,1,1,1,1,0}, // 8
  {1,1,1,1,0,1,1,0}, // 9
  {1,1,1,0,1,1,1,0}, // A
  {0,0,1,1,1,1,1,0}, // b
  {1,0,0,1,1,1,0,0}, // C
  {0,1,1,1,1,0,1,0}, // d
  {1,0,0,1,1,1,1,0}, // E
  {1,0,0,0,1,1,1,0}  // F
};

void displayDigit(int num) {
  for(int i=0; i<8; i++) { 
    // Pakai tanda '!' supaya nyala untuk Common Anode
    digitalWrite(segmentPins[i], !digitPattern[num][i]); 
  }
}

void setup() {
  for(int i=0; i<8; i++) {
    pinMode(segmentPins[i], OUTPUT);
  }
}

void loop() {
  // MODIFIKASI: Mulai dari angka 15, dan terus dikurangi (i--) sampai angka 0
  for(int i=15; i>=0; i--) { 
    displayDigit(i); 
    delay(1000); // Tunggu 1 detik biar angkanya kelihatan jelas
  }
}
```

2.6.4 Pertanyaan Praktikum
1. Gambarkan rangkaian schematic yang digunakan pada percobaan!
2. Mengapa pada push button digunakan mode INPUT_PULLUP pada Arduino Uno?
Apa keuntungannya dibandingkan rangkaian biasa?
3. Jika salah satu LED segmen tidak menyala, apa saja kemungkinan penyebabnya dari
sisi hardware maupun software?
4. Modifikasi rangkaian dan program dengan dua push button yang berfungsi sebagai
penambahan (increment) dan pengurangan (decrement) pada sistem counter dan
berikan penjelasan disetiap baris kode nya dalam bentuk README.md!

Jawab :
1. <img width="495" height="659" alt="image" src="https://github.com/user-attachments/assets/58463ccb-2b5c-40c1-a078-cefc8e2e05ea" />
2. Mode INPUT_PULLUP digunakan karena Arduino telah memiliki resistor internal bawaan. Penggunaan mode ini membuat rangkaian menjadi lebih sederhana karena tidak memerlukan resistor tambahan pada breadboard. Selain itu, mode INPUT_PULLUP juga membantu menjaga kestabilan sinyal listrik agar tidak mudah terpengaruh gangguan dari luar, sehingga pembacaan tombol menjadi lebih akurat.
3. Permasalahan dapat disebabkan oleh faktor perangkat keras (hardware) maupun perangkat lunak (software). Dari sisi hardware, kemungkinan terjadi karena kabel yang kendor atau putus, resistor yang rusak, atau LED pada segmen tertentu sudah tidak berfungsi. Sementara itu, dari sisi software, kesalahan dapat terjadi akibat penulisan urutan pin yang tidak tepat, tidak mengatur pin sebagai OUTPUT, atau kekeliruan dalam penyusunan pola data (nilai 1 dan 0).
4. 
```
#include <Arduino.h>

// 7-Segment Common Anode
// Pin mapping segment: a b c d e f g dp
const int segmentPins[8] = {7, 6, 5, 11, 10, 8, 9, 4};

// Pin button
const int btnUp = 2;
const int btnDown = 3;

int currentNumber = 0;

// Pola digit
byte digitPattern[16][8] = {
  {1,1,1,1,1,1,0,0}, //0
  {0,1,1,0,0,0,0,0}, //1
  {1,1,0,1,1,0,1,0}, //2
  {1,1,1,1,0,0,1,0}, //3 
  {0,1,1,0,0,1,1,0}, //4
  {1,0,1,1,0,1,1,0}, //5
  {1,0,1,1,1,1,1,0}, //6
  {1,1,1,0,0,0,0,0}, //7
  {1,1,1,1,1,1,1,0}, //8
  {1,1,1,1,0,1,1,0}, //9
  {1,1,1,0,1,1,1,0}, //A
  {0,0,1,1,1,1,1,0}, //b
  {1,0,0,1,1,1,0,0}, //C
  {0,1,1,1,1,0,1,0}, //d
  {1,0,0,1,1,1,1,0}, //E
  {1,0,0,0,1,1,1,0}  //F
};

// Fungsi tampil digit (dibalik untuk CA)
void displayDigit(int num)
{
  for(int i=0; i<8; i++)
  {
    digitalWrite(segmentPins[i], !digitPattern[num][i]);
  }
}

void setup()
{
  // Set segment sebagai output
  for(int i=0; i<8; i++)
  {
