// Arduino_Pi_com
// Arduino to Pi communication
// Arduino as slave 
// Created 3-20-18
// By Jeff

int inData[7]={};  //array to store commands recieved by pi
int signalVal;
int pinDigit;
int pinNumber;
int analogValue;
int digitalState;
int sensorVal;   //analog sensor input

void setup() {
 Serial.begin(9600);  // sets serial baude rate
 Serial.println("Hello Pi");  //send welcome message to pi when serial communication is begun
}

void loop() {
Serial.print("Hello pi");

getSerial();  // calls serial reading function
printInData(); // signalVal and pinDigit are set in this function that needs moved if this function is discarded
dataIo();     // switch statement to control io's, both read and write

}

void printInData() {        //this reads back input serial input for debugging purposes
  int first = inData[0];
  int second = inData[1];
  int third = inData[2];
  int fourth = inData[3];
  int fifth = inData[4];
  int sixth = inData[5];
  int seventh = inData[6];
Serial.println(first);
Serial.println(second);
pinDigit = (third*10) + fourth;
Serial.println(pinDigit);
signalVal = (fifth*100) + (sixth*10) + seventh;
Serial.println(signalVal);
}



 void getSerial(){
  int x = 0;
 while (x < 7) {
  int sByte = 0;
int iData = 0;
  sByte = Serial.read();
  if(sByte >0 ){
    iData = iData * 10 + sByte - '0';
    inData[x] = iData;
     // Serial.println(inData[x]);
      x++;
     // Serial.println(x);
  }
 }

 }

void dataIo(){  
  
  switch(inData[0]) // switches the first value received 
  {
  case 1:       //  WRITE    if '1' then write either analog or digital        
    {           // this is the first digit
      
      
      switch (inData[1])    
      {
      case 1:   //second digit analog write
        {
          
          pinNumber = pinDigit;
       
          analogValue = signalVal;
          pinMode(pinNumber, OUTPUT);
          analogWrite(pinNumber, analogValue);
          pinNumber = 0;
          break;
        }
      case 2:   //second digit digital write
        {
          
         
          pinNumber = pinDigit;
          
          digitalState = signalVal;
          pinMode(pinNumber, OUTPUT);
          if (digitalState == 0)
          {
            digitalWrite(pinNumber, LOW);
          }
          if (digitalState == 1)
          {
            digitalWrite(pinNumber, HIGH);
          }
          pinNumber = 0;
          break;
          
        }
     }
     break; 
   }
   case 2:  // READ     if '2' then  read either analog or digital 
   {        // this is the first digit
   
    switch(inData[1])
    {
      case 1: //second digit analog read
      {
        
        pinNumber = pinDigit;
        pinMode(pinNumber, INPUT);
        sensorVal = analogRead(pinNumber);
        Serial.print("Value: ");
        Serial.println(sensorVal);
        sensorVal = 0;
        pinNumber = 0;
        break;
      }
      case 2:  //second digit digital read
      {
        
        pinNumber = pinDigit;
        pinMode(pinNumber, INPUT);
        sensorVal = digitalRead(pinNumber);
     
        Serial.print(sensorVal);
        sensorVal = 0;
        pinNumber = 0;
        break;
      }
    }
    break;
   }
  }
}
