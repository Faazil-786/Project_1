#include <Wire.h> 
#include <LiquidCrystal_I2C.h>
LiquidCrystal_I2C lcd(0x27,16,2);
#include <SoftwareSerial.h>
SoftwareSerial nodemcu(2,3);
#include <Servo.h>
Servo myservo; // create servo object to control a servo
// twelve servo objects can be created on most boards
int pos = 0; 
#define Slot1 6
#define Slot2 7
#define Slot3 8
#define Slot4 9
#define gate_sensor1 10
#define gate_sensor2 11

    int Slot1_state;    //parking1_sensor
    int Slot2_state;    //parking2_sensor
    int Slot3_state;    //parking3_sensor
    int Slot4_state;    //parking4_sensor
    int gate_sensor1_state; //gate_open_sensor
    int gate_sensor2_state; //gate_close_sensor
    
    
    String sensor1;
    String sensor2;
    String sensor3;
    String sensor4;
    String sensor5;
    String sensor6;
    String cdata ="";
    bool myflag =true;
    
    void setup() {
     pinMode(Slot1, INPUT_PULLUP);
     pinMode(Slot2, INPUT_PULLUP);
     pinMode(Slot3, INPUT_PULLUP);
     pinMode(Slot4, INPUT_PULLUP);
     
     
     pinMode(gate_sensor1, INPUT_PULLUP);
     pinMode(gate_sensor2, INPUT_PULLUP);
     myservo.attach(5);
    // myservo.write(0);
     Serial.begin(9600);
     nodemcu.begin(9600);
     lcd.init();
     
     lcd.backlight();
     lcd.begin(16, 2);
     lcd.setCursor (0, 0);
     lcd.print("CAR PARKING ");// Print a message to the LCD.
     lcd.setCursor (0, 1);
     lcd.print("SYSTEM");
     
    }
    void loop() {
     Slot1_state = digitalRead(Slot1);
     Slot2_state = digitalRead(Slot2);
     Slot3_state = digitalRead(Slot3);
     Slot4_state = digitalRead(Slot4);
     gate_sensor1_state = digitalRead(gate_sensor1);
     gate_sensor2_state = digitalRead(gate_sensor2);
    Serial.print( "gate_sensor1_state:");
    Serial.println( gate_sensor1_state);
    Serial.print( "gate_sensor2_state:");
    Serial.println( gate_sensor2_state);
     
     sensor1 = Slot1_state;
     sensor2 = Slot2_state;
     sensor3 = Slot3_state;
     sensor4 = Slot4_state;
     //sensor5 = gate_sensor1_state;
     //sensor6 = gate_sensor2_state;
     cdata = cdata + sensor1 +"," + sensor2 + ","+ sensor3 +","+ sensor4 ; // comma
    will be used a delimeter
     Serial.println(cdata);
     
     nodemcu.println(cdata);
     delay(1000);
     // 100 milli seconds
     cdata = "";
     // wait a bit:

    parking1();
    parking2();
    parking3();
    parking4();
    gate_open();
    gate_close();
    }
    
    
    //LCD Display code
    void parking1(){
    if( Slot1_state ==0) {
     lcd.setCursor (0, 0);
     lcd.print("S1:FULL ");
     Serial.println("S1:FULL ");
     myflag =true;
    }
    if(Slot1_state ==1)
     lcd.setCursor (0, 0);
     lcd.print("S1:EMTY ");
     Serial.println("S1:EMTY ");
    }
    void parking2(){
    if( Slot2_state ==0) {
     lcd.setCursor (8, 0);
     lcd.print("S2:FULL ");
     myflag =true;
     Serial.println("S2:FULL ");
    }else if(Slot2_state ==1)
     lcd.setCursor (8, 0);
     lcd.print("S2:EMTY ");
    // Serial.println("S2:EMTY ");
    }void parking3(){
    if( Slot3_state ==0) {
     lcd.setCursor (0, 1);
     lcd.print("S3:FULL ");
     myflag =true;
    // Serial.println("S3:FULL ");
    }else if(Slot3_state ==1)
     lcd.setCursor (0, 1);
     lcd.print("S3:EMTY ");
     //Serial.println("S3:EMTY ");
    }
    void parking4(){
    if( Slot4_state ==0) {
     lcd.setCursor (8, 1);
     lcd.print("S4:FULL ");
     myflag =true;
    // Serial.println("S4:FULL ");
    }else if(Slot4_state ==1)
     lcd.setCursor (8, 1);
     lcd.print("S4:EMTY ");
    // Serial.println("S4:EMTY ");
    }
    
    //Gate_opening_closing
    void gate_open(){
    if(gate_sensor1_state ==0 || gate_sensor2_state ==0 && myflag ==true){
     
     pos = 0;
     
     
    }else if(gate_sensor1_state ==1 && gate_sensor2_state ==1 ){
     pos = 100;
     // control servo motor arccoding to the angle
    }
    myservo.write(pos);
    }
    
    void gate_close(){
     if(Slot1_state ==0 && Slot2_state ==0 && Slot3_state ==0 && Slot4_state ==0 ){
     pos = 100;
     lcd.clear();
     lcd.setCursor (3, 0);
     lcd.print("PARKING FULL ");
     lcd.setCursor (0, 1);
     lcd.print(" ");
     myflag =false;
     }
     myservo.write(pos);
     
    }
