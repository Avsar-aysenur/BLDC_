//USING_ATMEL328P_BLDC_MOTOR_DRİVER
// pot
#define analogInPin1 A0
// motor setup
#define y_motor_lout  12
#define y_motor_pwm_hout  10
#define b_motor_lout  13
#define b_motor_pwm_hout  11
#define g_motor_lout  8
#define g_motor_pwm_hout  9
//hall input
#define hall_sensor_y  5
#define hall_sensor_b  6
#define hall_sensor_g  7
//button
#define ON_OFF_SWITCH_1  A1 //aç kapa buton
#define ON_OFF_SWITCH_2  A2
#define BUTTON_PIN 4 //ileri geri buton
#define LED 3

int counter = 0;

int SWITCH_STATE_1 = 0;
int SWITCH_STATE_2 = 0;
int buttonState = 0 ;

int sensorValue1 = 0; // value read from the pot
int outputValue1 = 0; // value output to the P

float throttle = 0.0;

bool MotorOff = false;

float Cn = 0;

enum WheelDirection {
DIR_FORWARD,
DIR_BACKWARD,
DIR_STOP
};

// MOTOR DRIVE
void MoveWheel(WheelDirection (dir), float (speed)) {
if (MotorOff) return;

//empty all motor registers
//half bridge driver, hi part is active high
//lo part is active low
analogWrite(y_motor_pwm_hout, 0);//set motor to stop
analogWrite(b_motor_pwm_hout, 0);
analogWrite(g_motor_pwm_hout, 0);

digitalWrite(y_motor_lout, HIGH);
digitalWrite(b_motor_lout, HIGH);
digitalWrite(g_motor_lout, HIGH);

int hall_y = digitalRead(hall_sensor_y);
int hall_b = digitalRead(hall_sensor_b);
int hall_g = digitalRead(hall_sensor_g);

/*Serial.print("\r\n");
Serial.print(hall_y);
Serial.print(hall_b);
Serial.print(hall_g);*/

if (dir == DIR_STOP) {

} else if (dir == DIR_FORWARD) {
if (hall_y == 0 && hall_b == 0 && hall_g == 1) {//001
analogWrite(b_motor_pwm_hout, speed);
digitalWrite(g_motor_lout, LOW);
} else if (hall_y == 1 && hall_b == 0 && hall_g == 1) {//101
analogWrite(b_motor_pwm_hout, speed);
digitalWrite(y_motor_lout, LOW);
} else if (hall_y == 1 && hall_b == 0 && hall_g == 0) {//100
analogWrite(g_motor_pwm_hout, speed);
digitalWrite(y_motor_lout, LOW);
} else if (hall_y == 1 && hall_b == 1 && hall_g == 0) {//110
analogWrite(g_motor_pwm_hout, speed);
digitalWrite(b_motor_lout, LOW);
} else if (hall_y == 0 && hall_b == 1 && hall_g == 0) {//010
analogWrite(y_motor_pwm_hout, speed);
digitalWrite(b_motor_lout, LOW);
} else if (hall_y == 0 && hall_b == 1 && hall_g == 1) {//011
analogWrite(y_motor_pwm_hout, speed);
digitalWrite(g_motor_lout, LOW);
}
delayMicroseconds(10);
} 
else if (dir == DIR_BACKWARD) {
if (hall_y == 0 && hall_b == 0 && hall_g == 1) {//001
analogWrite(g_motor_pwm_hout, speed);
digitalWrite(b_motor_lout, LOW); 
} else if (hall_y == 1 && hall_b == 0 && hall_g == 1) {//101
analogWrite(y_motor_pwm_hout, speed);
digitalWrite(b_motor_lout, LOW);
} else if (hall_y == 1 && hall_b == 0 && hall_g == 0) {//100
analogWrite(y_motor_pwm_hout, speed);
digitalWrite(g_motor_lout, LOW);
} else if (hall_y == 1 && hall_b == 1 && hall_g == 0) {//110
analogWrite(b_motor_pwm_hout, speed);
digitalWrite(g_motor_lout, LOW);
} else if (hall_y == 0 && hall_b == 1 && hall_g == 0) {//010
analogWrite(b_motor_pwm_hout, speed);
digitalWrite(y_motor_lout, LOW);
} else if (hall_y == 0 && hall_b == 1 && hall_g == 1) {//011
analogWrite(g_motor_pwm_hout, speed);
digitalWrite(y_motor_lout, LOW);
}
}
delayMicroseconds(10);
}

void setup()
{
//Serial.begin(9600);
pinMode(BUTTON_PIN,INPUT);
pinMode(y_motor_lout, OUTPUT);
pinMode(y_motor_pwm_hout, OUTPUT);
pinMode(b_motor_lout, OUTPUT);
pinMode(b_motor_pwm_hout, OUTPUT);
pinMode(g_motor_lout, OUTPUT);
pinMode(g_motor_pwm_hout, OUTPUT);
pinMode(LED,OUTPUT);

pinMode(hall_sensor_y, INPUT);
pinMode(hall_sensor_b, INPUT);
pinMode(hall_sensor_g, INPUT);


//half bridge driver, hi part is active high
// lo part is active low
analogWrite(y_motor_pwm_hout, 0);//set motor to stop
analogWrite(b_motor_pwm_hout, 0);
analogWrite(g_motor_pwm_hout, 0);

digitalWrite(y_motor_lout, HIGH);
digitalWrite(b_motor_lout, HIGH);
digitalWrite(g_motor_lout, HIGH);

}

void loop()
{
// read the tuning knob value:
 
   buttonState = digitalRead(BUTTON_PIN);

  if (buttonState == 0) {
    counter++;
    while( buttonState == digitalRead(BUTTON_PIN))
  delayMicroseconds(100);
  }
if(counter>=2){
  counter=0;
}
  
 if(counter == 0){
   digitalWrite(LED,LOW);

   WheelDirection dir;

   dir == DIR_STOP;
   analogWrite(y_motor_pwm_hout, 0);//set motor to stop
   analogWrite(b_motor_pwm_hout, 0);
   analogWrite(g_motor_pwm_hout, 0);

   digitalWrite(y_motor_lout, HIGH);
   digitalWrite(b_motor_lout, HIGH);
   digitalWrite(g_motor_lout, HIGH);
 
 }
if (counter==1){

digitalWrite(LED,HIGH);

  for(int i=0;i<5;i++){
  sensorValue1 += analogRead (analogInPin1);
   } 
   sensorValue1 = sensorValue1/5; // map tuning knob to the range of the analog out: 
   
   
   outputValue1 = map(sensorValue1, 0, 1023, -120, 120); //PID CALC 
   Cn = outputValue1; //MOTOR DRIVE 
   WheelDirection dir;

   SWITCH_STATE_1 = digitalRead(ON_OFF_SWITCH_1);
   SWITCH_STATE_2 = digitalRead(ON_OFF_SWITCH_2);
     
  
   
  if (SWITCH_STATE_1 == 1 && SWITCH_STATE_2 == 0)
 dir = DIR_FORWARD;
    else if (SWITCH_STATE_1 == 0 && SWITCH_STATE_2 == 1)
 dir = DIR_BACKWARD; 
 
  throttle = abs(Cn); 
   MoveWheel(dir, throttle);
}
}
