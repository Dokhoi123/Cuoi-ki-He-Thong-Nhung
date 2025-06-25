# **IoT Smart Air Conditioner Controller with ESP32 & Blynk**

This is an advanced embedded systems project that creates a smart, IoT-enabled controller for an air conditioner. The system monitors the ambient temperature and humidity, allows for precise remote control via the Blynk app, and features a PIR motion sensor for automatic power management. All status information is displayed locally on an LCD screen.

This system is designed for "stateful" AC remotes, where each command (e.g., setting a new temperature) is a unique IR signal.

## **Key Features**

- **Real-time Environment Monitoring:** Uses a DHT11 sensor to track and display current temperature and humidity on both the Blynk app and an LCD screen.
- **Precise Remote Control:** A slider widget in the Blynk app allows for setting the exact desired temperature. A separate button provides manual power control.
- **Motion-based Automation:** Integrates a PIR motion sensor to automatically turn the AC on when a presence is detected and turn it off after a period of inactivity to save energy.
- **Local Status Display:** A 16x2 I2C LCD screen shows the AC's current status (ON/OFF), set temperature, and live sensor readings.
- **Stateful IR Code Management:** The code uses an efficient 2D array to store and manage the unique IR code for each temperature level.

## **Hardware & Software Requirements**

### **Hardware**
- **Microcontroller:** ESP32
- **Sensors:**
    - DHT11 Temperature & Humidity Sensor.
    - PIR Motion Sensor.
- **Actuators & Display:**
    - IR LED (Transmitter).
    - 5V LED (as a motion indicator).
    - 16x2 I2C LCD Display.
- **Peripherals:** Jumper wires, Breadboard.

### **Software & Platform**
- **IoT Platform:** Blynk
- **IDE:** Arduino IDE
- **Libraries:**
    - `BlynkSimpleEsp32.h`
    - `IRremoteESP8266.h`
    - `DFRobot_DHT11.h`
    - `LiquidCrystal_I2C.h`
    - `WiFi.h`

## **Setup and Usage Instructions**

This process requires you to first capture the IR codes from your physical remote and then insert them into the main sender sketch.

### **Part 1: Capturing Each Temperature's IR Raw Code**

*(You will need a separate `IR_Receiver.ino` sketch for this part, as described previously.)*

1.  **Capture Methodically:** For this project, you must capture a separate code for each temperature level (e.g., 18°C, 19°C, ... 32°C) and one code for the "OFF" command.
2.  **Get the Raw Data:**
    - Point your AC remote at the IR receiver and press a button.
    - The Arduino Serial Monitor will print the full C++ array, for example:
      ```cpp
      // Example output:
      uint16_t rawData[583] = {438, 444, 418, 444, ...};
      ```
    - **CRITICAL STEP:** You only need to copy the **content inside the curly braces `{}`**.
      ```cpp
      // CORRECT data to copy:
      {438, 444, 418, 444, ...}
      ```
    - Save each code in a text file. You will have one for each temperature and one for the OFF button.

### **Part 2: Configuring the Sender Code (`main.cpp`)**

1.  **Update Credentials:** Open the `main.cpp` file and set your WiFi and Blynk token:
    ```cpp
    const char* ssid     = "YOUR_WIFI_SSID";
    const char* password = "YOUR_WIFI_PASSWORD";
    char auth[] = "YOUR_BLYNK_AUTH_TOKEN";
    ```

2.  **Paste the Captured Codes:**
    - Locate the `rawDataON` 2D array in the code.
    - Paste each temperature's raw code data into a new row within the main curly braces. **The order is critical.** The first row corresponds to 18°C, the second to 19°C, and so on.
      ```cpp
      // Paste the data for each temperature here, in order
      uint16_t rawDataON[15][583] = {
        { ... }, // Paste raw code for 18°C here
        { ... }, // Paste raw code for 19°C here
        { ... }, // Paste raw code for 20°C here
        // ... continue for all other temperatures
      };
      ```
    - Locate the `rawDataOFF` array and paste the code for the OFF button there.
      ```cpp
      uint16_t rawDataOFF[583] = { ... }; // Paste OFF code here
      ```

3.  **Configure Blynk App:**
    - Create a project and get your **Auth Token**.
    - Add the following widgets:
        - **Gauge (Temp):** Virtual Pin **V0**.
        - **Gauge (Humidity):** Virtual Pin **V1**.
        - **Button (Power):** Virtual Pin **V2**. Set to "Switch" mode.
        - **Slider (Set Temp):** Virtual Pin **V4**. Set range from **18** to **32**.

4.  **Upload and Run:**
    - Connect your ESP32 board, select the correct Board and COM Port in the Arduino IDE.
    - Upload the sketch.
    - The system is now ready to use.

## **Project Logic**
- The ESP32 continuously reads data from the DHT11 sensor and sends it to Blynk.
- The user can set a target temperature using the **Slider (V4)** or toggle the power using the **Button (V2)**.
- The **PIR sensor** provides automation: if it detects motion, it turns the AC on to the last set temperature. If no motion is detected for a period, it sends the OFF signal.
- The **LCD screen** provides a local display of the system's status, set temperature, and sensor data.
