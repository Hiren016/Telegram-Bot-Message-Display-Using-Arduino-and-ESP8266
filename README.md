# 📱 Telegram Bot Message Display Using Arduino and ESP8266

**Technologies / Tools:**  
Arduino Uno, ESP8266 NodeMCU, Telegram Bot API, LCD I2C Display, C++, Arduino IDE, IoT Communication, Real-Time Messaging, WiFi Integration

---

## 📝 Project Overview

**Telegram Bot Message Display** is an **IoT-based real-time notification system** developed for the **Industrial Internet of Everything (IIOE) Laboratory** course. The project demonstrates seamless integration between **cloud messaging platforms, wireless communication modules, and embedded display hardware** to create a functional remote message display system.

The system enables users to send text messages via a Telegram Bot, which are instantly retrieved by an ESP8266 WiFi module, processed by an Arduino Uno, and displayed on a 16x2 LCD screen. This prototype mirrors real-world applications such as railway station information boards, smart home notification panels, and industrial monitoring systems.

---

## ❓ Problem Statement

Modern IoT applications require efficient communication between cloud services and embedded devices for real-time information dissemination. Key challenges include:

- 🌐 **Remote Accessibility**: Need for systems that can be controlled from anywhere via internet
- ⚡ **Real-Time Updates**: Instant message delivery without manual intervention
- 💰 **Cost-Effective Solutions**: Affordable hardware for academic and industrial prototyping
- 🔌 **Simple Integration**: Easy-to-implement architecture for learning and demonstration
- 📊 **Visual Feedback**: Clear display of information on physical hardware

**This project addresses these challenges** by creating a lightweight, affordable, and scalable IoT notification system that bridges cloud messaging and embedded displays through standard communication protocols.

---

## 🎯 IIOE Significance

### Industrial Internet of Everything Integration

This project exemplifies core IIOE principles by demonstrating:

**🔗 Device Interconnectivity**
- Seamless communication between cloud platforms (Telegram), wireless modules (ESP8266), and microcontrollers (Arduino)
- Real-time data exchange across internet-connected devices
- Standardized API-based integration

**📡 Wireless Communication**
- WiFi-enabled message retrieval and processing
- Internet-based remote control capabilities
- Secure bot-to-device communication channel

**🏭 Industrial Applications**
- Foundation for factory floor notification systems
- Remote monitoring and alert mechanisms
- Scalable architecture for larger industrial displays

**🔄 Real-Time Data Processing**
- Instant message parsing and display
- Continuous polling and update mechanisms
- Serial communication between hardware modules

---

## 🛠️ System Architecture & Implementation

### 🔹 Hardware Components

| Component | Role | Specifications |
|-----------|------|----------------|
| **Arduino Uno** | Main controller for display logic | ATmega328P, 16MHz, 5V operation |
| **ESP8266 NodeMCU** | WiFi communication module | 802.11 b/g/n, 80MHz, 3.3V logic |
| **16x2 LCD Display** | Visual output interface | I2C interface, 16 characters × 2 lines |
| **Breadboard** | Component mounting platform | Standard 830-point breadboard |
| **Jumper Wires** | Electrical connections | Male-to-Female configuration |
| **USB Cable** | Power supply and programming | Type A to Type B |

### 🔹 Software Stack

- **Arduino IDE**: Development environment for code compilation and upload
- **Telegram Bot API**: Cloud messaging interface for remote communication
- **ESP8266WiFi Library**: WiFi connectivity management
- **UniversalTelegramBot Library**: Telegram bot message handling
- **LiquidCrystal_I2C Library**: LCD display control via I2C protocol
- **WiFiClientSecure**: Secure HTTPS communication

### 🔹 Communication Flow
```
User → Telegram App → Telegram Servers → ESP8266 (WiFi) → Arduino (Serial) → LCD Display
```

1. **Message Input**: User sends text via Telegram bot interface
2. **API Polling**: ESP8266 continuously polls Telegram servers for new messages
3. **Authentication**: Chat ID verification ensures authorized access
4. **Data Transfer**: Message forwarded to Arduino via serial communication (TX/RX)
5. **Display Processing**: Arduino formats text for 16x2 LCD constraints
6. **Visual Output**: Message rendered on LCD with automatic line wrapping

---

## 🔧 Technical Implementation

### 🔹 Circuit Design

**Pin Configuration:**

**Arduino Uno Connections:**
```
5V     → LCD VCC, Breadboard Power Rail
GND    → LCD GND, Breadboard Ground Rail
TX(1)  → ESP8266 RX
RX(0)  → ESP8266 TX
D7     → LCD RS (Register Select)
D8     → LCD EN (Enable)
D9-D12 → LCD D4-D7 (4-bit Data Lines)
A0     → Potentiometer (Contrast Control)
```

