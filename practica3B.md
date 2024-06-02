# Pràctica 3

## Pràctica B: Comunicació Bluetooth amb el mòbil

### Objectius
L'objectiu de la pràctica és comprendre el funcionament del Bluetooth en l'ESP32. Es realitzarà una comunicació sèrie amb una aplicació mòbil utilitzant Bluetooth.

### Codi
Aquest codi crea un pont entre el sèrie i el Bluetooth clàssic (SPP).

```cpp
#include "BluetoothSerial.h"

#if !defined(CONFIG_BT_ENABLED) || !defined(CONFIG_BLUEDROID_ENABLED)
#error Bluetooth no està habilitat! Si us plau, executa `make menuconfig` per habilitar-lo
#endif

BluetoothSerial SerialBT;

//inicialitza la comunicació sèrie i el Bluetooth amb el nom "ESP32test".
void setup() {
  Serial.begin(115200);
  SerialBT.begin("ESP32test");  // Nom del dispositiu Bluetooth
  Serial.println("El dispositiu ha començat, ara pots emparellar-lo amb Bluetooth!");
}

//Bucle principal, comprova si hi ha dades disponibles al sèrie o al Bluetooth i les transmet a l'altre interfície.
void loop() {
  if (Serial.available()) {
    SerialBT.write(Serial.read());
  }
  if (SerialBT.available()) {
    Serial.write(SerialBT.read());
  }
  delay(20);
}

Sortides a través de la impressió sèrie:

El dispositiu ha començat, ara pots emparellar-lo amb Bluetooth!
```

### Exercici de millora de nota
1. Connexió WiFi en mode AP
Modificar el codi per configurar la ESP32 en mode AP.

Aquest codi configura ESP32 com a punt d'accés (AP) amb el SSID "ESP32-Access-Point" i la contrasenya "12345678". Els clients poden connectar-se directament a ESP32 i accedir a la pàgina web generada.

```cpp
#include <WiFi.h>
#include <WebServer.h>

// SSID i Contrasenya
const char* ssid = "ESP32-Access-Point";
const char* password = "12345678";

WebServer server(80);

void setup() {
  Serial.begin(115200);
  Serial.println("Configura l'ESP32 en mode AP");

  // Configura l'ESP32 en mode AP
  WiFi.softAP(ssid, password);

  IPAddress IP = WiFi.softAPIP();
  Serial.print("AP IP address: ");
  Serial.println(IP);

  server.on("/", handle_root);
  server.begin();
  Serial.println("Servidor HTTP iniciat en mode AP");
}

void loop() {
  server.handleClient();
}

String HTML = "<!DOCTYPE html>\
<html>\
<body>\
<h1>La Meva Primera Pàgina amb ESP32 - Mode AP &#128522;</h1>\
</body>\
</html>";

void handle_root() {
  server.send(200, "text/html", HTML);
}