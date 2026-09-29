# Object-Counting-on-Conveyor-Using-Ultrasonic-Sensors-
An Arduino-based object counting system designed for conveyor-belt applications. An HC-SR04 ultrasonic sensor detects objects passing through a defined sensing area, while an I2C 16×2 LCD displays the real-time object count. The project demonstrates low-cost sensing and automated counting for industrial applications.

Project Simulation Link: https://www.tinkercad.com/things/2JevoIxZrK2-object-counting-
#include <LiquidCrystal.h>

#define trigPin 2
#define echoPin 4

// Initialize the LCD screen (adjust the pins accordingly)
LiquidCrystal lcd(12, 11, 10, 9, 8, 7);

int counter = 0;
int currentState = 0;
int previousState = 0;

void setup() {
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  lcd.begin(16, 2);  // Initialize LCD
  lcd.setCursor(0, 0);
  lcd.print("Object Counter");
  lcd.setCursor(0, 1);
  lcd.print("Count: 0");
  delay(1000);  // Wait a second before starting to read
}

void loop() {
  long duration, distance;

  // Send pulse to the ultrasonic sensor
  digitalWrite(trigPin, LOW); 
  delayMicroseconds(2); 
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10); 
  digitalWrite(trigPin, LOW);
  
  // Measure the pulse duration
  duration = pulseIn(echoPin, HIGH);
  distance = (duration / 2) / 29.1;  // Calculate distance in cm

  // Check if object is within 10 cm range
  if (distance <= 100) {  
    currentState = 1;  // Object detected
  } else {
    currentState = 0;  // No object detected
  }

  // Only increment the counter if an object passes through (state changes)
  if (currentState == 1 && previousState == 0) {
    counter++;  // Increment counter when object is detected
    lcd.setCursor(0, 1);  // Move to the second row
    lcd.print("Count: ");  // Label for the counter
    lcd.print(counter);  // Display updated count on LCD
    delay(500);  // Debounce delay to prevent multiple counts for one object
  }

  previousState = currentState;  // Store current state for next loop

  delay(50);  // Short delay to stabilize the sensor
}
