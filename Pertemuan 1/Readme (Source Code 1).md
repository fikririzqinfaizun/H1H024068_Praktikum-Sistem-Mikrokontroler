cpp
const int ledPin = 6;     // Pin LED
int timeDelay = 100;      // Mulai dari cepat

void setup() {
  pinMode(ledPin, OUTPUT);   // Mengatur pin LED sebagai output
}

void loop() {

  // LED menyala
  digitalWrite(ledPin, HIGH);
  delay(timeDelay);

  // LED mati
  digitalWrite(ledPin, LOW);
  delay(timeDelay);

  // Mengecek apakah delay masih kurang dari 500
  if (timeDelay >= 500) {
    // Jika sudah mencapai batas, LED dimatikan dan program berhenti
    digitalWrite(ledPin, LOW);
    while (true) {
      // program berhenti di sini
    }
  } 
  else {
    // Jika masih kurang dari 500, delay ditambah agar kedipan melambat
    timeDelay = timeDelay + 100;
  }
}
