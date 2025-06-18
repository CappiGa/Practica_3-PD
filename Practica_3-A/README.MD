
# Práctica 3 A - Web Server en Modo Estación

## 1. Descripción
En esta práctica se implementa un servidor web utilizando la ESP32 en modo estación (STA). El objetivo principal es explorar la conectividad WiFi desde la ESP32, a través de un ejemplo práctico en el que se despliega una página web accesible desde cualquier dispositivo conectado a la misma red.

## 2. Objetivos
- Comprender el funcionamiento del módulo WiFi en la ESP32 en modo estación.
- Implementar un servidor web básico que responda a peticiones HTTP.

## 3. Introducción Teórica
Para poder trabajar con redes WiFi y tecnologías inalámbricas en general, es necesario tener una base conceptual en protocolos de comunicación como TCP/IP y UDP. En esta práctica, se recomienda como lectura previa los siguientes enlaces:

- Protocolos TCP/IP y UDP: [Conceptos básicos](https://www.tlm.unavarra.es/~daniel/docencia/lir/lir05_06/slides/1-Conceptosbasicos.pdf)
- Fundamentos de WiFi: [Iniciación WiFi](http://www.radiocomunicaciones.net/pdf/curso-iniciacion-wifi.pdf)
- Introducción a API REST: [API REST Arduino](https://aprendiendoarduino.wordpress.com/2019/10/27/api-rest/)
- Introducción a MQTT: [Primeros pasos MQTT](https://ricveal.com/blog/primeros-pasos-mqtt)

## 4. Materiales
- **ESP32-S3**
- **Cable USB tipo C**
- **Red WiFi disponible**

## 5. Desarrollo

### 5.1 Preparación del Entorno
1. Crear un nuevo proyecto en PlatformIO.
2. Configurar el archivo `platformio.ini` con el siguiente contenido:
```ini
[env:esp32-s3-devkitm-1]
platform = espressif32
board = esp32-s3-devkitm-1
framework = arduino
monitor_speed=115200
```

### 5.2 Programación del Microcontrolador
Se utilizó el siguiente código para configurar un servidor web básico que responde a la ruta raíz con una página HTML sencilla.

```cpp
#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "Nautilus";
const char* password = "20000Leguas";

WebServer server(80);

void handle_root();

void setup() {
    Serial.begin(115200);
    Serial.println("Try Connecting to ");
    Serial.println(ssid);
    WiFi.begin(ssid, password);

    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.print(".");
    }

    Serial.println("");
    Serial.println("WiFi connected successfully");
    Serial.print("Got IP: ");
    Serial.println(WiFi.localIP());

    server.on("/", handle_root);
    server.begin();
    Serial.println("HTTP server started");
    delay(100);
}

void loop() {
    server.handleClient();
}

String HTML = "<!DOCTYPE html><html><body><h1>My Primera Pagina con ESP32 - Station Mode &#128522;</h1></body></html>";

void handle_root() {
    server.send(200, "text/html", HTML);
}
```

**Explicación del código:**
- Se conecta a una red WiFi con las credenciales definidas.
- Muestra la IP asignada por DHCP.
- Crea un servidor web en el puerto 80 y define la respuesta para la ruta `/`.
- Muestra un mensaje HTML al acceder desde un navegador en la red.

### 5.3 Subida del Código al ESP32
1. Conectar la ESP32-S3 al ordenador.
2. Compilar y subir el código usando el botón de "Upload" en PlatformIO.
3. Abrir el monitor serie para verificar la conexión y la IP.
4. Desde un navegador en la misma red, acceder a la dirección IP mostrada en el monitor serie.

## 6. Resultados
- El servidor web inicia correctamente y responde a las peticiones desde dispositivos en la misma red.
- La página web muestra el mensaje: “My Primera Pagina con ESP32 - Station Mode 😊”.
![Image](https://github.com/user-attachments/assets/50a8c1b9-be9b-4ef0-87f4-9a9f19100bc3)
- Se verificó que la ESP32 recibe una IP válida y que la conexión es estable.
