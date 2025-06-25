# **IoT Air Conditioner (AC) Controller using ESP32 and Blynk**

This embedded system project creates an IoT-based smart remote for an air conditioner. It allows you to learn and clone signals from your existing AC remote (such as power, temperature, and mode settings) and control your AC from anywhere using the Blynk mobile application.

## **System Architecture**

The project is divided into two main parts:
1.  **IR Receiver:** A circuit and sketch used to capture and decode signals from a physical AC remote.
2.  **IR Sender:** The main circuit and sketch that connects to Blynk and transmits the captured IR signals to control the air conditioner.

## **Key Features**

- **AC Remote Cloning:** Learns and replicates raw IR signals for various air conditioner functions.
- **Remote AC Control:** Power on/off, adjust temperature, and change operating modes (e.g., Cool, Fan, Dry) via the Blynk app.
- **IoT Integration:** Turns a standard, non-smart air conditioner into a WiFi-enabled smart device.
- **Modular Code:** Separate, easy-to-use sketches for receiving (learning) and sending (controlling) signals.

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
    - `IRremoteESP8266.h` (This library also works for ESP32)
    - `WiFi.h`

## **Setup and Usage Instructions**

This project requires a two-step process: first, you capture the codes for each AC function, and second, you use those codes in the main sender application.

### **Part 1: Capturing AC IR Raw Codes (Receiver)**

1.  **Wire the Circuit:** Connect the IR Receiver module to your ESP32.
    - `VCC` -> `3.3V`
    - `GND` -> `GND`
    - `DATA` -> `Pin D15` (or any other GPIO, update in the code if changed).

2.  **Upload the Receiver Code:**
    - Open the `IR_Receiver.ino` sketch in the Arduino IDE.
    - Select your ESP32 board and the correct COM port and upload.

3.  **Capture the Codes:**
    - After uploading, open the **Serial Monitor** (set the baud rate to 115200).
    - Point your physical AC remote at the receiver.
    - Press a button you want to clone (e.g., **Power On**, **Temp Up**, **Mode Change**).
    - The Serial Monitor will print the captured signal as a C++ array. It will look like this:
      ```cpp
      uint16_t rawData[143] = {3484, 1692, 458, 414, 458, 1264, ...};
      ```
    - **COPY** the entire array.
    - **IMPORTANT:** Repeat this process for **every button function** you want to control (Power, Temp Down, Fan Speed, etc.). Save each copied array in a text file with a descriptive name.

### **Part 2: Setting up the Sender & Controlling via Blynk**

1.  **Wire the Circuit:** Connect the IR LED to your ESP32.
    - Anode (longer leg) -> `Pin D4` (or your chosen send pin).
    - Cathode (shorter leg) -> `GND`.

2.  **Configure the Sender Code:**
    - Open the `IR_Sender.ino` (main project) sketch.
    - **PASTE each captured array** into the code, replacing the placeholders. You will need multiple arrays, one for each function:
      ```cpp
      // Paste each captured signal into its corresponding array
      uint16_t powerSignal[] = { ... }; // Paste Power On/Off code here
      uint16_t tempUpSignal[] = { ... };  // Paste Temp Up code here
      uint16_t modeSignal[] = { ... };    // Paste Mode Change code here
      ```
    - Update your network and Blynk credentials:
      ```cpp
      char auth[] = "YOUR_BLYNK_AUTH_TOKEN";
      char ssid[] = "YOUR_WIFI_SSID";
      char pass[] = "YOUR_WIFI_PASSWORD";
      ```

3.  **Configure the Blynk App:**
    - Create a new project in the Blynk app and get your **Authentication Token**.
    - Add **multiple Button** widgets to your dashboard.
    - Assign each button to a different **Virtual Pin** (e.g., **V1 for Power**, **V2 for Temp Up**, **V3 for Mode**). Your code must be written to handle which signal to send based on the triggered Virtual Pin.

4.  **Upload the Sender Code:**
    - Select your ESP32 board and COM port.
    - Upload the sketch.

5.  **Control Your AC:**
    - Power on the ESP32 circuit.
    - Open the Blynk project and press the "Play" icon.
    - Pressing the buttons in the app will now send the corresponding IR signals to your air conditioner.