**ESP8266 NodeMCU Connections:**
```
VCC    → 3.3V Power Supply
GND    → Common Ground
TX     → Arduino RX (Pin 0)
RX     → Arduino TX (Pin 1)
EN     → 3.3V (Module Enable)
D1     → LCD SCL (I2C Clock)
D2     → LCD SDA (I2C Data)
```

**LCD Display Connections:**
```
VCC    → 5V Power Rail
GND    → Ground Rail
SDA    → ESP8266 D2 (GPIO4)
SCL    → ESP8266 D1 (GPIO5)
V0     → Potentiometer Output (Contrast)
```

### 🔹 Code Architecture

**Core Functionality:**
```cpp
// WiFi & Telegram Configuration
const char* ssid = "Your_WiFi_SSID";
const char* password = "Your_WiFi_Password";
#define BOT_TOKEN "Your_Telegram_Bot_Token"
#define CHAT_ID "Your_Authorized_Chat_ID"

// I2C LCD Initialization (0x27 address, 16x2 size)
LiquidCrystal_I2C lcd(0x27, 16, 2);

// ESP8266 I2C Configuration
Wire.begin(D2, D1); // SDA = D2, SCL = D1

// Message Handling Logic
void handleNewMessages(int numNewMessages) {
  for (int i = 0; i < numNewMessages; i++) {
    String text = bot.messages[i].text;
    String chat_id = bot.messages[i].chat_id;
    
    // Authorization Check
    if (chat_id != CHAT_ID) {
      bot.sendMessage(chat_id, "Unauthorized!", "");
      return;
    }
    
    // LCD Display Logic
    lcd.clear();
    if (text.length() <= 16) {
      lcd.print(text);
    } else {
      lcd.print(text.substring(0, 16));   // Line 1
      lcd.setCursor(0, 1);
      lcd.print(text.substring(16, 32));  // Line 2
    }
    
    // Confirmation Message
    bot.sendMessage(chat_id, "Text displayed:\n" + text, "");
  }
}

// Continuous Polling Loop
void loop() {
  if (millis() - lastTimeBotRan > botRequestDelay) {
    int newMessages = bot.getUpdates(bot.last_message_received + 1);
    while (newMessages) {
      handleNewMessages(newMessages);
      newMessages = bot.getUpdates(bot.last_message_received + 1);
    }
    lastTimeBotRan = millis();
  }
}
```

**Key Implementation Features:**
- **Secure Communication**: WiFiClientSecure for HTTPS encryption
- **Authorization**: Chat ID verification prevents unauthorized access
- **Text Formatting**: Automatic line wrapping for 16-character display limit
- **Polling Mechanism**: 1.5-second interval for message checking
- **Serial Debugging**: Real-time monitoring via Serial Monitor (115200 baud)
- **I2C Protocol**: Reduced wiring complexity using 2-wire communication

---

## 📊 System Capabilities

### ✅ Core Features

- ⚡ **Real-Time Messaging**: Instant message retrieval and display (< 2 seconds latency)
- 🌐 **Remote Control**: Send messages from anywhere with internet connectivity
- 🔒 **Secure Access**: Chat ID-based authorization prevents unauthorized users
- 📱 **User-Friendly Interface**: Simple Telegram app-based message sending
- 🔄 **Automatic Updates**: Continuous polling ensures no messages are missed
- 📺 **Visual Feedback**: Clear 16x2 LCD display with adjustable contrast
- 🛡️ **Error Handling**: Unauthorized access alerts and message validation

### 🎯 Technical Specifications

- **Display Capacity**: 32 characters (16×2 lines) with automatic wrapping
- **Polling Interval**: 1.5 seconds (configurable)
- **WiFi Standard**: 802.11 b/g/n (2.4 GHz)
- **Communication Protocol**: HTTPS (TLS encrypted)
- **Power Consumption**: ~200mA (Arduino) + ~80mA (ESP8266) + ~20mA (LCD)
- **Operating Voltage**: 5V (Arduino/LCD), 3.3V (ESP8266)
- **Serial Baud Rate**: 115200 bps

---

## 📈 Results & Demonstration

### ✅ Successful Implementation

**Connection Establishment:**
- ✓ WiFi connection established successfully
- ✓ ESP8266 authenticated with Telegram servers
- ✓ I2C communication verified between ESP8266 and LCD
- ✓ Serial communication confirmed between Arduino and ESP8266

