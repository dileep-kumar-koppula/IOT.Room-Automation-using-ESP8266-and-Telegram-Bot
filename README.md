# Room Automation using ESP8266 and Telegram Bot

This project allows you to control devices such as lights and fans connected to an ESP8266 using a Telegram bot. The bot interacts with the ESP8266, sending commands to control the devices (turning them ON or OFF). The system uses Wi-Fi for communication and utilizes secure connections for the Telegram bot.

## Requirements

1. **Hardware**:
   - ESP8266 (e.g., NodeMCU or Wemos D1 Mini)
   - Relays (for controlling lights and fans)
   - Jumper wires and breadboard

2. **Software**:
   - Arduino IDE with ESP8266 board support
   - Libraries:
     - `ESP8266WiFi.h` - For Wi-Fi functionality
     - `WiFiClientSecure.h` - For secure communication with Telegram
     - `UniversalTelegramBot.h` - For Telegram bot functionality
     - `ArduinoJson.h` - For handling JSON data in Telegram messages

3. **Telegram Bot**:
   - Create a new bot via the Telegram app by chatting with the [BotFather](https://core.telegram.org/bots#botfather).
   - Get the bot token from BotFather, which will be used to communicate with your bot.

## Features

- **Wi-Fi Connectivity**: Connects the ESP8266 to a Wi-Fi network.
- **Telegram Bot**: Allows control of lights and fans through a Telegram bot using inline keyboard buttons.
- **Secure Communication**: Uses secure HTTPS communication between the ESP8266 and Telegram servers.

## Pin Definitions

This project assumes you are using the following pins for controlling devices:

- **Lights**: 
  - `l1` - D0
  - `l2` - D1
  - `l3` - D2
  - `l4` - D3
- **Fans**:
  - `f1` - D4
  - `f2` - D5
  - `f3` - D6
  - `f4` - D7

## Code

### Full Code

```cpp
#include <ESP8266WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>
#include <ArduinoJson.h>

char ssid[] = "Wi-Fi Name";         // your network SSID (name)
char password[] = "B8V6VunuVuy&%vyujbyg";  // your network password

#define BOT_TOKEN "1978817624:AAGul0EGuyvLIyS9j1B1VA_EIYBvldOf0pc"

const unsigned long BOT_MTBS = 1000; // mean time between scan messages

X509List cert(TELEGRAM_CERTIFICATE_ROOT);
WiFiClientSecure secured_client;
UniversalTelegramBot bot(BOT_TOKEN, secured_client);
unsigned long bot_lasttime;          // last time messages scan has been done
bool Start = false;

// Pin definitions for devices
#define l1 D0
#define l2 D1
#define l3 D2
#define l4 D3
#define f1 D4
#define f2 D5
#define f3 D6
#define f4 D7

void setup() {
  Serial.begin(115200);

  // Set WiFi to station mode and disconnect from an AP if it was previously connected
  WiFi.mode(WIFI_STA);
  WiFi.disconnect();
  delay(100);

  // Initialize pins as output
  pinMode(l1, OUTPUT);
  digitalWrite(l1, HIGH);
  pinMode(l2, OUTPUT);
  digitalWrite(l2, HIGH);
  pinMode(l3, OUTPUT);
  digitalWrite(l3, HIGH);
  pinMode(l4, OUTPUT);
  digitalWrite(l4, HIGH);
  pinMode(f1, OUTPUT);
  digitalWrite(f1, HIGH);
  pinMode(f2, OUTPUT);
  digitalWrite(f2, HIGH);
  pinMode(f3, OUTPUT);
  digitalWrite(f3, HIGH);
  pinMode(f4, OUTPUT);
  digitalWrite(f4, HIGH);

  // Connect to WiFi network
  Serial.print("Connecting Wifi: ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(500);
  }

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

  // Only required on 2.5 Beta
  secured_client.setInsecure();

  // longPoll keeps the request to Telegram open for the given amount of seconds if there are no messages
  bot.longPoll = 60;
}

void handleNewMessages(int numNewMessages) {

  for (int i = 0; i < numNewMessages; i++) {

    // If the type is a "callback_query", an inline keyboard button was pressed
    if (bot.messages[i].type == F("callback_query")) {
      String text = bot.messages[i].text;
      Serial.print("Call back button pressed with text: ");
      Serial.println(text);

      // Handle button press actions for lights and fans
      if (text == F("l1-on")) {
        digitalWrite(l1, LOW);
      }
      else if (text == F("l1-off")) {
        digitalWrite(l1, HIGH);
      }
      else if (text == F("l2-on")) {
        digitalWrite(l2, LOW);
      }
      else if (text == F("l2-off")) {
        digitalWrite(l2, HIGH);
      }
      else if (text == F("l3-on")) {
        digitalWrite(l3, LOW);
      }
      else if (text == F("l3-off")) {
        digitalWrite(l3, HIGH);
      }
      else if (text == F("l4-on")) {
        digitalWrite(l4, LOW);
      }
      else if (text == F("l4-off")) {
        digitalWrite(l4, HIGH);
      }
      else if (text == F("f1-on")) {
        digitalWrite(f1, LOW);
      }
      else if (text == F("f1-off")) {
        digitalWrite(f1, HIGH);
      }
      else if (text == F("f2-on")) {
        digitalWrite(f2, LOW);
      }
      else if (text == F("f2-off")) {
        digitalWrite(f2, HIGH);
      }
      else if (text == F("f3-on")) {
        digitalWrite(f3, LOW);
      }
      else if (text == F("f3-off")) {
        digitalWrite(f3, HIGH);
      }
      else if (text == F("f4-on")) {
        digitalWrite(f4, LOW);
      }
      else if (text == F("f4-off")) {
        digitalWrite(f4, HIGH);
      }
    }
    else {
      String chat_id = String(bot.messages[i].chat_id);
      String text = bot.messages[i].text;

      // If the user asks for options
      if (text == F("/options")) {
        String keyboardJson = F("[[{ \"text\" : \"L1 ON\", \"callback_data\" : \"l1-on\" },{ \"text\" : \"L1 OFF\", \"callback_data\" : \"l1-off\" }],[{ \"text\" : \"L2 ON\", \"callback_data\" : \"l2-on\" },{ \"text\" : \"L2 OFF\", \"callback_data\" : \"l2-off\" }],[{ \"text\" : \"L3 ON\", \"callback_data\" : \"l3-on\"},{ \"text\" : \"L3 OFF\", \"callback_data\" : \"l3-off\" }],[{ \"text\" : \"L4 ON\", \"callback_data\" : \"l4-on\" },{ \"text\" : \"L4 OFF\", \"callback_data\" : \"l4-off\" }],[{ \"text\" : \"F1 ON\", \"callback_data\" : \"f1-on\" },{ \"text\" : \"F1 OFF\", \"callback_data\" : \"f1-off\" }],[{ \"text\" : \"F2 ON\", \"callback_data\" : \"f2-on\" },{ \"text\" : \"F2 OFF\", \"callback_data\" : \"f2-off\" }],[{ \"text\" : \"F3 ON\", \"callback_data\" : \"f3-on\" },{ \"text\" : \"F3 OFF\", \"callback_data\" : \"f3-off\" }],[{ \"text\" : \"F4 ON\", \"callback_data\" : \"f4-on\" },{ \"text\" : \"F4 OFF\", \"callback_data\" : \"f4-off\" }]]");
        bot.sendMessageWithInlineKeyboard(chat_id, "MVGR-GLUG ROOM AUTOMATION BUTTONS(L,F indicate Light,Fan: )", "", keyboardJson);
      }

      // If the user asks for help
      if (text == F("/start")) {
        bot.sendMessage(chat_id, "/options : Returns the buttons for ON & OFF\n", "Markdown");
      }
    }
  }
}

void loop() {
  if (millis() - bot_lasttime > BOT_MTBS) {
    int numNewMessages = bot.getUpdates(bot.last_message_received + 1);
    while (numNewMessages) {
      Serial.println("got response");
      handleNewMessages(numNewMessages);
      numNewMessages = bot.getUpdates(bot.last_message_received + 1);
    }

    bot_lasttime = millis();
  }
}
