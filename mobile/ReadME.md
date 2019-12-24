## Analyze mobile applications for getting more information of IoT device
### Android
The easiest way for finding interesting web endpoints, some API, of IoT device it checks them from mobile applications. 
Because often for controlling device your phone should be in same network (WiFi) than IoT device.

#### For finding and analyze endpoints we will need:
* Android device with root and application for Iot device which we want to hack ;)
* Burp Suite, enough community version
* adb drivers for working with phone
* frida server

For interception requests from application we should configure proxy on our WiFi connection.
