# Práctica 3 B - Comunicación BLE con ESP32-S3

## 1. Descripción
En esta práctica se explora la capacidad de comunicación inalámbrica mediante Bluetooth Low Energy (BLE) del ESP32-S3. Se implementa un servidor BLE que permite enviar y recibir datos a través de una característica BLE, y se simula el envío periódico de datos de temperatura.

## 2. Objetivos
- Configurar el ESP32-S3 como servidor BLE.
- Establecer comunicación BLE con una aplicación móvil.
- Simular el envío de datos periódicos a través de una característica BLE.
- Visualizar datos y eventos a través de la aplicación movil.

## 3. Introducción Teórica
Bluetooth Low Energy (BLE) es una tecnología de comunicación inalámbrica de bajo consumo energético, utilizada para conexiones rápidas y estables con dispositivos móviles. En esta práctica, se emplea la biblioteca **NimBLE-Arduino**, una implementación ligera del stack BLE para ESP32.

**Referencias recomendadas:**
- [ESP32 Bluetooth Classic vs BLE](https://randomnerdtutorials.com/esp32-bluetooth-classic-arduino-ide/)
- [BLE en ESP32 con Arduino](https://randomnerdtutorials.com/esp32-ble-server-client/)
- [API REST y MQTT](https://aprendiendoarduino.wordpress.com/2019/10/27/api-rest/)

## 4. Materiales
- **ESP32-S3 DevKitC-1**
- **Cable USB tipo C**
- **Aplicación móvil Serial Bluetooth Terminal** (para comunicación BLE)
- **PC con PlatformIO instalado**

## 5. Desarrollo

### 5.1 Preparación del Entorno
1. Crear un proyecto en PlatformIO
2. Configurar el archivo `platformio.ini` con el siguiente contenido:

```
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
monitor_speed = 115200
build_flags = 
    -DCORE_DEBUG_LEVEL=5
    -DARDUINO_USB_MODE=1
    -DARDUINO_USB_CDC_ON_BOOT=1
lib_deps =
    h2zero/NimBLE-Arduino @ ^1.4.0
```

### 5.2 Programación del Microcontrolador
El código implementado crea un servidor BLE con un servicio y una característica personalizadas. Se notifican valores simulados de temperatura cada 2 segundos.

```cpp
#include <Arduino.h>
#include <BLEDevice.h>
#include <BLEServer.h>
#include <BLEUtils.h>
#include <BLE2902.h>

#define SERVICE_UUID        "4fafc201-1fb5-459e-8fcc-c5c9c331914b"
#define CHARACTERISTIC_UUID "beb5483e-36e1-4688-b7f5-ea07361b26a8"

BLEServer* pServer = NULL;
BLECharacteristic* pCharacteristic = NULL;
bool deviceConnected = false;
bool oldDeviceConnected = false;

float temperature = 0;
unsigned long previousMillis = 0;
const long interval = 2000;

class MyServerCallbacks: public BLEServerCallbacks {
    void onConnect(BLEServer* pServer) {
      deviceConnected = true;
      Serial.println("Device connected");
    }
    void onDisconnect(BLEServer* pServer) {
      deviceConnected = false;
      Serial.println("Device disconnected");
    }
};

class CharacteristicCallbacks: public BLECharacteristicCallbacks {
    void onWrite(BLECharacteristic *pCharacteristic) {
      std::string value = pCharacteristic->getValue();
      if (value.length() > 0) {
        Serial.println("Received data: ");
        for (int i = 0; i < value.length(); i++) {
          Serial.print(value[i]);
        }
        Serial.println();
      }
    }
};

void setup() {
  Serial.begin(115200);
  Serial.println("Starting BLE application");

  BLEDevice::init("Yatusah");
  pServer = BLEDevice::createServer();
  pServer->setCallbacks(new MyServerCallbacks());

  BLEService *pService = pServer->createService(SERVICE_UUID);

  pCharacteristic = pService->createCharacteristic(
                      CHARACTERISTIC_UUID,
                      BLECharacteristic::PROPERTY_READ   |
                      BLECharacteristic::PROPERTY_WRITE  |
                      BLECharacteristic::PROPERTY_NOTIFY |
                      BLECharacteristic::PROPERTY_INDICATE
                    );
  pCharacteristic->addDescriptor(new BLE2902());
  pCharacteristic->setCallbacks(new CharacteristicCallbacks());
  pService->start();

  BLEAdvertising *pAdvertising = BLEDevice::getAdvertising();
  pAdvertising->addServiceUUID(SERVICE_UUID);
  pAdvertising->setScanResponse(true);
  pAdvertising->setMinPreferred(0x06);
  pAdvertising->setMinPreferred(0x12);
  BLEDevice::startAdvertising();
  
  Serial.println("BLE service started. Waiting for a client connection...");
}

void loop() {
  unsigned long currentMillis = millis();
  if (currentMillis - previousMillis >= interval) {
    previousMillis = currentMillis;
    temperature = random(2000, 3000) / 100.0;
    if (deviceConnected) {
      String tempStr = String(temperature, 2);
      pCharacteristic->setValue(tempStr.c_str());
      pCharacteristic->notify();
      Serial.print("Temperature: ");
      Serial.println(tempStr);
    }
  }

  if (!deviceConnected && oldDeviceConnected) {
    delay(500);
    pServer->startAdvertising();
    Serial.println("Started advertising again");
    oldDeviceConnected = deviceConnected;
  }
  if (deviceConnected && !oldDeviceConnected) {
    oldDeviceConnected = deviceConnected;
  }
  delay(10);
}
```

**Explicación del código:**
- Se crea un servicio BLE con una característica para lectura, escritura y notificaciones.
- Se envía un valor simulado de temperatura cada 2 segundos si hay un dispositivo conectado.
- Se maneja la conexión y desconexión de dispositivos móviles mediante callbacks.

### 5.3 Comunicación con la Aplicación Móvil
1. Instalar la app **Serial Bluetooth Terminal** en el móvil.
2. Activar el Bluetooth y buscar el dispositivo "Yatusah".
3. Emparejar y conectar para comenzar a recibir datos de temperatura.

## 6. Resultados
- El dispositivo ESP32-S3 inicia un servidor BLE con nombre **Yatusah**.
- Desde la app móvil se logra conectar y recibir valores actualizados de temperatura cada 2 segundos.
- La comunicación es estable y bidireccional (se puede enviar texto desde la app y verlo en el monitor serie).