**Message Flow Validation:**
- ✓ Messages sent via Telegram bot received instantly
- ✓ Text displayed correctly on LCD with proper formatting
- ✓ Long messages wrapped across two lines automatically
- ✓ Unauthorized access attempts blocked successfully

**Performance Metrics:**
- 📡 Average message delivery time: **1.8 seconds**
- 🔄 Successful message display rate: **100%**
- 🌐 WiFi connection stability: **Consistent across tests**
- 🔒 Authorization system: **No false positives/negatives**

### 📸 Output Screenshots

**System States Captured:**
1. **Actual Hardware Setup**: Complete circuit assembled on breadboard
2. **WiFi Module Connected**: ESP8266 successfully joined network
3. **Telegram Message Sent**: User interface showing sent message
4. **Serial Monitor Output**: Real-time debugging showing received message
5. **LCD Display Output**: Final message rendered on 16x2 screen

**[Video Demonstration Available]**: Live working demonstration of complete message flow

---

## 🚀 Applications & Use Cases

### 🏭 Industrial Applications

**Factory Automation:**
- Production line status updates
- Shift change notifications
- Equipment maintenance alerts
- Safety warnings and emergency messages

**Remote Monitoring:**
- Supervisor-to-worker communication
- Machine status displays
- Inventory alerts
- Quality control notifications

### 🏢 Commercial Applications

**Public Information Systems:**
- Railway station platform displays
- Bus depot arrival/departure boards
- Airport gate information panels
- Hospital room status indicators

**Office Environments:**
- Meeting room availability displays
- Announcement boards
- Task reminder panels
- Reception area notifications

### 🏠 Personal & Educational Applications

**Smart Home Integration:**
- Family message boards
- Medication reminders
- Calendar event notifications
- Doorbell alerts

**Academic Demonstrations:**
- IoT concept teaching
- Wireless communication labs
- Embedded systems projects
- API integration exercises

### 🔬 Research & Development

**Prototyping Platform:**
- IoT proof-of-concept development
- Communication protocol testing
- Display system validation
- Automation workflow design

---

## ⚡ Advantages

### 💪 Technical Strengths

- 🌐 **Remote Accessibility**: Control from anywhere with internet connection
- ⚡ **Real-Time Performance**: Instant message delivery and display
- 💰 **Cost-Effective**: Total hardware cost under ₹1,225
- 🔧 **Easy Implementation**: Simple wiring and straightforward programming
- 🔄 **Modular Design**: Components can be easily replaced or upgraded
- 🔒 **Secure Communication**: Telegram bot authentication and HTTPS encryption
- 📊 **Visual Feedback**: Clear LCD display for immediate confirmation
- 🛠️ **Expandable**: Foundation for adding sensors, automation, or larger displays

### 🎯 Practical Benefits

- No need for complex networking infrastructure
- Works with existing WiFi networks
- No additional hardware required beyond basic components
- Open-source libraries and tools reduce development time
- Compatible with standard Arduino ecosystem
- Suitable for rapid prototyping and experimentation

---

## ⚠️ Limitations

### 🔧 Technical Constraints

- 📶 **WiFi Dependency**: Requires stable internet connection for operation
- 📺 **Limited Display**: 16x2 LCD constrains message length (32 characters max)
- ⏱️ **API Rate Limits**: Telegram polling interval introduces slight delays
- ⚡ **Processing Speed**: ESP8266 may lag with rapid consecutive messages
- 🔌 **Power Requirements**: No battery backup, requires continuous wired power
- 🌞 **Visibility Issues**: LCD difficult to read in bright sunlight
- 📡 **Serial Noise**: Potential interference in electrically noisy environments
- 📏 **Scalability**: Not suitable for large-scale commercial deployments

### 🎯 Use Case Restrictions

- Best suited for small-scale notification systems
- Not designed for high-traffic environments
- Limited to text-only messages (no images/multimedia)
- Single-user control (one authorized chat ID)
- Requires manual setup for WiFi credential changes

---

## 💰 Project Costing

| Component | Quantity | Unit Cost (INR) |
|-----------|----------|----------------|
| Arduino Uno | 1 | ₹335 |
| ESP8266 WiFi Module | 1 | ₹320 |
| 16x2 LCD Display (I2C) | 1 | ₹218 |
| Jumper Wires (M-F) | 1 set | ₹80 |
| Breadboard | 1 | ₹150 |
| USB Cable | 1 | ₹120 |
| Telegram App | 1 | **Free** |
| Arduino IDE | 1 | **Free** |
| **Total Project Cost** | | **₹1,223** |

