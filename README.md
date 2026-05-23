# Campus Noise Level Monitor

An STM32-based embedded system designed to monitor, classify, and visualize ambient noise levels in academic environments. This project provides real-time feedback to occupants and remote monitoring capabilities for facility staff.

## Team Members
| Name | GitHub |
| ------------- | ------------- |
| **Mohab Bahnassy** | [@mohab-bahnassy](https://github.com/mohab-bahnassy) |
| **Kirollos Zikry** | [@kirollos21](https://github.com/kirollos21) |
| **Farida Said** | [@faridaasaidd](https://github.com/faridaasaidd) |

**Course:** CSCE 4301 - Embedded Systems  
**Institution:** American University in Cairo (AUC)  
**Semester:** Spring 2026  

---

## Overview
The Campus Noise Level Monitor continuously samples ambient sound and classifies it into three states: QUIET, WARN, and ALARM. 

### Key Features
* **Real-time Classification:** Uses a 12-bit ADC to capture sound via a MAX9812 microphone.
* **Adaptive Thresholds:** Includes presets for Libraries, Study Rooms, and Labs.
* **Visual Feedback:** Coordinated LED indicators and a Pmod CLP 16x2 LCD with a dynamic intensity bar.
* **Staff Interface:** UART-based CLI and a Python GUI for live charting and remote threshold tuning.
* **Hysteresis Logic:** Prevents rapid state toggling using time-based confirmation.

---

## Hardware Design

### System Architecture
The system is built around the Nucleo-F303K8. It uses a burst-sampling technique (64 samples every 100ms) to calculate peak-to-peak amplitude, followed by an 8-sample moving average filter.

### Pin Mapping
| Signal | MCU Pin | Direction | Component | Notes |
|---|---|---|---|---|
| **Mic Input** | PB0 | Analog IN | MAX9812 | ADC1_IN11 |
| **LCD Data** | PA0-PA1, PB1, PA4-PA8 | OUT | Pmod CLP | DB0-DB7 |
| **LCD Control**| PF0, PF1, PA9 | OUT | Pmod CLP | RS, R/W, E |
| **Green LED** | PB4 | OUT | Status | QUIET Indicator |
| **Red LED** | PB5 | OUT | Status | WARN/ALARM Indicator |
| **Push-button**| PA10 | IN | Preset Toggle | Active LOW |
| **UART TX/RX** | PA2 / PA15 | Serial | PC Link | 115200 Baud |

---

## Software Implementation

### Key Algorithms
* **Peak-to-Peak Sampling:** Samples the ADC 64 times in a tight loop to capture transients independently of DC offset.
* **Hysteresis Logic:** * **WARN to ALARM:** Requires noise to exceed threshold for 5 consecutive ticks (0.5s).
    * **ALARM to QUIET:** Requires noise to stay below threshold for 30 consecutive ticks (3.0s).
* **Moving Average:** 8-sample window to smooth out environmental spikes.

---

## PC GUI Application

The companion application (`noise_monitor_gui.py`) provides a visual dashboard for monitoring noise levels and adjusting hardware thresholds remotely via a serial link.

### GUI Prerequisites
The GUI requires Python 3.x and the following libraries:
* **pyserial**: For UART communication with the STM32.
* **matplotlib**: For real-time data plotting.
* **tkinter**: Standard Python GUI library (usually pre-installed).

### Installation
Install the required dependencies via pip:
```bash
pip install pyserial matplotlib
```

### How to Run the GUI
1. Connect the Nucleo-F303K8 to your PC via USB.
2. Ensure the firmware is flashed and the device is powered.
3. Open a terminal/command prompt in the project folder.
4. Run the script:

```bash
python noise_monitor_gui.py
```

5. Select the appropriate COM Port from the dropdown menu.

6. Set the baud rate to 115200 and click Connect.

7. Use the sliders and Set buttons to adjust thresholds in real-time.

### References
* [STM32F303K8 Datasheet](https://www.st.com/resource/en/datasheet/stm32f303k8.pdf)
* [Pmod CLP Reference Manual](https://www.mouser.com/datasheet/2/690/pmodclp_rm-845694.pdf)
* [MAX9812 Datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/max9812-max9813l.pdf)
