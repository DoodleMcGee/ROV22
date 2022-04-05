# ROV22
Movement code for the WPCP ROV, 21-22

Takes two joystick input, with the left joystick controlling vertical motion and yaw and the right joystick controlling all horizontal motion. The two joystick buttons control roll motion (press down on the right to roll right, vice versa). This is intended to work with two of the standard 3-output arduino joysticks, and has built in compensation for manufacturing tolerances.

If not using a T100 x drive and T200 verticals, remember to change T100 and T200 max pulse at the beginning of the code. The max pulse should be the pulse width that achieves the same thrust as the maximum reverse pulse width (usually 1100). This is necessary because the thrusters are generally stronger forward than back, but the x-drive assumes they are equally strong both ways, so we limit the forward thrust to match the backward.

When using with this year's controller, keep hands off of the joysticks and reset the code before each use using the black button in the center (this will record and re-center the joysticks to calibrate the 0-input). When the red light comes on, controls are live.

motor diagram:

  3        1
   \      /
5 ----  ---- 6
   /      \
  2        4
  
1-4 are T100 x-drive
5 and 6 are T200 verticals

The functionality relies heavily on vector mathematics, if this is completely new to you, it might help to watch a few khan academy videos and come back.

Basic functionality-

Setup: Record centered position (no input)

Loop: 

INPUT
  Get inputs from the joystick. Map inputs from -1 to 1, with the recorded center mapped to 0 (uneven mapping function called mapWithC, or map with center). Record button inputs with digitalread function.
  
X-DRIVE MOTION:
  Using the right joystick inputs, calculate the angle of horizontal motion (with 0 being 45 degrees to the right because the x-drive is rotated by 45 degrees) - this value is stored as the direction
  Using the right joystick inputs, calculate the distance from center on a scale from 0 to 1 (1 being pushed all the way to the side) - this value is stored as the speed modifier
  Because of the 45% rotation of the x-drive, the thrust (on a scale from -1 to 1) for each motor can be easily calculated, with motors 1 and 2 each being sin(direction) and motors 3 and 4 each being cos(direction).
  The x-axis of the left joystick is used for yaw control (rotation around the vertical axis). This input is mapped from -1 to 1 and stored as the rotation modifier. The rotation component of the thrust is then calculated for motors 1-4, with the rotation component for motors 2 and 3 being equal to the modifer and the rotation component for 1 and 4 being equal to the negative of the modifier (because with clockwise rotation these motors should be thrusting backwards)
  The horizontal component for each motor is multiplied by the speed modifier, and then added to the rotation modifier, resulting in a final thrust value for each one.
 
 VERTICAL MOTION
  The vertical motors are much simpler. The vertical axis of the left joystick is mapped -1 to 1 and this value is used directly as the vertical component for the two thrusters. The button input is interpreted and, if one is pressed, the motors are assigned a roll component of + or - 0.5, appropriate to the direction of roll. This value is added to the vertical component (but maxed out at 1 and -1), and these are stored as the final thrust values.

COMMANDS
  The values in the thrust[] array are then mapped to the min and max of each type of motor (with the center at 1500) to form the pulse[] array, which is then written to each motor. 
  
  
  