**Cost Breakdown:**
- 🖥️ Hardware Components: ₹1,103 (90%)
- 🔌 Accessories: ₹120 (10%)
- 💻 Software Tools: ₹0 (Free)

*Affordable pricing makes this project ideal for educational institutions, hobbyists, and industrial prototyping.*

---

## 🎓 Skills Demonstrated

### Technical Competencies

- **IoT Architecture Design**: Integration of cloud services with embedded hardware
- **Wireless Communication**: WiFi protocol implementation and management
- **API Integration**: Telegram Bot API interaction and message handling
- **Embedded Programming**: Arduino/ESP8266 firmware development in C++
- **Serial Communication**: UART-based data transfer between microcontrollers
- **Display Control**: LCD interfacing using I2C protocol
- **Circuit Design**: Hardware assembly and pin configuration
- **Debugging**: Serial monitoring and troubleshooting techniques

### Soft Skills

- **Problem-Solving**: Addressing communication latency and display constraints
- **Documentation**: Comprehensive technical report and README creation
- **Project Management**: Component selection and cost optimization
- **Team Collaboration**: Group-based development and testing

---

## 🔮 Future Enhancements

### Potential Upgrades

- 📱 Add larger OLED or TFT displays for more text capacity
- 🔋 Implement battery backup for portable operation
- 📊 Include sensor integration (temperature, humidity) for monitoring displays
- 🎨 Add RGB LED indicators for message type classification
- 🔊 Integrate buzzer for audio notifications
- 🌐 Support multiple authorized users with role-based access
- 💾 Add SD card logging for message history
- 🔄 Implement scrolling text for longer messages
- 📡 Enable bidirectional communication (send status back to Telegram)
- 🏗️ Scale to multiple display nodes in a network

---

## 🎯 Why This Project Matters

### Educational Value

- 📚 **Practical IoT Learning**: Hands-on experience with real-world IoT communication
- 🔬 **IIOE Demonstration**: Clear example of Industrial Internet of Everything principles
- 💡 **Foundation Building**: Stepping stone for advanced embedded systems projects
- 🛠️ **Skill Development**: Covers wireless communication, API integration, and hardware programming

### Industry Relevance

- 🏭 **Industrial Automation**: Mirrors factory floor notification systems
- 🚉 **Public Infrastructure**: Replicates railway/transport information displays
- 🏠 **Smart Home Technology**: Foundation for IoT home automation panels
- 📊 **Monitoring Systems**: Base architecture for remote alert mechanisms

### Innovation & Scalability

- 🚀 **Low Barrier to Entry**: Affordable components enable widespread adoption
- 🔄 **Modular Architecture**: Easy to adapt for different applications
- 🌐 **Cloud Integration**: Demonstrates modern IoT communication patterns
- 📈 **Scalability**: Concept extendable to large-scale deployment

---

## 📚 Course Context

**Industrial Internet of Everything Laboratory**  
**Course Code**: DJS22ICC702  
**Semester**: VII  
**Academic Year**: 2025-2026

**Department**: Computer Science and Engineering  
**(IoT and Cyber Security with Blockchain Technology)**

**Institution**: Dwarkadas J. Sanghvi College of Engineering

---

## 👥 Project Contributors

### Group No. 3

- **Parshav Dedhia** (60019220120)
- **Devarsh Chandiwade** (60019230112)
- **Hiren Darji** (60019230114)
- **Tushar Panchal** (60019230116)

### Project Guide

**Mr. Swapnil Gharat**  
Department of Computer Science and Engineering  
(IoT and Cyber Security with Blockchain Technology)

---

## 📧 Contact

**Hiren Darji**  
📩 darjihiren850@gmail.com  
🔗 [linkedin.com/in/hiren-darji31](https://linkedin.com/in/hiren-darji31)

---

## 📄 License

This project is submitted as partial fulfillment for the Industrial Internet of Everything Laboratory course. All sources and references have been properly cited and acknowledged.

---

## 🔗 Resources

- 📖 [Telegram Bot API Documentation](https://core.telegram.org/bots/api)
- 🛠️ [Arduino Reference](https://www.arduino.cc/reference/en/)
- 📡 [ESP8266 Community Documentation](https://arduino-esp8266.readthedocs.io/)
- 📺 [LiquidCrystal I2C Library](https://github.com/johnrickman/LiquidCrystal_I2C)

---

🚀 *Telegram Bot Message Display — Connecting Cloud to Hardware. Demonstrating Real-Time IoT Communication. Building the Future of Industrial Internet of Everything.*
