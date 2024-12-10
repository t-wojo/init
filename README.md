## Introduction
This guide is designed to help you seamlessly set up your Arduino Pro Portenta C33, run a basic "Blinky" example, integrate with the Arduino IoT Cloud, and control the onboard RGB LED, push buttons, and display the A0 value on a gauge.

## Materials Required
- Arduino Pro Portenta C33 board
- USB-C data cable (high quality)
- Wi-Fi antenna (2.4GHz)
- Computer with internet access
- Arduino account
- Arduino IDE 2.x
- Arduino Account
- Arduino Cloud Agent
- **Arduino Create Agent** (for online IDE and cloud connectivity)

---

## Installing the Arduino Create Agent (for online Arduino IDE)

The Arduino Create Agent allows your computer to communicate with the Arduino Web Editor and the Arduino IoT Cloud.

1. **Download the Arduino Create Agent**:
   - [Arduino Create Agent installation page](https://create.arduino.cc/getting-started/plugin/installation)

2. **Install the Create Agent**:
   - Run the installer and follow the on-screen instructions.
   - After installation, the agent should run in the background (look for the Arduino icon in your system tray or menu bar).

---

## Setting Up Your Portenta C33 Board

1. **Connect the Wi-Fi Antenna**:

   - Attach the provided 2.4GHz Wi-Fi antenna to the u.FL connector on the Portenta C33 board.

2. **Connect the Board to Your Computer**:

   - Use the USB-C data cable to connect the Portenta C33 to your computer.
   - Ensure the cable supports data transfer (some cables are charge-only).

3. **Select the Port in the Arduino IDE**:

   - Open the Arduino IDE.
   - Go to **Tools** > **Port** and select the port corresponding to your Portenta C33.

4. **Install the Board Support Package**:

   - In the Arduino IDE, go to **Tools** > **Board** > **Boards Manager**.
   - Search for **"Portenta C33"** or **"Arduino Mbed OS Portenta Boards"**.
   - Install the package to add support for the Portenta C33.

---

## Basic Hardware Test

Let's start with a simple test to ensure your board is communicating properly.

1. **Create a New Sketch**:

   - In the Arduino IDE, click on **File** > **New**.

2. **Enter the Following Code**:

   ```cpp
   // Basic connectivity test
   void setup() {
     Serial.begin(115200);
     while (!Serial);  // Wait for Serial to initialize
     Serial.println("Portenta C33 - Hello World!");
   }

   void loop() {
     delay(1000);
     Serial.println("System running...");
   }
   ```

3. **Upload the Sketch**:

   - Click on the **Upload** button (right-arrow icon).
   - Wait for the upload to complete.

4. **Open the Serial Monitor**:

   - Click on the **Serial Monitor** icon (magnifying glass) or go to **Tools** > **Serial Monitor**.
   - Ensure the baud rate is set to **115200**.
   - You should see the messages "**Portenta C33 - Hello World!**" and "**System running...**" printed every second.

---

## RGB LED Test

Now let's test the onboard RGB LED.

1. **Understanding the LED Pins**:

   - The Portenta C33 has built-in definitions for the RGB LED pins: `LEDR`, `LEDG`, and `LEDB`.
   - **Note**: On the Portenta C33, the LEDs are **active LOW** (writing `LOW` turns them **ON**).

2. **Create a New Sketch**:

   - Click on **File** > **New**.

3. **Enter the Following Code**:

   ```cpp
   // RGB LED Test

   void setup() {
     pinMode(LEDR, OUTPUT);  // Initialize Red LED pin
     pinMode(LEDG, OUTPUT);  // Initialize Green LED pin
     pinMode(LEDB, OUTPUT);  // Initialize Blue LED pin

     digitalWrite(LEDR, HIGH);  // Turn off Red LED
     digitalWrite(LEDG, HIGH);  // Turn off Green LED
     digitalWrite(LEDB, HIGH);  // Turn off Blue LED
   }

   void loop() {
     digitalWrite(LEDR, LOW);   // Turn on Red LED
     delay(1000);
     digitalWrite(LEDR, HIGH);  // Turn off Red LED

     digitalWrite(LEDG, LOW);   // Turn on Green LED
     delay(1000);
     digitalWrite(LEDG, HIGH);  // Turn off Green LED

     digitalWrite(LEDB, LOW);   // Turn on Blue LED
     delay(1000);
     digitalWrite(LEDB, HIGH);  // Turn off Blue LED
   }
   ```

4. **Upload the Sketch**:

   - Upload the code to your board.
   - The RGB LED should cycle through **Red**, **Green**, and **Blue**, with each color staying on for one second.

---



## Arduino IoT Cloud Integration

Integrate your Portenta C33 with the Arduino IoT Cloud to control and monitor it remotely.

### Add Your Device to the Arduino IoT Cloud

1. **Log in to the Arduino IoT Cloud**:

   - Visit [Arduino IoT Cloud](https://create.arduino.cc/iot) and log in with your Arduino account.

2. **Add a New Device**:

   - Click on **Devices** in the left sidebar.
   - Click on **Add Device** > **Set up an Arduino device**.
   - Choose **Portenta C33** from the list.
   - Follow the on-screen instructions to connect your device.
   - Double-tap the reset button if you run into issues (LED should change)

   *Note*: The Arduino Create Agent must be running for the IoT Cloud to detect your device.

---

### Create a New Thing

1. **Create a Thing**:

   - Click on **Things** in the left sidebar.
   - Click on **Create Thing**.

2. **Configure Variables**:

   - **Add Variables** for your Thing. Click on **Add variable** and configure the following:

     - **`rgbLedRed`**:
       - Type: **Integer**
       - Variable Name: **rgbLedRed**
       - Permission: **Read & Write**
       - Update Policy: **On Change**

     - **`rgbLedGreen`**:
       - Type: **Integer**
       - Variable Name: **rgbLedGreen**
       - Permission: **Read & Write**
       - Update Policy: **On Change**

     - **`rgbLedBlue`**:
       - Type: **Integer**
       - Variable Name: **rgbLedBlue**
       - Permission: **Read & Write**
       - Update Policy: **On Change**

     - **`a0Value`**:
       - Type: **Integer**
       - Variable Name: **a0Value**
       - Permission: **Read Only**
       - Update Policy: **Periodic** (e.g., every 1 second)

3. **Select Network Configuration**:

   - Under **Device**, ensure your Portenta C33 is selected.
   - Under **Network**, set up your Wi-Fi credentials:
     - **SSID**: Your Wi-Fi network name.
     - **Password**: Your Wi-Fi password.

---

### Generate and Modify the Sketch

1. **Open the Sketch Editor**:

   - In your Thing, click on **Sketch** to view the c++ code.

2. **Modify the Sketch**:

   - The Arduino IoT Cloud generates a basic sketch template. You'll need to modify it to include functionality for the RGB LED and analog input.

---

## Modifying the Sketch for Cloud Integration

1. **Include Required Libraries**:

   - The `thingProperties.h` file is already included and autogenerated.

2. **Setup Function**:

   ```cpp
   void setup() {
     Serial.begin(115200);  // Initialize serial communication
     delay(1500);           // Wait for serial monitor to open

     // Initialize pins
     pinMode(LEDR, OUTPUT);  // Initialize Red LED pin
     pinMode(LEDG, OUTPUT);  // Initialize Green LED pin
     pinMode(LEDB, OUTPUT);  // Initialize Blue LED pin
     pinMode(A0, INPUT);     // Configure analog input pin

     // Turn off LEDs
     digitalWrite(LEDR, HIGH);  // Turn off Red LED
     digitalWrite(LEDG, HIGH);  // Turn off Green LED
     digitalWrite(LEDB, HIGH);  // Turn off Blue LED

     initProperties();  // Initialize cloud properties

     ArduinoCloud.begin(ArduinoIoTPreferredConnection);  // Connect to Arduino IoT Cloud
     setDebugMessageLevel(2);       // Set debug message level (optional)
     ArduinoCloud.printDebugInfo(); // Print debug messages
   }
   ```

3. **Loop Function**:

   ```cpp
   void loop() {
     ArduinoCloud.update();  // Sync with Arduino IoT Cloud

     a0Value = analogRead(A0);  // Read analog input and assign to cloud variable

     // Control RGB LED based on cloud variables
     analogWrite(LEDR, 255 - rgbLedRed);    // Write value to Red LED
     analogWrite(LEDG, 255 - rgbLedGreen);  // Write value to Green LED
     analogWrite(LEDB, 255 - rgbLedBlue);   // Write value to Blue LED
   }
   ```

4. **Callback Functions**:

   - Callback functions are automatically generated for cloud variables if needed. ex:

     ```cpp
     void onRgbLedRedChange() {
       // Optional: Code to run when rgbLedRed changes
     }

     void onRgbLedGreenChange() {
       // Optional: Code to run when rgbLedGreen changes
     }

     void onRgbLedBlueChange() {
       // Optional: Code to run when rgbLedBlue changes
     }
     ```

   - For read-only variables like `a0Value`, you don't need callback functions.

5. **Save and Upload the Sketch**:

   - Save the modified sketch.
   - Upload it to your Portenta C33 (or similar).

---


## Creating a Cloud Dashboard

Creating a dashboard to interact with your device from the Arduino IoT Cloud.

1. **Navigate to Dashboards**:

   - Click on **Dashboards** in the left sidebar.

2. **Create a New Dashboard**:

   - Click on **Create Dashboard**.
   - Give your dashboard a name.

3. **Add Widgets**:

   - **RGB LED Control**:

     - Add three **Slider** widgets for **`rgbLedRed`**, **`rgbLedGreen`**, and **`rgbLedBlue`**.
     - Configure each slider:
       - **Link Variable**: Select the corresponding variable.
       - **Set the range** from **0** to **255**.

   - **Analog Input Display**:

     - Add a **Gauge** widget.
     - **Link Variable**: **`A0Value`**
     - **Set the range** according to your expected analog input (e.g., 0 to 1023).

4. **Test the Dashboard**:

   - Ensure your device is online and connected.
   - Move the sliders to change the RGB LED colors on your device.
   - Observe the analog input value updating in real-time on the gauge.


---



## Dashboard and Mobile App Integration
**Download the Mobile App**:
   - Download the **Arduino IoT Cloud Remote** app from the [App Store](https://apps.apple.com/app/id1454124072) (iOS) or [Google Play Store](https://play.google.com/store/apps/details?id=cc.arduino.cloud.iot) (Android).

**Log In and Control Your Device**:
   - Open the app and log in with your Arduino account.
   - Access your dashboard to control the RGB LED and observe the A0 value in real-time on your mobile device.

---
### Troubleshooting Tips
- Ensure the Arduino Cloud Agent is running in the background for seamless communication between your device and the cloud.
- If encountering issues with going online or with the dashboard, double-check your network credentials and cloud variable configurations.
- Use the `setDebugMessageLevel()` function to obtain detailed debug information, aiding in troubleshooting connection or cloud sync issues.
