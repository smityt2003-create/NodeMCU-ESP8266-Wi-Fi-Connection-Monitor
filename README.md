# NodeMCU ESP8266 Wi-Fi Connection Monitor + PIR Motion Alert
2nd Sem Collage Project

A beginner-friendly IoT/cybersecurity learning project using a **NodeMCU ESP8266**, a PIR motion sensor, an LED, and an active buzzer.

The device:
- Connects to your configured Wi-Fi network.
- Prints connection status, IP address, and signal strength (RSSI) to the Serial Monitor.
- Retries Wi-Fi connection when disconnected.
- Detects motion using a PIR sensor.
- Activates a buzzer and LED briefly when motion is detected.
- Blinks the LED while Wi-Fi is disconnected.

> **Scope:** This monitors the NodeMCU's own Wi-Fi connection. It is not a Wi-Fi scanner, packet sniffer, or tool for monitoring other people's devices.

## Contents

```text
nodemcu-wifi-monitor/
├── src/
│   └── wifi_monitor/
│       ├── wifi_monitor.ino
│       └── secrets.example.h
├── docs/
│   └── wiring.md
├── .gitignore
├── LICENSE
└── README.md
```

## 1. Parts required

- NodeMCU ESP8266 development board (ESP-12E/ESP-12F)
- HC-SR501 PIR motion sensor
- 1 LED (any common 3 mm/5 mm LED)
- 220–330 Ω resistor for the LED
- 3.3 V-compatible **active buzzer module** with low GPIO current, or a transistor driver circuit for a larger buzzer
- Breadboard and jumper wires
- USB data cable
- A 2.4 GHz Wi-Fi network (ESP8266 does not connect to 5 GHz-only Wi-Fi)

## 2. Wiring / pin mapping

All pin labels below refer to the labels printed on a typical NodeMCU board.

| Component | Component pin | NodeMCU pin | ESP8266 GPIO |
|---|---|---|---|
| PIR sensor | VCC | VIN / 5V (if available) or 3V3 per module specification | — |
| PIR sensor | GND | GND | — |
| PIR sensor | OUT | D2 | GPIO4 |
| LED | Anode (+, long leg) through 220–330 Ω resistor | D1 | GPIO5 |
| LED | Cathode (−, short leg) | GND | — |
| Active buzzer module | Signal / + | D5 | GPIO14 |
| Active buzzer module | GND / − | GND | — |

**Power and safety notes**
- Check the pin labels and specifications of your exact board and sensor before connecting power.
- Many HC-SR501 modules accept 5 V on VCC and output a logic signal around 3.3 V; verify your module. Connect all grounds together.
- Use only a low-current buzzer module suitable for a 3.3 V GPIO signal. Do not power a high-current buzzer directly from a GPIO pin; use a suitable transistor driver and separate supply if needed.
- Never apply 5 V to an ESP8266 GPIO pin.
- Disconnect USB power before changing wiring.

See [`docs/wiring.md`](docs/wiring.md) for a compact wiring checklist.

## 3. Install Arduino IDE and ESP8266 support

1. Install Arduino IDE: https://www.arduino.cc/en/software
2. Open **File → Preferences**.
3. Add this URL to **Additional Boards Manager URLs**:

   `https://arduino.esp8266.com/stable/package_esp8266com_index.json`

   If other URLs are already present, separate them with commas.
4. Open **Tools → Board → Boards Manager**.
5. Search for **esp8266** and install **esp8266 by ESP8266 Community**.
6. Connect the NodeMCU using a USB data cable.
7. Select **Tools → Board → ESP8266 Boards → NodeMCU 1.0 (ESP-12E Module)** (wording can vary slightly).
8. Select the correct port under **Tools → Port**.

## 4. Configure your Wi-Fi credentials safely

1. In the sketch folder, copy `secrets.example.h` and name the copy `secrets.h`.
2. Open `secrets.h` and replace the example values with your own 2.4 GHz Wi-Fi SSID and password.
3. Keep `secrets.h` private. The supplied `.gitignore` excludes it from Git.
4. Do not commit passwords, API keys, or other secrets to GitHub.

