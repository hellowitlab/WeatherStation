# WeatherStation
Weather Station from Team Thorussell @ Witlab

# Materiale

  - Arduino UNO
  - Raspberry Pi 3 (B+) con sistema operativo Raspbian
  - Sensore di Hall
  - Anemometro (Amazon: https://www.amazon.it/dp/B01F1QBF1K/ref=asc_df_B01F1QBF1K53248169/?tag=googshopit-21&creative=23394&creativeASIN=B01F1QBF1K&linkCode=df0&hvdev=c&hvnetw=g&hvqmt=)
  - Sensore di umidità del terreno (igrometro)
  - Sensore di temperatura e umidità
  - Stampante 3D 

# Software
## Codice per Arduino 
    #include <Wire.h>
    #define SLAVE_ADDRESS 0X04
    #include <Adafruit_AM2320.h>
    #include <Adafruit_Sensor.h>
    #include <DHT.h>
    #include <DHT_U.h>
    #define dataPin 8 // Defines pin number to which the sensor is connected
    #define DHTTYPE DHT11 
    
    DHT dht(dataPin, DHTTYPE); // Creats a DHT object
    int sensorPin = A1;  
    int sensorValue = 0;  
    int percent = 0;
    int t = 0;
    int h = 0;
    int Level;
    int HallPin = 9;
    int pluvi = 0;
    
    int convertToPercent(int value) {
        int percentValue = 0;
        percentValue = map(value, 0, 685, 0, 100);
        return percentValue;
    }
    
    void setup() {
        pinMode(HallPin, INPUT);
        Serial.begin(9600);
        Wire.begin(SLAVE_ADDRESS);
        dht.begin();
    }
    void loop() {
        int sensorValue = analogRead(A0);
        float outvoltage = sensorValue * (5.0 / 1023.0);
        Level = 6*outvoltage;
        t = dht.readTemperature(); // Gets the values of the temperature
        h = dht.readHumidity(); // Gets the values of the humidity
        sensorValue = analogRead(sensorPin);
        percent = convertToPercent(sensorValue);
  
        for(int i=0;i<100;i++){
            if(digitalRead(HallPin)== LOW){
                pluvi++;
            }
        delay(100);
        }
 
    Serial.print("-");
    Serial.print(Level);
    Serial.print("-");
    Serial.print(t);
    Serial.print("-");
    Serial.print(h);
    Serial.print("-");
    Serial.print(percent);
    Serial.print("-");
    Serial.print(pluvi*6);
    Serial.print("-");
    Serial.println();
    }
    


## Codice per Raspberry 
    import serial
    import time

    ser = serial.Serial('/dev/ttyACM0', 9600)
    
    while True:
        read_ser = ser.readline()
        s = str((ser.readline(),16))
    
        x,level,t,h,percent,pluvi,y = s.split("-")
        speed=0
        level2= int(level)
   
        if level2 == 0:
            speed=0.5
        if level2 == 1:
            speed=3
        if level2 == 2:
            speed=8.5
        if level2 == 3:
            speed=15.5
        if level2 == 4:
            speed=24
        if level2 == 5:
            speed=33.5
        if level2 == 6:
            speed=44
        if level2 == 7:
            speed=55.5
        if level2 == 8:
            speed=68
        if level2 == 9:
            speed=81.5
        if level2 == 10:
            speed=95.5
        if level2 == 11:
            speed=110
        if level2 == 12:
            speed=118
        
        print ("Level of speed: ", level)
        print("Wind speed: ", speed,"km/h")
        print("Temperature: ", t, "°C")
        print("Humidity: ", h, "%")
        print ("Ground humidity: ",percent,"%")
        print("Water fallen: ",pluvi,"ml")
        print ()
        time.sleep(5)
