MicroPython Example with Google IoT Core
============================================

This sample demonstrates how to use MicroPython to connect to Cloud IoT Core incorporating parts from [TrackingPrototype](https://github.com/jbrichau/TrackingPrototype).

## Quickstart with Espressif ESP32

1. Clone or download this repository

```
    git clone https://github.com/GoogleCloudPlatform/iot-core-micropython
    cd iot-core-micropython
```

2. Create a virtual environment and install the tools for ESP32.

```
    virtualenv env
    source env/bin/activate
    pip install esptool adafruit-ampy rsa
```

3. Determine the port your board is on and set an environment variable to be reused later.

```
    ls /dev/ | grep -i "tty" | grep -i "usb"
    export SERIALPORT="/dev/tty.SLAB_USBtoUART"
```

4. Test flashing your device.

```
    esptool.py --port $SERIALPORT flash_id
```

5. [Download and install MicroPython](http://micropython.org/download).

```
    esptool.py --chip esp32 --port $SERIALPORT erase_flash
    esptool.py --chip esp32 --port $SERIALPORT --baud 460800 write_flash -z 0x1000 ~/Downloads/esp32–20190529-v1.11.bin
```

6. Generate your public / private keypair.

```
    openssl genrsa -out rsa_private.pem 2048
    openssl rsa -in rsa_private.pem -pubout -out rsa_public.pem
```

7. Create your registry and device, [as described in the Cloud IoT Core documentation](https://cloud.google.com/iot/docs/how-tos/devices), using the keys from the previous step.

8. Translate the RSA key for device configuration and install the output into a new file based on `config.py.example`.

```
    cp config.py.example config.py
    python utils/decode_rsa.py >> config.py
    ...
```

9. Update config.py to use the key you appended to the file and set the Google Cloud Confiugration section of the file based on the device and registry you set up in the previous step.

10. Copy the Python sources to the device.

```
    ampy --port $SERIALPORT --baud 115200 put third_party
    ampy --port $SERIALPORT --baud 115200 put config.py
    ampy --port $SERIALPORT --baud 115200 put main.py
```

11. Connect to the device over the serial port and press reset on the device.

```
    screen -L $SERIALPORT 115200
```

Note that on machines without screen, you can use other software such as the
[Arduino IDE](https://arduino.cc) for accessing the terminal.

If everything worked you should see output similar to the following.

    Publishing message {“temp”: 113, “device_id”: “esp32-oled-1”}
    Publishing message {“temp”: 114, “device_id”: “esp32-oled-1”}
    Publishing message {“temp”: 114, “device_id”: “esp32-oled-1”}

You can read the telemetry from PubSub using the following [Google Cloud SDK](https://cloud.google.com/sdk) command.

    gcloud pubsub subscriptions pull <your-pubsub-repo> --auto-ack --limit=500

## Troubleshooting
If the device freezes around the time network initialization completes you may
want to try using a different pin for the LED. This can resolve some issues.

## Hardware target(s)
* Espressif ESP32

## Dependencies
* Some small modifications have been done to [python-rsa](https://github.com/sybrenstuvel/python-rsa) library to allow this to work on MicroPython.

## Pre-requisites
* Google Cloud Project with the Cloud IoT Core API enabled
* ESP32 compatble device

## See Also
* [Connecting MicroPython devices to Cloud IoT Core](https://medium.com/google-cloud/connecting-micropython-devices-to-google-cloud-iot-core-3680e632681e)

## License

Apache 2.0; see [LICENSE](LICENSE) for details.

## Disclaimer

This project is not an official Google project. It is not supported by Google
and Google specifically disclaims all warranties as to its quality,
merchantability, or fitness for a particular purpose.
