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

#### SSL pining bypass using frida

Get certificate from Burp Suite and send it to phone in /data/local/tmp/ but you should change name of cert on phone to cert-der.crt or change name cert in js-script.
Get frida-server and js-script for ssl pining bypass and also send to phone in /data/local/tmp/.
Change permissions for frida server and start it, use next commands:

```
adb shell chmod +x /data/local/tmp/frida-server
adb shell ./data/local/tmp/frida-server &
```

Get list of working application on phone

```
frida-ps -U
```

Start necessary application with SSL pining bypass:

```
frida -l root-ssl-pin-bypass.js -U -f com.necessary.app --no-pause
```

Note: js-script should be located in directory where you start this command.

After that you should see all requests from com.necessary.app in Burp Suite

# TODO
* Add iOS option
* Add way for BLE requests analize
