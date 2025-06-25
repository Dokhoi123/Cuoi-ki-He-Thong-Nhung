# **IoT Stateful Air Conditioner (AC) Controller using ESP32 and Blynk**

This is an embedded system project that creates an IoT-based smart remote for a stateful air conditioner. This type of AC remote sends a complete state signal (including temperature, mode, and fan speed) with every button press.

The system learns these unique signals and allows you to select a specific temperature using a slider in the Blynk mobile app, giving you precise control over your AC from anywhere.

## **System Architecture**

The project is divided into two main parts:
1.  **IR Receiver:** A circuit and sketch used to capture and decode the unique signal for **each temperature level** from the physical AC remote.
2.  **IR Sender:** The main circuit and sketch that connects to Blynk, interprets the value from a slider widget, and transmits the corresponding pre-recorded IR signal.

## **Key Features**

- **Stateful IR Signal Cloning:** Learns and replicates the complete state-based IR signal for each desired temperature setting.
- **Precise Temperature Control:** Uses a slider in the Blynk app for intuitive and precise temperature selection (e.g., from 18°C to 30°C).
- **IoT Integration:** Turns a standard, stateful-remote air conditioner into a smart, WiFi-enabled device.
- **Scalable Code Structure:** The code is designed to easily add more temperature codes as they are captured.

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
    - `IRremoteESP8266.h` (Works for both ESP8266 and ESP32)
    - `WiFi.h`

## **Setup and Usage Instructions**

This project requires a methodical, two-step process. You must capture a separate IR code for every single temperature you wish to control.

### **Part 1: Capturing Each Temperature's IR Raw Code**

1.  **Wire the Receiver Circuit:** Connect the IR Receiver module to your ESP32 (`VCC`->`3.3V`, `GND`->`GND`, `DATA`->`Pin D15`).
2.  **Upload the Receiver Code:** Open `IR_Receiver.ino` in the Arduino IDE and upload it to the ESP32.
3.  **Capture Codes Methodically:**
    - Open the **Serial Monitor** (baud rate 115200).
    - **Set your physical AC remote to the first temperature**, for example, **24°C** (ensure mode and fan speed are also set as desired).
    - Point the remote at the receiver and press the power/send button once.
    - The Serial Monitor will print the captured signal as a C++ array.
      ```cpp
      // This is the code for 24 DEGREES
      uint16_t rawData[143] = {3484, 1692, 458, ...};
      ```
    - **COPY** this entire array and save it in a text file, labeling it `temp_24_signal`.
    - **Now, set your physical remote to 25°C** and press the button again. A **new, different** code will be generated.
    - **COPY** this new array and save it as `temp_25_signal`.
    - **REPEAT** this process for every single temperature level you want to control (e.g., 18°C, 19°C, ..., 30°C).

### **Part 2: Setting up the Sender & Controlling via Blynk**

1.  **Wire the Sender Circuit:** Connect the IR LED to your ESP32 (`Anode`->`Pin D4`, `Cathode`->`GND`).
2.  **Configure the Sender Code:**
    - Open the `IR_Sender.ino` sketch.
    - **Create a separate array for each temperature code** you captured. Paste the data you saved into the corresponding array:
      ```cpp
      // Paste each captured signal into its corresponding array
      uint16_t temp24Signal[] = {3484, 1692, ...}; // Code for 24°C
      uint16_t temp25Signal[] = {3490, 1680, ...}; // Code for 25°C
      uint16_t temp26Signal[] = {3488, 1688, ...}; // Code for 26°C
      // ...and so on for all other temperatures
      ```
    - Update your network and Blynk credentials:
      ```cpp
      char auth[] = "YOUR_BLYNK_AUTH_TOKEN";
      char ssid[] = "YOUR_WIFI_SSID";
      char pass[] = "YOUR_WIFI_PASSWORD";
      ```
    - Ensure your code has a `BLYNK_WRITE` function with a `switch` statement to handle the slider input:
      ```cpp
      BLYNK_WRITE(V1) { // Assuming your slider is on Virtual Pin V1
        int temp = param.asInt(); // Get temperature value from slider

        switch (temp) {
          case 24:
            irsend.sendRaw(temp24Signal, sizeof(temp24Signal)/sizeof(temp24Signal[0]), 38);
            break;
          case 25:
            irsend.sendRaw(temp25Signal, sizeof(temp25Signal)/sizeof(temp25Signal[0]), 38);
            break;
          // ... add a 'case' for every other temperature you have
        }
      }
      ```

3.  **Configure the Blynk App:**
    - Create a new project and get your **Authentication Token**.
    - Add a **Slider** widget to your dashboard.
    - Configure the slider:
        - **OUTPUT:** Set it to the Virtual Pin you used in the code (e.g., **V1**).
        - **RANGE:** Set the min and max values to match the temperatures you captured (e.g., **18** to **30**).

4.  **Upload and Control:**
    - Upload the `IR_Sender.ino` sketch to the ESP32.
    - Power on the circuit, open the Blynk app, and press "Play".
    - Moving the slider will now send the specific IR command for that temperature to your AC.

## **Author**
* **Full Name:** [Your Name]
* **Student ID:** [Your Student ID]
* **Class:** [Your Class]
