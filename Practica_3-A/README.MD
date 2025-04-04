
# Práctica 3 A - Web Server en Modo Estación

## 1. Descripción
En esta práctica se implementa un servidor web en modo estación utilizando la placa ESP32-S3. El objetivo es que la placa se conecte a una red Wi-Fi y sirva una página web básica. Este ejercicio és útil para comprender el funcionamiento de periféricos de comunicación como el Wifi.

## 2. Objetivos
- Configurar la ESP32-S3 como cliente de red (modo estación).
- Implementar un servidor web básico que sirva una página HTML.
- Comprender la interacción entre cliente y servidor en una red local.

## 3. Recursos recomendados
- Fundamentos de protocolos TCP/IP y UDP:  
  https://www.tlm.unavarra.es/~daniel/docencia/lir/lir05_06/slides/1-Conceptosbasicos.pdf  
- Introducción al Wi-Fi:  
  http://www.radiocomunicaciones.net/pdf/curso-iniciacion-wifi.pdf  
- Conceptos de API REST:  
  https://aprendiendoarduino.wordpress.com/2019/10/27/api-rest/  
- Introducción a MQTT:  
  https://ricveal.com/blog/primeros-pasos-mqtt  

## 4. Materiales
- **ESP32-S3**
- **Protoboard**
- **Cable USB tipo C**
- **Red Wi-Fi disponible**

## 5. Desarrollo

### 5.1 Preparación del Entorno
1. Se utilizó el siguiente archivo `platformio.ini` para configurar el entorno:
```ini
[env:esp32-s3-devkitm-1]
platform = espressif32
board = esp32-s3-devkitm-1
framework = arduino
monitor_speed=115200
```
2. Conexión de la placa al ordenador mediante USB.

### 5.2 Programación del Microcontrolador
Código cargado en el archivo `main.cpp`:

```cpp
#include <WiFi.h>
#include <WebServer.h>

// SSID & Password
const char* ssid = "*****"; // Enter your SSID here
const char* password = "*****"; //Enter your Password here

WebServer server(80); // Object of WebServer(HTTP port, 80 is default)

void setup() {
  Serial.begin(115200);
  Serial.println("Try Connecting to ");
  Serial.println(ssid);

  // Connect to your Wi-Fi modem
  WiFi.begin(ssid, password);

  // Check if Wi-Fi is connected
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi connected successfully");
  Serial.print("Got IP: ");
  Serial.println(WiFi.localIP()); // Show ESP32 IP on serial

  server.on("/", handle_root);
  server.begin();
  Serial.println("HTTP server started");
  delay(100);
}

void loop() {
  server.handleClient();
}

// HTML content to display on web page
String HTML = "<!DOCTYPE html><html><body><h1>My Primera Pagina con ESP32 - Station Mode &#128522;</h1></body></html>";

// Handle root url (/)
void handle_root() {
  server.send(200, "text/html", HTML);
}
```

**Explicación del código:**
- Se configura la placa para conectarse a una red Wi-Fi.
- Se inicializa un servidor web que responde a la raíz `/` con una página HTML sencilla.
- La IP asignada por la red se muestra por el puerto serie.

### 5.3 Subida y Pruebas
1. Compilar y subir el código desde PlatformIO.
2. Observar en el monitor serie la IP asignada.
3. Ingresar esa IP en un navegador conectado a la misma red.
4. Visualizar la página HTML enviada por la ESP32.

## 6. Resultados
- La ESP32 se conecta exitosamente a la red Wi-Fi.
- La IP es mostrada correctamente en el monitor serie.
- Al acceder a dicha IP desde un navegador, se visualiza una página HTML simple.
- La comunicación es estable y permite futuras expansiones como control de GPIOs o lectura de sensores desde la web.
