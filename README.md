Automated water pumps plays a vital role in the reduction and efficiency of water scarcity and water management respectively,
leveraging on the systematic approach of the iterative waterfall model for the dengn and development of this automated pump is
 an innovative approach to reduce the risk of defect on the design and maximize its productivity. The ultrasonic sensor sends
 distances to the arduino board in the form of varying electrical signals that will in tum control the submersible pump by
 triggering the relay or a command can be sent through the BT terminal app Through rigorous testing and evaluation, this work
 has shown the capabilities and limitation of this automated water pump switch. This study contributes to the advancement of
 technology in the area of water management and thus offering potential applications in residential and commercial buildings 
 and also implicitly encouraging embedded system developers to leverage on the different approaches that could be used to model
 the analysis, design and implementation of embedded products.

# Automated-water-pump-switch
This code is for programming to Arduino microcontroller to automate water pumps


#define echo 3
#define trig 2
#define RED 8
#define YELLOW 6 
#define GREEN  7
#define RELAY 12
#include <SoftwareSerial.h>
SoftwareSerial mySerial(10, 11); // RX, TX
long duration;
int distance;
float volume;
void ultrasonic(){
       digitalWrite(trig,LOW);
      delayMicroseconds(10);
    
      digitalWrite(trig,HIGH);
      delayMicroseconds(10); 
    
      digitalWrite(trig,LOW);
      delayMicroseconds(10);

      duration = pulseIn(echo,HIGH);

      distance = duration*0.034/2;
      
      /*
       * CALCULATIONS FOR VOLUME OF WATER TANK
      volume of a cylinder V = π*r^2*h
      π=3.142
      r=5.81, r^2=33.7561
      h=distance
      */
      volume = 3.142*33.7561*distance;
      //calibration of volumetric size of tank
      volume = 1593.00-volume;
      //conversion from cm^3 to liters
      volume = volume/1000;
      
      Serial.print("D I S T A N C E = ");
      Serial.print(distance);
      Serial.print(" cm.");
      Serial.println();
      Serial.print("VOLUME = ");
      Serial.print(volume);
      Serial.print(" liters.");
      Serial.println();
      Serial.println();
      mySerial.print("D I S T A N C E = ");
      mySerial.print(distance);
      mySerial.print(" cm.");
      mySerial.println();
      mySerial.print("VOLUME = ");
      mySerial.print(volume);
      mySerial.print(" liters.");
      mySerial.println();
      mySerial.println(); 
}
void  bt(){
      char BT=mySerial.read();
      delayMicroseconds(1000);
      delay(500);
}
void setup() {
  // put your setup code here, to run once:
      pinMode(trig,OUTPUT);
      pinMode(echo,INPUT);
      pinMode(RELAY,OUTPUT);
      pinMode(RED,OUTPUT);
      pinMode(YELLOW,OUTPUT);
      pinMode(GREEN,OUTPUT);
      // Open serial communications and wait for port to open:
      Serial.begin(9600);
      while (!Serial) {
      // wait for serial port to connect. Needed for native USB port only
      }
      // set the data rate for the SoftwareSerial port
      mySerial.begin(9600);
}

void loop() {
  // put your main code here, to run repeatedly:
  ultrasonic();
  bt();
      if ((distance>=13) || (BT=='p')){
       // digitalWrite(RELAY,HIGH);
        digitalWrite(RED,HIGH);
        digitalWrite(YELLOW,LOW);
        digitalWrite(GREEN,LOW);
        Serial.print("pump on");
        Serial.println(); 
        mySerial.print("pump on bt");
        mySerial.println(); 
      }
      else if(distance>=3 && distance<13){
        digitalWrite(RED,LOW);
        digitalWrite(YELLOW,HIGH);
        digitalWrite(GREEN,LOW);
        while (BT=='s'){
          digitalWrite(RELAY,LOW);
          Serial.print("pump stoped by BT command. ");
          Serial.println();
          mySerial.print("pump stoped by BT command. ");
          mySerial.println();
          delay(500);
          ultrasonic();
          bt();
            if ((distance>=13) || (BT=='p')){
               break;
            }
        }
      }
      else if(distance<=2 ){
        digitalWrite(RED,LOW);
        digitalWrite(YELLOW,LOW);
        digitalWrite(GREEN,HIGH);
        digitalWrite(RELAY,LOW);
        Serial.print("pump off ");
        Serial.println();
        mySerial.print("pump off ");
      }
   }
