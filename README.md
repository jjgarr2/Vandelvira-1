
________________________________________
Technical Report of the Robot
WRO Future Engineers 2026 – Spain, Jaén
Our team has designed and developed an autonomous car to participate in the Future Engineers category of WRO 2026 in Spain, Jaén. The main objective of the project is to build a vehicle capable of moving autonomously, detecting obstacles, and making decisions about the direction it should take during the course.
To achieve this, the robot uses a combination of sensors, actuators, and control systems programmed with an Arduino board. The vehicle includes a HUSKYLENS camera, ultrasonic sensors, a drive motor, a servomotor for steering, an encoder to improve motion control, and a lithium battery as its power source.
________________________________________
1. General Robot Design
The robot is built on a chassis designed by our team and manufactured using 3D printing. We also designed and printed different supports to correctly place the sensors, camera, battery, Arduino board, and other electronic components.
The movement system consists of a motor connected to the wheels, allowing the car to move forward and backward. The steering system is controlled by a servomotor, which changes the turning angle of the front wheels.
The robot is powered by a lithium battery, chosen because of its good balance between weight, capacity, and efficiency. This battery provides stable power to the system while reducing the total weight of the vehicle compared with other types of batteries.
In addition, an encoder has been added to the system. The encoder allows the robot to measure the rotation of the wheels or motor. Thanks to this component, the robot can obtain more precise information about the distance travelled, speed, and real movement of the vehicle. This is especially useful for improving turn accuracy, controlling the trajectory, and correcting errors during the course.
________________________________________
2. Main Components Used
The main components of the robot are:
●	Arduino board as the control unit.
●	DC motor for movement.
●	L298N motor driver.
●	Servomotor for steering.
●	Ultrasonic sensors for obstacle detection.
●	HUSKYLENS camera for detecting colours or objects.
●	Encoder to measure the movement of the motor or wheels.
●	Lithium battery to power the system.
●	Chassis and supports designed and manufactured using 3D printing.
________________________________________
3. Development of the Movement System
The first step of the project was to control the motor using the L298N driver. This module allows us to control both the direction of rotation and the speed of the motor using digital signals and a PWM signal.
The first code developed was used to check that the motor could move forward, stop, and move backwards correctly.
// Define the pins connected to the L298N
const int IN1 = 8;
const int IN2 = 9;
const int EN = 10; // PWM pin for speed control

void setup() {
  // Configure the pins as outputs
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(EN, OUTPUT);

  // Start with the motor stopped
  analogWrite(EN, 0);
}

void loop() {
  // Move forward at medium speed
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  analogWrite(EN, 128); // PWM value from 0 to 255
  delay(2000);

  // Stop the motor
  analogWrite(EN, 0);
  delay(1000);

  // Move backwards at medium speed
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  analogWrite(EN, 128);
  delay(2000);

  // Stop the motor
  analogWrite(EN, 0);
  delay(1000);
}
This code allowed us to check that the traction system worked correctly before integrating it with the other components.
________________________________________
4. Ultrasonic Sensor Control
The second step was to control the ultrasonic sensors. These sensors allow the robot to measure the distance between the vehicle and an obstacle. To do this, the sensor emits an ultrasonic signal and measures the time it takes for the echo to return.
This system is useful for detecting obstacles in front of the vehicle and deciding when it should stop or change direction.
// Define pins for the ultrasonic sensor
const int trigPin = 9;
const int echoPin = 10;

void setup() {
  Serial.begin(9600);

  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
}

void loop() {
  long duration;
  int distance;

  // Clear the trigger pin
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);

  // Send a 10-microsecond pulse
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // Read the time it takes to receive the echo
  duration = pulseIn(echoPin, HIGH);

  // Calculate the distance in centimetres
  distance = duration * 0.034 / 2;

  // Display the distance on the serial monitor
  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  delay(500);
}
During this phase, we confirmed that the sensor was able to measure approximate distances and detect nearby objects.
________________________________________
5. Use of the HUSKYLENS Camera
The third step was learning how to use the HUSKYLENS camera, an artificial vision camera that can recognise colours, objects, or lines depending on the selected working mode.
In our project, the camera is used to detect visual references on the course. This information helps the robot decide whether it should turn to one side or the other.
#include <HUSKYLENS.h>
#include <Wire.h>

HUSKYLENS huskylens;

void setup() {
  Serial.begin(9600);

  // Start communication with HUSKYLENS using I2C
  Wire.begin();

  while (!huskylens.begin(Wire)) {
    Serial.println("Error starting HUSKYLENS");
    delay(100);
  }

  Serial.println("HUSKYLENS started correctly");
}

void loop() {
  if (huskylens.request()) {
    if (huskylens.available()) {
      HUSKYLENSResult result = huskylens.read();

      Serial.print("Object detected - ID: ");
      Serial.print(result.ID);
      Serial.print(" - X: ");
      Serial.print(result.xCenter);
      Serial.print(" - Y: ");
      Serial.println(result.yCenter);
    }
  }

  delay(100);
}
This test allowed us to obtain information about the objects or colours detected by the camera and display their coordinates on the serial monitor.
________________________________________
6. Steering Control with a Servomotor
The fourth step consisted of controlling the servomotor, which is responsible for changing the direction of the car. This component allows the front wheels to turn at different angles.
#include <Servo.h>

Servo steeringServo;

void setup() {
  steeringServo.attach(9);
}

