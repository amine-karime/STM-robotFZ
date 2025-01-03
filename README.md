# Project Title: Microbit Controlled Robot

## Overview
This project involves using two Microbits to control a robot. One Microbit acts as a controller in your hand, powered by a small 2xAA battery pack. The other Microbit is mounted on the robot and receives signals from the controller to move the robot accordingly. The robot is powered by a 3.7V battery connected to a Microbit shield, which provides the necessary pins and power.

## Components
- 2 Microbits
- 2xAA battery pack for the controller Microbit
- 3.7V battery for the robot Microbit
- Microbit shield
- Breadboard
- Jumper wires
- Motor driver
- 2 TT geared motors
- Small wheels
- 12V battery pack (3x3.7V batteries) for the motor driver
- Servo motor
- Mechanism for catching a ball
- Switches

## How It Works
1. **Controller Microbit**: This Microbit is held in your hand and powered by a 2xAA battery pack. It senses the movement of your hand and sends the X and Y axis data to the robot Microbit via radio signals. Button A controls the servo motor, and Button B moves the robot backward.
2. **Robot Microbit**: This Microbit is mounted on the robot and receives the X and Y axis data from the controller Microbit. It calculates the motor speeds and sends the appropriate signals to the motor driver to control the motors. It also controls a servo motor mechanism at the front of the robot, which is used to catch a ball and move it to a hole.
3. **Motor Driver and Motors**: The motor driver is powered by a 12V battery pack and controls the two TT geared motors based on the signals received from the robot Microbit. The motors drive the wheels to move the robot.
4. **Servo Motor**: The servo motor is controlled by the robot Microbit and is used to operate a mechanism at the front of the robot to catch a ball.

## Code Explanation
### Motor Speed Control
```python
def set_motor_speed():
    global forward_speed, turning_speed, left_speed, right_speed
    forward_speed = Y
    turning_speed = X
    left_speed = max(0, forward_speed + turning_speed)
    right_speed = max(0, forward_speed - turning_speed)
    pins.analog_write_pin(ENABLE_LEFT, min(1023, abs(left_speed)))
    pins.analog_write_pin(ENABLE_RIGHT, min(1023, abs(right_speed)))
```
This function calculates the motor speeds based on the X and Y axis data and ensures no backward motion. It then sets the motor speeds using the 

analog_write_pin

 function.

### Radio Communication
```python
def on_received_value(name, value):
    global X, Y
    if name == "X":
        X = value if abs(value) > DEADZONE else 0
    elif name == "Y":
        Y = value if abs(value) > DEADZONE else 0
    elif name == "Servo":
        pins.servo_write_pin(DigitalPin.P0, 90 if value == 1 else 0)
    elif name == "Pin13":
        pins.digital_write_pin(DigitalPin.P13, 1 if value == 1 else 0)
radio.on_received_value(on_received_value)
```
This function handles the received radio values and updates the global X and Y variables. It also controls a servo motor and a digital pin based on the received values.

### Initialization and Main Loop
```python
right_speed = 0
left_speed = 0
turning_speed = 0
forward_speed = 0
ENABLE_RIGHT = DigitalPin.P2
ENABLE_LEFT = DigitalPin.P1
X = 0
Y = 0
DEADZONE = 200
radio.set_group(1)
basic.show_icon(IconNames.YES)

def on_forever():
    set_motor_speed()
basic.forever(on_forever)

def on_forever2():
    radio.send_value("X", input.acceleration(Dimension.X))
    radio.send_value("Y", input.acceleration(Dimension.Y))
    radio.send_value("Servo", 1 if input.button_is_pressed(Button.A) else 0)
    radio.send_value("Pin13", 1 if input.button_is_pressed(Button.B) else 0)
basic.forever(on_forever2)
```
This section initializes the variables and sets up the radio group. It also defines two main loops: one for setting the motor speed and another for sending the X and Y axis data, as well as button states, to the robot Microbit.

## Assembly Instructions
1. Connect the controller Microbit to the 2xAA battery pack.
2. Mount the robot Microbit on the robot and connect it to the Microbit shield.
3. Connect the shield to the breadboard using jumper wires.
4. Connect the motor driver to the breadboard and power it with the 12V battery pack.
5. Connect the TT geared motors to the motor driver.
6. Attach the small wheels to the motors.
7. Connect the servo motor to the robot Microbit.
8. Ensure all connections are secure and switches are in place.

## Usage
1. Turn on the controller Microbit and the robot Microbit.
2. Move your hand to control the robot's movement.
3. Press button A to control the servo motor and catch a ball.
4. Press button B to move the robot backward.

## Conclusion
This project demonstrates how to use Microbits for wireless control of a robot. By utilizing radio communication, motor control, and a servo motor mechanism, you can create a responsive and interactive robot that moves with your hand gestures and can catch and move a ball.
