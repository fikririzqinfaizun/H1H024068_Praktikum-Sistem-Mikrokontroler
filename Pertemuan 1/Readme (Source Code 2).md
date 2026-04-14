cpp
int timer = 200;

// daftar pin LED
int ledKiri[] = {2, 3, 4};
int ledKanan[] = {5, 6, 7};

void setup() {
  // mengatur semua pin LED menjadi OUTPUT
  for (int i = 2; i <= 7; i++) {
    pinMode(i, OUTPUT);
  }
}

void loop() {

  // Nyalakan LED kiri dan matikan LED kanan
  for (int i = 0; i < 3; i++) {
    digitalWrite(ledKiri[i], HIGH);
    digitalWrite(ledKanan[i], LOW);
  }

  delay(timer);

  // Nyalakan LED kanan dan matikan LED kiri
  for (int i = 0; i < 3; i++) {
    digitalWrite(ledKanan[i], HIGH);
    digitalWrite(ledKiri[i], LOW);
  }

  delay(timer);
}
