Hello..

This tutorial will make your Souliss ON/OFF Output Timer/T11 typicals Node capable to send notification to Pushbullet.

First we got to declare ThingSpeak API and ThingSpeak server, add this in top of your sketch

```
// ThingSpeak API
const char* apiKey = "Your ThingSpeak API key";
const char* serverTS = "api.thingspeak.com";
```

In your Souliss Node sketch you should have a code to read the pin state..

`````
 int inputState = digitalRead(RELAY);
          if (inputState != oldInputState){
              sendInputState(inputState);
              oldInputState = inputState;
          } 
`````

Put above code in void loop(), don't forget to define oldInputState as bool in void setup()

`  
  bool oldInputState;
` 

Then in void setup you also should have oldInputState value, it easy, add this below code in void setup()  

`  
  oldInputState = !digitalRead(RELAY);
`  

Now the last thing we should do in the arduino sketch is create a function of sendInputState, put this after void loop()

``````````````````````````````
  // send data to ThingSpeak.com
  void sendInputState(bool inputState){
    Serial.println(inputState); 
 
       WiFiClient client;
       if(!client.connect(serverTS,80)) {  //   "184.106.153.149" or api.thingspeak.com
          Serial.println("Connection Fail!");
        }
        else {
          String postStr = apiKey;
                 postStr +="&field1=";
                 postStr += String(inputState);
                 postStr += "\r\n";
 
                 client.print("POST /update HTTP/1.1\n");
                 client.print("Host: api.thingspeak.com\n");
                 client.print("Connection: close\n");
                 client.print("X-THINGSPEAKAPIKEY: "+apiKey+"\n");
                 client.print("Content-Type: application/x-www-form-urlencoded\n");
                 client.print("Content-Length: ");
                 client.print(postStr.length());
                 client.print("\n\n");
                 client.print(postStr);
                 Serial.println("Send Relay State to Thingspeak");
 
        }
  client.stop();
  Serial.println("Client to send Stoped");
  
}
``````````````````````````````

Now we need to setup ThingSpeak Channel, ThingHTTP and React.. but first you will need Pushbullet token, so create pushbullet account, then create ThingSpeak account..

Steps :

1. Create New Channel in ThingSpeak, name it as you wish, in my case "PushbulletTriggerTeras"

2. Make sure you have one field, use field 1 and name it as "Light State"

![ts11](https://cloud.githubusercontent.com/assets/12625575/8893820/87afe1ac-33ca-11e5-8455-b6cdfa0fc7f2.PNG)

3. Click APPS in ThingSpeak, and click ThingHTTP app, create new ThingHTTP, this will send http request to pushbullet if the Light ON, fill the field as the picture

![ts2](https://cloud.githubusercontent.com/assets/12625575/8893826/d39051e2-33ca-11e5-9600-716a0502995a.PNG)

4. Create another ThingHTTP for the Light OFF, follow the picture below..

![ts1](https://cloud.githubusercontent.com/assets/12625575/8893767/f16cc176-33c7-11e5-84c5-541380c2a40a.PNG)

5. Back to ThingSpeak APPS, now click React app, create new React, this will executed ThingHTTP is some condition meet.. we create a new React for Light ON

![ts4](https://cloud.githubusercontent.com/assets/12625575/8893846/a40e9eaa-33cb-11e5-9e30-f60f7544a95d.PNG)

6. Last step, create another React for Light OFF

![ts5](https://cloud.githubusercontent.com/assets/12625575/8893852/c9309792-33cb-11e5-8dae-3713ddfaa0d4.PNG)

You are done, now everytime you pressing ON OFF button in SoulissApp you should get notification in Pushbullet..