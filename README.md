#include <Servo.h>

// Sensor pins
const int moisturePin = A0;
const int metalSensorPin = 2;

// Servo objects
Servo lidRotator;
Servo bioLid;
Servo microbeDispenser;

// Servo pins
const int rotatorPin = 9;
const int bioLidPin = 10;
const int microbePin = 11;

// Thresholds
const int moistureThreshold = 500; // Adjust based on your sensor
const int metalDetected = HIGH;

void setup() {
  Serial.begin(9600);

  // Attach servos
  lidRotator.attach(rotatorPin);
  bioLid.attach(bioLidPin);
  microbeDispenser.attach(microbePin);

  // Setup pins
  pinMode(metalSensorPin, INPUT);

  // Initial positions
  lidRotator.write(90); // Neutral
  bioLid.write(0);       // Closed
  microbeDispenser.write(0); // Idle
}

void loop() {
  int moisture = analogRead(moisturePin);
  int metal = digitalRead(metalSensorPin);

  Serial.print("Moisture: ");
  Serial.print(moisture);
  Serial.print("  Metal: ");
  Serial.println(metal);

  delay(1000); // debounce

  if (moisture > moistureThreshold) {
    sortToBio();
  } else if (metal == HIGH) {
    sortToEwaste();
  } else {
    sortToDryWaste();
  }

  delay(5000); // Wait before next reading
}

void sortToBio() {
  Serial.println("Sorting to BIO-WASTE");

  lidRotator.write(0); // Rotate to bio chamber
  delay(1000);

  bioLid.write(90); // Open bio lid
  delay(1000);

  microbeDispenser.write(90); // Sprinkle microbes
  delay(500);
  microbeDispenser.write(0);  // Reset dispenser

  delay(500);
  bioLid.write(0); // Close bio lid
  lidRotator.write(90); // Return to center
}

void sortToEwaste() {
  Serial.println("Sorting to E-WASTE");

  lidRotator.write(45); // Rotate to e-waste chamber
  delay(2000);
  lidRotator.write(90); // Back to center
}

void sortToDryWaste() {
  Serial.println("Sorting to DRY RECYCLABLES");

  lidRotator.write(135); // Rotate to dry recyclables
  delay(2000);
  lidRotator.write(90); // Back to center
}
