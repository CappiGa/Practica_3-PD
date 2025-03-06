# Práctica 3 A - Web Server en Modo Estación

## Descripción
Este código configura un servidor web en el ESP32 en **Modo Estación (STA)**. El ESP32 se conecta a una red Wi-Fi y, al acceder a su dirección IP desde un navegador, muestra una página HTML simple con un mensaje.

## Código principal
`main.cpp`

## Instrucciones
1. Cargar el código en la ESP32-S3.
2. Actualizar el código con los datos correctos de tu red Wi-Fi:
   ```cpp
   const char* ssid = "TU_SSID";
   const char* password = "TU_CONTRASEÑA";
