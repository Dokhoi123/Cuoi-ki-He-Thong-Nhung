# **IoT Universal IR Remote Control System using ESP32 and Blynk**

This is an embedded system project that creates an IoT-based Universal Infrared (IR) Remote. The system can learn and clone signals from any physical IR remote control and then transmit those signals on command, allowing you to control devices like TVs, air conditioners, or fans from the Blynk mobile application.

## **System Architecture**

The project is divided into two main parts:
1.  **IR Receiver:** A circuit and sketch used to capture and decode signals from a physical remote.
2.  **IR Sender:** The main circuit and sketch that connects to Blynk and transmits the captured IR signals to control a device.

![System Diagram (add your image if available)](https://via.placeholder.com/600x300.png?text=Your+System+Diagram)

## **Features**

- **Universal Remote Cloning:** Can learn and replicate raw IR signals from virtually any remote control.
- **Real-time Control:** Control your devices remotely via the Blynk mobile app over WiFi.
- **IoT Integration:** A practical implementation of an Internet of Things device for smart home automation.
- **Modular Code:** Separate, easy-to-use sketches for receiving (learning) and sending (controlling).

## **Hardware & Software Requirements**

### **Hardware**
- **Microcontroller:** ESP32 (or ESP8266).
- **IR Components:**
    - IR Receiver Module (e.g., VS1838B).
    - IR LED (Transmitter).
- **Peripherals:**
    - Jumper wires.
    - Breadboard.

### **Software & Platform**
- **IoT Platform:** Blynk
- **IDE:** Arduino IDE
- **Libraries:**
    - `BlynkSimpleEsp32.h`
    - `IRremoteESP8266.h` (This library works for both ESP8266 and ESP32)
    - `WiFi.h`

## **Setup and Usage Instructions**

This project requires a two-step process: first, you capture the codes from your remote, and second, you use those codes in the main sender application.

### **Part 1: Capturing IR Raw Codes (Receiver)**

1.  **Wire the Circuit:** Connect the IR Receiver module to your ESP32.
    - `VCC` -> `3.3V`
    - `GND` -> `GND`
    - `DATA` -> `Pin D15` (or any other GPIO pin, update in the code if changed).

2.  **Upload the Receiver Code:**
    - Open the `IR_Receiver.ino` (or similarly named) sketch in the Arduino IDE.
    - Select your ESP32 board and the correct COM port.
    - Upload the sketch.

3.  **Capture the Codes:**
    - After uploading, open the **Serial Monitor** (set the baud rate to 115200).
    - Point your physical remote control (e.g., TV remote) at the IR receiver.
    - Press the button you want to clone (e.g., the "Power" button).
    - The Serial Monitor will print the captured signal as a C++ array of raw data. It will look something like this:
      ```cpp
      uint16_t rawData[67] = {9018, 4428, 608, 1614, 608, 1614, ...};
      ```
    - **COPY** the entire array (`uint16_t rawData[...] = {...};`). This is the unique signal for that button.
    - Repeat this process for every button you want to control. Save each copied array.

### **Part 2: Setting up the Sender & Controlling via Blynk**

1.  **Wire the Circuit:** Connect the IR LED to your ESP32.
    - Anode (longer leg) -> `Pin D4` (or your chosen send pin).
    - Cathode (shorter leg) -> `GND`.

2.  **Configure the Sender Code:**
    - Open the `IR_Sender.ino` (or main project) sketch.
    - **PASTE the captured raw code array** into the sketch, replacing the placeholder array. For example, if you captured the power button signal:
      ```cpp
      // Replace this placeholder array with the one you copied
      uint16_t powerSignal[67] = {9018, 4428, 608, 1614, ...};
      ```
    - Update your network and Blynk credentials in the code:
      ```cpp
      char auth[] = "YOUR_BLYNK_AUTH_TOKEN";
      char ssid[] = "YOUR_WIFI_SSID";
      char pass[] = "YOUR_WIFI_PASSWORD";
      ```

3.  **Configure the Blynk App:**
    - Create a new project in the Blynk app.
    - You will receive an **Authentication Token** via email. Paste this into the `auth[]` variable above.
    - Add a **Button** widget to your project dashboard.
    - Set the button's output to a Virtual Pin, for example, **V1**.

4.  **Upload the Sender Code:**
    - Select your ESP32 board and COM port.
    - Upload the `IR_Sender.ino` sketch.

5.  **Control Your Device:**
    - Power on the ESP32 circuit.
    - Open the Blynk project and press the "Play" icon.
    - When you press the button (V1) in the app, the ESP32 will transmit the IR signal, and your device (e.g., TV) should respond.

![image](https://github.com/user-attachments/assets/8d020380-cce1-4f6d-8767-3e839dc2863a)
