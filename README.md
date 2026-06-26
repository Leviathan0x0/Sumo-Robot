# 🏆 Antigravity: The Championship-Winning Sumo Robot

Welcome to the repository for **Antigravity**, a high-power, safety-first, ESP32-powered Sumo Robot. 

Antigravity was designed and programmed from the ground up to dominate the ring. It successfully competed in the **YPS Mohali IT TechX Fest 2026**, where it won **🥇 First Place out of 40 schools**!

---

## 📖 The Story & Purpose

Antigravity was engineered specifically for the Sumo Robot category at the **YPS Mohali IT TechX Fest 2026**. The goal was simple: create an unstoppable robot with high torque, fast response times, and bulletproof reliability under heavy radio interference. 

While many competitors struggled with fragile connections, delayed responses, or runaway robots, Antigravity's custom firmware and hardware architecture ensured it was both incredibly agile and perfectly safe.

---

## ⚡ Evolution: Uno to ESP32

To achieve maximum performance and power, the hardware underwent a complete migration:

| Feature | Prototype v1.0 | Champion Version (Current) | Why it was upgraded |
| :--- | :--- | :--- | :--- |
| **MCU** | Arduino Uno | **ESP32 Dev Module** | Faster dual-core processor, native wireless support, and precise PWM controllers. |
| **Bluetooth** | External HC-05 | **ESP32 Built-in Bluetooth** | Removed external module wiring and software serial bottlenecks. |
| **Motor Driver** | L298N Dual H-Bridge | **2× BTS7960 43A H-Bridges** | BTS7960 handles massive current draws from high-torque motors without overheating. |
| **Drivetrain** | Direction-only | **LEDC PWM (20 kHz)** | Precise, silent variable speed control with zero motor whine. |
| **Fail-Safe** | Basic timeout | **3-Layer Active Safety** | Immediate physical motor shutdown if the signal drops or disconnects. |

---

## 🛠️ Hardware Specification

*   **Processor**: ESP32 Dev Board (Dual-core Xtensa LX6, 240 MHz)
*   **Locomotion**: 4× High-Torque Geared Johnson DC Motors (Left side and Right side wired in parallel)
*   **Motor Control**: 2× BTS7960 43A H-Bridge drivers
*   **Battery Configuration**:
    *   **Drivetrain Power**: 12V Celled Battery (Heavy-duty high-current path)
    *   **Logic Power**: LiPo Battery (Powers ESP32 via expansion board)
*   **Safety Status Indicator**: On-board Status LED (GPIO 2) — illuminates only when the robot is actively driving.

---

## 🛡️ The Fail-Safe Core (3-Layer Protection)

In high-stakes Sumo Robot matches, a runaway robot is dangerous. Antigravity runs a custom fail-safe controller loop:

1.  **Layer 1 — Instant Bluetooth Disconnect**: The ESP32 utilizes a low-level callback (`ESP_SPP_CLOSE_EVT`). If the driver's phone app disconnects, the controller triggers a motor stop within milliseconds.
2.  **Layer 2 — Command Timeout**: If connection remains but no control command is received for **1500 ms**, the robot assumes a lost signal and halts.
3.  **Layer 3 — Physical Hardware Enable Kill**: During any fail-safe event, the R_EN and L_EN pins on both BTS7960 drivers are pulled **LOW**, instantly killing the driver stage at the hardware level.

---

## 🔌 Pinout & Wiring Layout

A common ground connection between the ESP32, both BTS7960 drivers, and both battery systems is **mandatory** for safety and signal integrity.

### Left BTS7960 (Left Drivetrain)
*   `RPWM` ➡️ **GPIO 32** (Forward PWM)
*   `LPWM` ➡️ **GPIO 33** (Reverse PWM)
*   `R_EN` ➡️ **GPIO 25** (Right Enable)
*   `L_EN` ➡️ **GPIO 26** (Left Enable)

### Right BTS7960 (Right Drivetrain)
*   `RPWM` ➡️ **GPIO 27** (Forward PWM)
*   `LPWM` ➡️ **GPIO 14** (Reverse PWM)
*   `R_EN` ➡️ **GPIO 4** (Right Enable)
*   `L_EN` ➡️ **GPIO 13** (Left Enable)

### Logic Indicator
*   `Status LED` ➡️ **GPIO 2** (On-board LED)

---

## 🎛️ Bluetooth Control & Command Protocol

Antigravity operates on **Bluetooth Classic (SPP)**. It advertises itself under the name `Antigravity`. It uses an intelligent, non-blocking single-char lookahead token parser that supports both single-character abbreviations and full-word commands:

| Action | Commands | Left Motors | Right Motors |
| :--- | :--- | :--- | :--- |
| **Move Forward** | `F`, `FORWARD`, `GO`, `AHEAD`, `1` | Forward | Forward |
| **Move Backward**| `B`, `BACK`, `BACKWARD`, `REVERSE`, `2` | Reverse | Reverse |
| **Turn Left** | `L`, `LEFT`, `3` | Reverse | Forward |
| **Turn Right** | `R`, `RIGHT`, `4` | Forward | Reverse |
| **Stop Motors** | `S`, `STOP`, `BRAKE`, `IDLE`, `0`, `5` | Off | Off |

---

## 💻 How to Build and Flash

This project is built using the **PlatformIO** ecosystem. 

1.  Clone this repository.
2.  Install the **PlatformIO** extension in VS Code.
3.  Open the project directory.
4.  Connect your ESP32 board via USB.
5.  Click the **PlatformIO: Build** button, then **PlatformIO: Upload** to flash the firmware.
6.  Open the Serial Monitor at `115200` baud to view debugging logs.

---

*Made with 🦾 by the Antigravity Team.*
