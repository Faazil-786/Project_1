
//ARDUNIO_CODE



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











//NODEMCU_CODE

#define BLYNK_TEMPLATE_ID "TMPL3t-kk5uJr"
#define BLYNK_TEMPLATE_NAME "Street light"
#define BLYNK_AUTH_TOKEN 
"8j4KnGIYEmMAvqA9CcbvtiUhMLZk98dv"
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h> 
#include <BlynkSimpleEsp8266.h>
char auth[] = BLYNK_AUTH_TOKEN;
char ssid[] = "Nagu"; // Enter your Wifi Username
char pass[] = "Nagu@213"; // Enter your Wifi password
SimpleTimer timer;
String myString; // complete message from arduino, which consistors of snesors 
data
char rdata; // received charactors
int val1,val2,val3,val4,val5,val6;
WidgetLED led1 (V0);
WidgetLED led2 (V1);
WidgetLED led3 (V2);
WidgetLED led4 (V3);
WidgetLED led5 (V2);
WidgetLED led6 (V3);
// This function sends Arduino’s up time every second to Virtual Pin (1).
// In the app, Widget’s reading frequency should be set to PUSH. This means
// that you define how often to send data to Blynk App.
void myTimerEvent()
{
// You can send any value at any time.
// Please don’t send more that 10 values per second.
Blynk.virtualWrite(V1, millis() / 1000);
}
void setup()
{
// Debug console
Serial.begin(9600);
Blynk.begin(auth, ssid, pass, "blynk.cloud", 80);
timer.setInterval(1000L,sensorvalue1); //mq135 
timer.setInterval(1000L,sensorvalue2); //mq135
timer.setInterval(1000L,sensorvalue3); //mq135
timer.setInterval(1000L,sensorvalue4); //mq135
//timer.setInterval(1000L,sensorvalue5); //mq135
//timer.setInterval(1000L,sensorvalue6); //mq135
}
void loop()
{
if (Serial.available() == 0 )
{
Blynk.run();
timer.run(); // Initiates BlynkTimer
}
if (Serial.available() > 0 )
{
rdata = Serial.read();
myString = myString+ rdata;
if( rdata == '\n')
{
String l = getValue(myString, ',', 0);
String m = getValue(myString, ',', 1);
String n = getValue(myString, ',', 2);
String o = getValue(myString, ',', 3);
String p = getValue(myString, ',', 4);
String q = getValue(myString, ',', 5);
// these leds represents the leds used in Blynk application
val1 = l.toInt();
val2 = m.toInt();
val3 = n.toInt();
val4 = o.toInt();
val5 = p.toInt();
val6 = q.toInt();
myString = "";
// end new code
 }
 }
}
void sensorvalue1()
{
int sdata = val1;
if( sdata ==1){
// Serial.println("light1 fail");
 Blynk.logEvent("light1_");
led1.off();
}if( sdata ==0 ){
 led1.on();
}
Serial.print( "sdata1:");
 Serial.println( sdata);
}
void sensorvalue2()
{
int sdata = val2;
if( sdata ==1){
 //Serial.println("light2 fail");
 Blynk.logEvent("light2");
 led2.off();
}if( sdata ==0){
 led2.on();
}
 Serial.print( "sdata2:");
 Serial.println( sdata);
}
void sensorvalue3()
{
int sdata = val3;
if( sdata ==1){
 //Serial.println("light3 fail");
 led3.off();
}if( sdata ==0){
 led3.on();
}
 
 Serial.print( "sdata3:");
 Serial.println( sdata);
}
void sensorvalue4()
{
int sdata = val4;
if( sdata ==1){
// Serial.println("light4 fail");
 led4.off();
}if( sdata ==0){
 led4.on();
}
 Serial.print( "sdata4:");
 Serial.println( sdata);
}
/*void sensorvalue5()
{
int sdata = val5;
if( sdata ==1){
 //Serial.println("light5 fail");
 led5.off();
}if( sdata ==0){
 led5.on();
}
 Serial.print( "sdata5:");
 Serial.println( sdata);
}
void sensorvalue6()
{
int sdata = val6;
if( sdata ==1){
 //Serial.println("light6 fail");
led6.off();
}if( sdata ==0){
 led6.on();
}
Serial.print( "sdata6:");
 Serial.println( sdata);
}
void sensorvalue7()
{
int sdata = val7;
if(val1==0 && val2==0&&val3==0 && val4==0&&val5==0 && val6==0 && 
val7 >=15){
led1.off();
led2.off();
led3.off();
led4.off();
led5.off();
led6.off();
 
}
Serial.print( "sdata7:");
 Serial.println( sdata);
}
void sensorvalue8()
{
int sdata = val8;
if(sdata==0){
Blynk.logEvent("power_off");
}
Serial.print( "sdata8:");
 Serial.println( sdata);
}
*/
/*String getValue(String data, char separator, int index)
{
int found = 0;
int strIndex[] = { 0, -1 };
int maxIndex = data.length() - 1;
for (int i = 0; i <= maxIndex && found <= index; i++) {
if (data.charAt(i) == separator || i == maxIndex) {
found++;
strIndex[0] = strIndex[1] + 1;
strIndex[1] = (i == maxIndex) ? i+1 : i;
 }
 }
return found > index ? data.substring(strIndex[0], strIndex[1]) : "";
}*/
String getValue(String data, char separator, int index) {
 int found = 0;
 int strIndex[] = { 0, -1 };
 int maxIndex = data.length() - 1;
 // Iterate through the characters of the string
 for (int i = 0; i <= maxIndex && found <= index; i++) {
 // Check if the current character is the separator or if it's the last character
 if (data.charAt(i) == separator || i == maxIndex) {
 found++; // Increment the counter for found separators
 strIndex[0] = strIndex[1] + 1; // Set the start index of the next substring
 strIndex[1] = (i == maxIndex) ? i + 1 : i; // Set the end index of the current 
substring
 }
 }
 // Check if the requested index was found
 // If found, return the substring, otherwise return an empty string
 return found > index ? data.substring(strIndex[0], strIndex[1]) : "";
