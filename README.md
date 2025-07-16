# arduino-robo-car
An autonomous robotic car built using Arduino that can detect and avoid obstacles.
#include <Servo.h>

// Motor driver direction pins only
#define IN1 7
#define IN2 6
#define IN3 5
#define IN4 4

// Ultrasonic sensor pins
#define trigPin 9
#define echoPin 8

// Servo motor pin
#define servoPin 3


Servo scanServo;
long duration;
int distance;

void setup() {
  Serial.begin(9600);

  // Motor direction pins
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);

  // Ultrasonic sensor
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  // Buzzer and LEDs
  pinMode(buzzerPin, OUTPUT);
  pinMode(led1Pin, OUTPUT);
  pinMode(led2Pin, OUTPUT);

  // Servo
  scanServo.attach(servoPin);
  scanServo.write(90); // Center position
}

void loop() {
  distance = getDistance();

  if (distance < 15) {
    stopMotors();
    delay(300);

    reverse(); // Reversing with buzzer and LEDs
    delay(500);

    stopMotors(); // Stop again before scanning

    // Scan right
    scanServo.write(150);
    delay(500);
    int rightDist = getDistance();

    // Scan left
    scanServo.write(30);
    delay(500);
    int leftDist = getDistance();

    // Return to center
    scanServo.write(90);
    delay(200);

    if (leftDist > rightDist) {
      turnLeft();
    } else {
      turnRight();
    }

    delay(800); // Time to complete turn
  } else {
    moveForward();
  }
}

// Function to move forward
void moveForward() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);

}

// Function to reverse
void reverse() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);

}

// Turn left
void turnLeft() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
}

// Turn right
void turnRight() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
}

// Stop all motors and indicators
void stopMotors() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);

}

// Get distance from ultrasonic sensor
int getDistance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  return duration * 0.034 / 2; // Convert to centimeters
}
