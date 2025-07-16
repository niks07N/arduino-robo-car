# arduino-robo-car
An autonomous robotic car built using Arduino that can detect and avoid obstacles.

#include <Servo.h>

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

  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);

  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);


  scanServo.attach(servoPin);
  scanServo.write(90); 
}

void loop() {
  distance = getDistance();

  if (distance < 15) {
    stopMotors();
    delay(300);

    reverse(); 
    delay(500);

    stopMotors(); 

    scanServo.write(150);
    delay(500);
    int rightDist = getDistance();

    scanServo.write(30);
    delay(500);
    int leftDist = getDistance();

    scanServo.write(90);
    delay(200);

    if (leftDist > rightDist) {
      turnLeft();
    } else {
      turnRight();
    }

    delay(800);
  } else {
    moveForward();
  }
}

void moveForward() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);

}

void reverse() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);

}

void turnLeft() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
}

void turnRight() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
}

void stopMotors() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);

}

int getDistance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  return duration * 0.034 / 2;
}
