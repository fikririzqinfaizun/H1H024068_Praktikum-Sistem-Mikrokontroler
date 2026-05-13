5.5.4 Pertanyaan Praktikum
1. Apakah ketiga task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!
2. Bagaimana cara menambahkan task keempat? Jelaskan langkahnya!
3. Modifikasilah program dengan menambah sensor (misalnya potensiometer), lalu
gunakan nilainya untuk mengontrol kecepatan LED! Bagaimana hasilnya? Jelaskan
program pada file README.md.

Jawab :
1. Ketiga task dieksekusi secara bergantian dalam waktu yang sangat singkat dengan bantuan scheduler pada FreeRTOS. Scheduler bertugas mengatur pembagian waktu proses agar setiap task memperoleh kesempatan untuk dijalankan secara teratur. Saat sebuah task menjalankan fungsi vTaskDelay(), task tersebut akan berada pada kondisi blocked, sehingga scheduler akan mengalihkan eksekusi ke task lain yang siap berjalan. Karena proses pergantian task berlangsung sangat cepat, sistem terlihat seperti menjalankan beberapa task secara bersamaan
2. Task keempat dapat ditambahkan dengan membuat fungsi task baru terlebih dahulu, misalnya TaskBlink3(). Setelah itu, task didaftarkan pada fungsi setup() menggunakan xTaskCreate(). Setiap task yang ditambahkan dapat dikonfigurasi dengan parameter yang berbeda, seperti nilai delay, tingkat prioritas, maupun fungsi yang dijalankan, sesuai dengan kebutuhan sistem.
3. Modifikasi :
   Modifikasi:
```
#include <Arduino_FreeRTOS.h>

void TaskBlink1(void *pvParameters);
void TaskBlink2(void *pvParameters);
void Taskprint(void *pvParameters);

const int led1 = 8;
const int led2 = 7;
const int potPin = A0;

int nilaiPot = 0;
int delayLed = 200;

void setup() {

  Serial.begin(9600);

  xTaskCreate(
    TaskBlink1,
    "task1",
    128,
    NULL,
    1,
    NULL
  );

  xTaskCreate(
    TaskBlink2,
    "task2",
    128,
    NULL,
    1,
    NULL
  );

  xTaskCreate(
    Taskprint,
    "task3",
    128,
    NULL,
    1,
    NULL
  );

  vTaskStartScheduler();
}

void loop() {

}

void TaskBlink1(void *pvParameters) {

  pinMode(led1, OUTPUT);

  while (1) {

    nilaiPot = analogRead(potPin);

    delayLed = map(nilaiPot, 0, 1023, 100, 1000);

    digitalWrite(led1, HIGH);
    vTaskDelay(delayLed / portTICK_PERIOD_MS);

    digitalWrite(led1, LOW);
    vTaskDelay(delayLed / portTICK_PERIOD_MS);
  }
}

void TaskBlink2(void *pvParameters) {

  pinMode(led2, OUTPUT);

  while (1) {

    digitalWrite(led2, HIGH);
    vTaskDelay((delayLed + 100) / portTICK_PERIOD_MS);

    digitalWrite(led2, LOW);
    vTaskDelay((delayLed + 100) / portTICK_PERIOD_MS);
  }
}

void Taskprint(void *pvParameters) {

  int counter = 0;

  while (1) {

    counter++;

    Serial.print("Counter: ");
    Serial.print(counter);

    Serial.print(" | Potensiometer: ");
    Serial.print(nilaiPot);

    Serial.print(" | Delay LED: ");
    Serial.println(delayLed);

    vTaskDelay(500 / portTICK_PERIOD_MS);
  }
}
```
Penjelasan :
Potensiometer berfungsi sebagai input untuk mengatur kecepatan kedipan LED. Nilai analog yang diperoleh melalui fungsi analogRead() selanjutnya dikonversi menggunakan fungsi map() menjadi nilai delay dengan rentang 100 ms hingga 1000 ms. Semakin tinggi nilai yang dihasilkan potensiometer, maka delay akan semakin besar sehingga kedipan LED menjadi lebih lambat. Sebaliknya, nilai potensiometer yang lebih rendah menghasilkan delay yang lebih kecil sehingga LED berkedip lebih cepat. Program tetap menerapkan FreeRTOS dengan tiga task yang berjalan secara concurrent, yaitu task LED pertama, task LED kedua, serta task untuk menampilkan data pada Serial Monitor. Pengaturan eksekusi setiap task dilakukan oleh scheduler FreeRTOS melalui fungsi vTaskDelay(), sehingga seluruh task dapat dijalankan secara bergantian tanpa saling mengganggu proses masing-masing. Berdasarkan hasil percobaan, perubahan nilai pada potensiometer mampu memengaruhi kecepatan kedipan LED secara real-time, sementara sistem tetap beroperasi dengan stabil meskipun menjalankan beberapa task secara bersamaan.