Example `secrets.h` format:

```cpp
#pragma once
#define WIFI_SSID "YOUR_2_4_GHZ_WIFI_NAME"
#define WIFI_PASSWORD "YOUR_WIFI_PASSWORD"
```

## 5. Upload the program

1. Open `src/wifi_monitor/wifi_monitor.ino` in Arduino IDE.
2. Ensure `secrets.h` is in the **same folder** as `wifi_monitor.ino`.
3. Select the NodeMCU board and correct serial port.
4. Click **Verify** (checkmark) to compile.
5. Click **Upload** (right arrow).
6. Open **Tools → Serial Monitor** and set the baud rate to **115200**.
7. Reset the board if needed. Wait for the PIR sensor to stabilize (often 30–60 seconds).

## 6. Test the project

### Test A — Wi-Fi connection
- Boot the device with valid Wi-Fi credentials.
- Serial Monitor should show `Wi-Fi connected`, an IP address, and RSSI in dBm.
- RSSI closer to 0 generally means a stronger signal (for example, −45 dBm is stronger than −80 dBm).

### Test B — Motion alert
- Wait for the PIR sensor to stabilize.
- Move in front of the sensor.
- The Serial Monitor should report motion and the buzzer/LED should activate briefly.
- PIR sensors may need sensitivity and delay adjustment using their onboard potentiometers.

### Test C — Wi-Fi disconnection
- Disconnect or switch off your own test access point, or temporarily enter an incorrect password for a controlled test.
- The LED should blink while the device is disconnected, and the Serial Monitor should report the connection state.
- Restore the network. The device should reconnect when the network becomes available.

## 7. Troubleshooting

| Problem | Things to check |
|---|---|
| Board/port not visible | Try a known data-capable USB cable; install the board's USB-serial driver if required; check Device Manager on Windows. |
| Wi-Fi never connects | Use 2.4 GHz Wi-Fi; verify SSID/password and signal; check that the router permits new clients. |
| `secrets.h` missing | Copy `secrets.example.h` to `secrets.h` in the same folder as the `.ino` file. |
| PIR triggers constantly | Wait for warm-up; check OUT/VCC/GND; adjust sensor potentiometers; avoid pointing at moving heat sources. |
| No buzzer sound | Confirm it is an active buzzer compatible with 3.3 V signal; check polarity and common GND; use a transistor driver for a larger buzzer. |
| LED does not light | Check LED polarity and resistor; confirm D1 wiring and common GND. |
| Upload fails | Select the right board and port; close other Serial Monitor windows; try another USB cable/port. |
| Garbled serial output | Set Serial Monitor to 115200 baud. |

## 8. Security and privacy

- Use this project only on a Wi-Fi network you own or are authorized to use.
- It monitors its own association with Wi-Fi; it does not discover clients or inspect traffic.
- Keep credentials out of source control. If a password is accidentally published, change it.
- For a real deployment, add authenticated remote status reporting, secure credential provisioning, and a safe enclosure.

## 9. GitHub upload steps

### Option A — GitHub website
1. Sign in to GitHub and create a repository named `nodemcu-wifi-monitor`.
2. Choose Public or Private as appropriate.
3. Add the project files and folders from this repository.
4. Check that `secrets.h` is **not** uploaded.
5. Commit the files.

### Option B — Git command line
Run these commands from the project folder after creating an empty GitHub repository:

```bash
git init
git add .
git status
```

Check that `secrets.h` is not listed. Then:

```bash
git commit -m "Add NodeMCU Wi-Fi monitor and PIR alert"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/nodemcu-wifi-monitor.git
git push -u origin main
```

Replace `YOUR-USERNAME` with your GitHub username. If Git asks you to authenticate, follow GitHub's current sign-in/token instructions.

## Learning outcomes

- ESP8266 GPIO mapping and sensor wiring
- Wi-Fi connection status and reconnect logic
- Serial debugging
- Basic IoT event handling
- Safe handling of project credentials
- Git/GitHub repository workflow

## License

This project is provided for educational use under the MIT License. See [`LICENSE`](LICENSE).
