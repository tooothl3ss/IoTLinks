## Analyze mobile applications for getting more information of IoT device
### Android
The easiest way for finding interesting web endpoints, some API, of IoT device it checks them from mobile applications. 
Because often for controlling device your phone should be in same network (WiFi) than IoT device.

#### For finding and analyze endpoints we will need:
* Android device with root and application for Iot device which we want to hack ;)
* Burp Suite, enough community version
* adb drivers for working with phone
* frida server

For interception requests from application we should configure proxy on our WiFi connection, but that would we can get nessesary requests from application in Burp Suite we should install certificate on phone or use script for ssl unpining for frida. 

For installing certificat on the system, in order it would be used by all applications we create new certificate for valid period one year and add it in trusted on phone and add it to Burp Suite.

#### Installing certificate in system
Making new certificate:

```cp /usr/lib/ssl/openssl.cnf ./
openssl req -x509 -days 365 -nodes -newkey rsa:2048 -outform der -keyout server.key -out ca.der -extensions v3_ca -config openssl.cnf
openssl rsa -in server.key -inform pem -out server.key.der -outform der
openssl pkcs8 -topk8 -in server.key.der -inform der -out server.key.pkcs8.der -outform der -nocrypt
openssl x509 -in ca.der -inform der -noout -text
openssl x509 -inform DER -in ca.der -out ca.pem
openssl x509 -inform PEM -subject_hash_old -in ca.pem | head -1
cp ca.pem <hash>.0 
```
Adding this certificate to phone:

```
adb root
adb remount
adb push <hash>.0 /sdcart/
adb shell
mv /sdcard/<hash>.0 /system/etc/security/cacerts/  
chmod 644 /system/etc/security/cacerts/<hash>.0  
reboot
```
After full phone reboot check your certificate in _Settings -> Security -> Trusted Credentials_
Configuring phone done, now will add cert in Burp and collect requests from phone.
