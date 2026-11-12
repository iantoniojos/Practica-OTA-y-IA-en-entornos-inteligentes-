# ESP32: OTA e IA para gestión de dispositivos

Proyecto académico (Universidad del Cauca, Ingeniería Electrónica y Telecomunicaciones).
Código documentado con Doxygen, desarrollado con **ESP-IDF** (v5.2 o superior).

## 1. OTA (`1_ota_esp32/`)
Actualización de firmware por Wi-Fi vía HTTPS con `esp_https_ota` y un servidor local en Python.

1. `cd 1_ota_esp32/servidor` y generar el certificado (cambie la IP por la de su PC):
   `openssl req -x509 -newkey rsa:2048 -keyout server.key -out server.crt -days 365 -nodes -subj "/CN=192.168.1.50" -addext "subjectAltName=IP:192.168.1.50"`
2. Copiar `server.crt` a `1_ota_esp32/main/`.
3. Editar `OTA_URL` en `main/main.c` con la misma IP.
4. `idf.py menuconfig` -> *Example Connection Configuration* (SSID y clave).
5. Compilar y flashear v1.0.0: `idf.py build flash monitor`.
6. Cambiar `PROJECT_VER` a `2.0.0`, `idf.py build`, copiar `build/ota_esp32.bin` a `servidor/firmware_v2.bin`.
7. Ejecutar `python3 server_ota.py` y reiniciar el ESP32: descarga, reinicia y arranca la v2.0.0.

## 2. IA en el ESP32 (`2_ia_gestion_dispositivos/`)
Nodo de monitoreo ambiental con **TinyML** (SHT31 + MicroSD) que genera insights localmente:

- **Health Check** del sensor (comunicación/CRC, rango, saltos bruscos).
- **Punto de rocío y VPD** calculados en el dispositivo.
- **Detección de anomalías**: distancia L2 sobre Z-Score (`env_insights.c`).
- **Régimen ambiental**: K-Means con centroides pre-entrenados (`kmeans_regime.c`).
- **Compresión inteligente de registros**: guarda cada 10 min si todo es estable; ante
  anomalía lee cada 5 s y marca el evento; también registra al cambiar de clúster.

Conexiones: SHT31 (SDA=GPIO21, SCL=GPIO22), MicroSD por SPI (MOSI=23, MISO=19, CLK=18, CS=5).
Sin sensor, ponga `SIMULATE_SENSOR` en 1 en `main.c`.

`cd 2_ia_gestion_dispositivos && idf.py build flash monitor` (los datos quedan en `/sdcard/datos.csv`).

## Documentación Doxygen
Desde esta carpeta: `doxygen Doxyfile` y abrir `docs/html/index.html`.