5.6.4 Pertanyaan Praktikum
1. Apakah kedua task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!
2. Apakah program ini berpotensi mengalami race condition? Jelaskan!
3. Modifikasilah program dengan menggunakan sensor DHT sesungguhnya sehingga
informasi yang ditampilkan dinamis. Bagaimana hasilnya? Jelaskan program pada file
README.md.

Jawab :
1. Kedua task dijalankan secara concurrent dengan bantuan scheduler pada FreeRTOS. Task read_data berfungsi untuk mengirimkan data ke queue, sedangkan task display menerima serta menampilkan data tersebut pada Serial Monitor. Saat salah satu task berada dalam kondisi delay atau menunggu data dari queue, scheduler akan mengalihkan proses eksekusi ke task lainnya yang siap dijalankan. Sehingga dengan mekanisme tersebut, kedua task dapat bekerja secara bergantian secara teratur dan efisien.
2. Program ini memiliki kemungkinan terjadinya race condition yang sangat rendah karena proses pertukaran data dilakukan melalui queue pada FreeRTOS. Queue berperan sebagai mekanisme komunikasi data yang aman antar task, sehingga pengelolaan akses data dikendalikan langsung oleh sistem RTOS. Dengan mekanisme tersebut, data tidak akan diakses secara bersamaan oleh beberapa task pada waktu yang sama, sehingga potensi terjadinya konflik akses data dapat diminimalkan.
3. Modifikasi :
```
#include <Arduino_FreeRTOS.h>
#include <queue.h>
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT11

DHT dht(DHTPIN, DHTTYPE);

struct readings {
  int temp;
  int h;
};

QueueHandle_t my_queue;

void read_data(void *pvParameters);
void display(void *pvParameters);

void setup() {

  Serial.begin(9600);

  dht.begin();

  my_queue = xQueueCreate(1, sizeof(struct readings));

  xTaskCreate(
    read_data,
    "read sensors",
    128,
    NULL,
    0,
    NULL
  );

  xTaskCreate(
    display,
    "display",
    128,
    NULL,
    0,
    NULL
  );
}

void loop() {

}

void read_data(void *pvParameters) {

  struct readings x;

  for (;;) {

    x.temp = dht.readTemperature();
    x.h = dht.readHumidity();

    xQueueSend(my_queue, &x, portMAX_DELAY);

    vTaskDelay(1000 / portTICK_PERIOD_MS);
  }
}

void display(void *pvParameters) {

  struct readings x;

  for (;;) {

    if (xQueueReceive(my_queue, &x, portMAX_DELAY) == pdPASS) {

      Serial.print("Temperature = ");
      Serial.println(x.temp);

      Serial.print("Humidity = ");
      Serial.println(x.h);
    }
  }
}
```
Penjelasan :
Pada modifikasi program ini, sensor DHT11 dimanfaatkan untuk melakukan pembacaan nilai suhu dan kelembapan secara langsung. Program dibagi menjadi dua task, yaitu read_data yang bertugas membaca data dari sensor kemudian mengirimkannya ke queue melalui fungsi xQueueSend(), serta task display yang menerima data menggunakan xQueueReceive() dan menampilkannya pada Serial Monitor. Penerapan queue menjadikan proses komunikasi antar task lebih aman dan terorganisasi. Berdasarkan hasil pengujian, nilai suhu dan kelembapan dapat berubah secara dinamis mengikuti kondisi lingkungan di sekitar. Selain itu, kedua task tetap mampu berjalan secara concurrent dengan pengaturan eksekusi oleh scheduler FreeRTOS.
