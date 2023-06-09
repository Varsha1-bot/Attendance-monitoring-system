# Attendance-monitoring-system
Designing  an Attendance Monitoring system using RFID. 

# Problem Statement:
Design an attendance system using RFID. A set of RFID tags must be pre-registered in
the system. System should be interfaced with an RFID reader. When a user taps the
RFID card , entry time along with RFID tag number (or user name associated with RFID
tag) must be stored in ThingSpeak. Create an account on twitter. You need to send a
tweet to your twitter account for every RFID based entry

# Introdution:
Software called the Attendance Monitoring System was created to track daily attendance
in schools, universities, institutes, and businesses. It makes it easier to get a specific
user’s attendance data every day. This approach aids in assessing attendance eligibility
standards. The intention behind creating an attendance monitoring system is to automate
the manual process of taking attendance. The software’s ability to automatically generate
reports at the session’s end or in between sessions is another reason for its development.

# Objectives:
• The purpose of the attendance monitoring software is to reduce the time that is
consumed when attendance is taken manually.
• Weekly and monthly attendance reports for the employee are produced.
• When we scan RFID tag count of visitors and their name will be displayed on LCD.
• At the same time, entry date,time and other details will be recorded in ThingSpeak
cloud and tweet will be sent.


 # Flowchart:
 
