# **IoT Smart Air Conditioner Controller with ESP32 & Blynk**

This is an advanced embedded systems project that creates a smart, IoT-enabled controller for an air conditioner. The system monitors the ambient temperature and humidity, allows for precise remote control via the Blynk app, and features a PIR motion sensor for automatic power management. All status information is displayed locally on an LCD screen.

This system is designed for "stateful" AC remotes, where each command (e.g., setting a new temperature) is a unique IR signal.

<img src="https://github.com/user-attachments/assets/b7590e9e-617d-40f7-90fa-41d14f31c7e6" alt="System Diagram" width="500"/>

## **Key Features**

- **Real-time Environment Monitoring:** Uses a DHT11 sensor to track and display current temperature and humidity on both the Blynk app and an LCD screen.
- **Precise Remote Control:** A slider widget in the Blynk app allows for setting the exact desired temperature. A separate button provides manual power control.
- **Motion-based Automation:** Integrates a PIR motion sensor to automatically turn the AC on when a presence is detected and turn it off after a period of inactivity to save energy.
- **Local Status Display:** A 16x2 I2C LCD screen shows the AC's current status (ON/OFF), set temperature, and live sensor readings.
- **Stateful IR Code Management:** The code uses an efficient 2D array to store and manage the unique IR code for each temperature level.