void loop() {
  // Turn left
  steeringServo.write(45);
  delay(1000);

  // Centre steering
  steeringServo.write(90);
  delay(1000);

  // Turn right
  steeringServo.write(135);
  delay(1000);
}
The servomotor is essential for allowing the robot to modify its trajectory during the course.
________________________________________
7. Encoder Integration
To improve the precision of the movement, an encoder has been added to the system. The encoder measures the number of rotations or pulses generated by the motor or wheels.
Thanks to the encoder, the robot can control the distance travelled and the speed more accurately. This allows the car to avoid depending only on the activation time of the motor and instead move based on real movement data.
The encoder will be especially useful for:
●	Measuring the distance travelled.
●	Controlling the motor speed.
●	Improving turn precision.
●	Detecting possible loss of traction.
●	Correcting deviations during the course.
A basic example of encoder reading is shown below:
const int encoderPin = 2;
volatile int pulses = 0;

void countPulses() {
  pulses++;
}

void setup() {
  Serial.begin(9600);

  pinMode(encoderPin, INPUT);
  attachInterrupt(digitalPinToInterrupt(encoderPin), countPulses, RISING);
}

void loop() {
  Serial.print("Encoder pulses: ");
  Serial.println(pulses);

  delay(500);
}
This code counts the pulses generated by the encoder and displays them on the serial monitor. Later, these values can be used to calculate speed or distance travelled.
________________________________________
8. Integration of All Components
After testing each component separately, the next step was to integrate all the systems into a single program. This has been one of the most complex parts of the project, because all components must work together in a coordinated way.
The robot must be able to move forward, measure distances, detect obstacles, interpret camera information, control the steering with the servomotor, and use the encoder to improve motion control.
The first integrated code is still being tested and presents some problems. However, it has helped us check the general logic of the system.
#include <Servo.h>
#include <HUSKYLENS.h>
#include <Wire.h>

HUSKYLENS huskylens;

// Pins for the motor with L298N
const int motorPin1 = 2;   // IN1
const int motorPin2 = 3;   // IN2
const int enablePin = 5;   // ENA - PWM pin

// Pins for the ultrasonic sensor
const int trigPin = 9;
const int echoPin = 10;

// Pin for the servomotor
const int servoPin = 6;
Servo steeringServo;

// Encoder pin
const int encoderPin = 7;
volatile int encoderPulses = 0;

// Variables
long duration;
int distance;

void countPulses() {
  encoderPulses++;
}

void setup() {
  Serial.begin(9600);
  Wire.begin();

  // Motor pin configuration
  pinMode(motorPin1, OUTPUT);
  pinMode(motorPin2, OUTPUT);
  pinMode(enablePin, OUTPUT);

  // Ultrasonic sensor configuration
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  // Encoder configuration
  pinMode(encoderPin, INPUT);
  attachInterrupt(digitalPinToInterrupt(encoderPin), countPulses, RISING);

  // Servomotor configuration
  steeringServo.attach(servoPin);
  steeringServo.write(90); // Centred steering

  // Start HUSKYLENS
  while (!huskylens.begin(Wire)) {
    Serial.println("Error starting HUSKYLENS");
    delay(100);
  }

  Serial.println("System started correctly");

  moveForward();
}

void loop() {
  distance = readUltrasonicDistance();

  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.print(" cm | Encoder pulses: ");
  Serial.println(encoderPulses);

  if (distance > 0 && distance < 10) {
    stopMotor();

    int colourID = detectColour();

    if (colourID == 1) {
      turnLeft();
    } else if (colourID == 2) {
      turnRight();
    } else {
      steeringServo.write(90);
    }

    delay(1000);
    moveForward();
  } else {
    moveForward();
  }

  delay(100);
}

int readUltrasonicDistance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);

  int distanceCm = duration * 0.034 / 2;
  return distanceCm;
}

int detectColour() {
  if (huskylens.request()) {
    if (huskylens.available()) {
      HUSKYLENSResult result = huskylens.read();
      return result.ID;
    }
  }

  return 0;
}

void moveForward() {
  digitalWrite(motorPin1, HIGH);
  digitalWrite(motorPin2, LOW);
  analogWrite(enablePin, 180);
}

void stopMotor() {
  analogWrite(enablePin, 0);
}

void turnLeft() {
  steeringServo.write(45);
}

void turnRight() {
  steeringServo.write(135);
}
________________________________________
9. Current State of the Project
At the moment, the team has already managed to control the main components of the robot separately: the motor, ultrasonic sensors, HUSKYLENS camera, servomotor, and encoder.
The biggest challenge is integrating all the systems into a single stable program. The first integrated code still causes some issues, especially with sensor communication and the decision-making process of the robot.
Even so, the project is progressing correctly. Each test helps us better understand how the components work and allows us to improve both the mechanical design and the programming.
________________________________________
10. Planned Improvements
The next improvements we plan to make are:
●	Correctly integrate all sensors into a single code.
●	Improve the reading of the HUSKYLENS camera.
●	Use the encoder to calculate the distance travelled.
●	Adjust the motor speed depending on the situation.
●	Improve the accuracy of the turns using the servomotor.
●	Optimise the position of the sensors on the chassis.
●	Carry out more tests on the track.
●	Improve autonomy and stability by using the lithium battery efficiently.
________________________________________
11. Conclusion
The development of this robot for WRO Future Engineers 2026 – Spain, Jaén has allowed us to learn about mechanical design, 3D printing, electronics, programming, and autonomous control.
Although the project is still in the integration and improvement phase, we have already managed to control the main components of the vehicle individually. The incorporation of the HUSKYLENS camera, ultrasonic sensors, servomotor, encoder, and lithium battery gives the robot a solid base for further development.
Our goal is to continue improving the system until we achieve a more precise, stable, and autonomous car capable of completing the challenges proposed in the competition.