![Screenshot 2022-12-22 114857](https://github.com/Varsha1-bot/Attendance-monitoring-system/assets/83944628/ab29419c-8d57-425d-9a26-266d6f64b3af)
  
# Getting the Unique RFID Code from RFID Tag:

Before programming the NodeMCU, we need to find out the unique 12 digit RFID code. As we discussed before, RFID tags contain a 12 digit unique code and it can be decoded by using an RFID reader. When we swipe the RFID tag near the Reader, the Reader will give us a unique code via the serial port on getting connected to NodeMCU. For that, we need to set up a small piece of code, and once we get the codes for all our modules, we will need to keep them safe for later use.

After successfully uploading the small code given below, open the serial monitor, and set the baud rate to 9600. Then swipe the card near the Reader and you will see the 12 digit code displayed on the serial monitor. Do this process for all the used RFID tags and note them down for future references.

# Arduino Program to get Unique 12 Digit RFID Tag Code:
```c

int count = 0;
char card_no[12];
void setup()
{
   Serial.begin(9600);
}
void loop()
{
   if(Serial.available())
   {
      count = 0;
      while(Serial.available() && count < 12)
      {
         card_no[count] = Serial.read();
         count++;
         delay(5);
      }
      Serial.print(card_no);
   }
}
```
# Final Code for Arduino Based Event Management Project:

We start the code by including all the required library files. We have used the ThingSpeak.h library to communicate with the ThingSpeak platform. You can add this library with the help of the board manager method. As we are using a 16x2  I2C based LCD, we have to include the “LiquidCrystal_I2C.h” and “wire. h” libraries. We also need to include the time library that will help us to get our current timestamp.
```c

#include "ThingSpeak.h"
#include <ESP8266WiFi.h>
#include<LiquidCrystal_I2C.h>
#include<Wire.h>
#include <time.h>
```
Next, we initialize our LCD with the help of the function given here.
```c
LiquidCrystal_I2C lcd(0x3f, 16, 2);
```
Now, we define all the necessary network credentials i.e. SSID and Password. These are required to connect the NodeMCU with the internet. Then we define all the credentials for the ThingSpeak account such as the channel number and write API which was recorded earlier. Make sure, you have edited your credentials in place of these variables.
```c
unsigned long ch_no = 123456
const char * write_api = "MCxxxxxxx";
char ssid[] = "admin";
char pass[] = "12345678";
```
inside setup(), we start by enabling the serial monitor. Next, we get the current timestamp. Thereafter, we use the wire function to initialize the LCD.
```c
Serial.begin(9600);
configTime(11 * 1800, 0, "pool.ntp.org", "time.nist.gov");
Wire.begin(D2, D1);
lcd.begin(16, 2);
lcd.init();
```
To connect nodeMCU to the internet, the WiFi.begin function was called and the network SSID and password were passed as its arguments. After that, we checked if Wi-Fi is connected or not using WiFi.status() function, and upon successful connection, a message was shown on the LCD was stating “WiFi connected”.
```c
WiFi.begin(ssid, pass);
  while (WiFi.status() != WL_CONNECTED)
  {
    delay(500);
    Serial.print(".");
  }
  Serial.println("WiFi connected");
  Serial.println(WiFi.localIP());
  lcd.setCursor(0, 1);
  lcd.print("WiFi connected");
  ```
Inside while(1) loop, we count the total no. of visitors displayed on LCD using command lcd.print as shown below.
```c
lcd.setCursor(0, 0);
lcd.print("No. of Vis:" + String(vis_count));
```
To get the current timestamp, time() was used and the output was saved in a character array as shown below.
```c
time_t now = time(nullptr);
str = ctime(&now);
```
Here, the unique 12 digit codes of the RFID tags are decoded and stored in an array. Then the elements of the array will be matched with the Stored Tag numbers in the memory to get the visitor’s details. Remember the Tag details we saved earlier.
```c
if (Serial.available())
  {
    count = 0;
    while (Serial.available() && count < 12)
    {
      input[count] = Serial.read();
      count++;
      delay(5);
    }
if (count == 12)
    {
      if ((strncmp(input, "0B00284F9AF6", 12) == 0) && (a == 0))
      {
        lcd.setCursor(0, 1);
        lcd.print("Welcome Debasis         ");
        vis_count++;
        a = 1;
        delay(2000);
        lcd.clear();
        senddata("Debasis");
      }
}
```
Senddata() function is used to send the visitor data to ThingSpeak cloud. From ThingSpeak, we can download this data later in excel format to see the recorded details such as Visitor Name and Time of Entry, etc.
```c
void senddata(String input)
{
  ThingSpeak.setField(1,input);
  ThingSpeak.setField(2, str);
  ThingSpeak.writeFields(ch_no, write_api);
}
```
This marks the end of our coding section, now we can move onto the testing part.



# Results:
# Output in serial window:



![RFID](https://github.com/Varsha1-bot/Attendance-monitoring-system/assets/83944628/4de321fe-a4b9-4504-8ede-f6f988197780)

# Hardware results:
![circuit connections](https://github.com/Varsha1-bot/Attendance-monitoring-system/assets/83944628/2d4acc95-db21-4ffd-86e4-cec72669be36)

# ThingSpeak results:
![WhatsApp Image 2022-12-12 at 7 59 23 PM (3)](https://github.com/Varsha1-bot/Attendance-monitoring-system/assets/83944628/d4ee90cf-0e0d-4a35-a498-f011f287f67d)

# Numeric data:

![numeric data](https://github.com/Varsha1-bot/Attendance-monitoring-system/assets/83944628/a7cc7666-1bec-49aa-b873-4d34c3a228df)

# Twitter results:

![efh](https://github.com/Varsha1-bot/Attendance-monitoring-system/assets/83944628/bc786702-2b8b-49fe-951d-9c8c41385989)
# Conclusion:
The IoT-based attendance monitoring system is very successful and reliable. It can be turned into an efficient and error-free attendance management system for schools, colleges, and other organizations.In our project, we have used RFID sensor.Each user will be provided with RFID tags which contains 12-digit unique code.Only if the user is present on that particular date and within time, the attendance will be considered.Once the RFID tag is scanned by the user the entry time, date, month will be recorded. We have used ThingSpeak cloud platform which is used to collect the data and store it in the cloud with advanced data analysis.Every time a user scans the card a tweet is sent. This system is easy to use and very convenient, making it a great choice for any organization. Thus, we have completed our project successfully.
