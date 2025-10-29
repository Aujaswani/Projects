#  RFID-Based Real-Time Attendance System using ESP32 and Cloud Automation

> **A real-time, IoT-powered attendance system** that automates classroom roll calls using an ESP32 microcontroller, RFID technology, Google Sheets, Google Apps Script, and Azure Logic Apps.  
> The system ensures fast, accurate, and touchless attendance logging — with automated email reporting to professors.

---

##  Table of Contents
1. [Overview](#-overview)
2. [Objectives](#-objectives)
3. [System Architecture](#-system-architecture)
4. [Hardware & Wiring](#-hardware--wiring)
5. [Software Stack](#-software-stack)
6. [Code Components](#-code-components)
7. [Cloud Integration Flow](#-cloud-integration-flow)
8. [Automation Triggers](#-automation-triggers)
9. [Master Sheet Logic](#-master-sheet-logic)
10. [Demo & Screenshots](#-demo--screenshots)
11. [Benefits](#-benefits)
12. [Future Enhancements](#-future-enhancements)
13. [Conclusion](#-conclusion)
14. [Repository Structure](#-repository-structure)
15. [Credits](#-credits)

---

##  Overview

The **RFID-Based Real-Time Attendance System** offers a smart alternative to manual attendance methods by integrating **hardware (ESP32 + RFID)** and **cloud automation (Google Apps Script + Azure Logic Apps)**.  

When a student taps their RFID card, the ESP32 sends a time-stamped JSON payload to a Google Apps Script Web App. The script matches the UID with a master list, records the entry in Google Sheets, and triggers an Azure Logic App to email the attendance report to the instructor.

This hybrid IoT–Cloud workflow ensures instant, automated, and reliable attendance management.

---

##  Objectives

- Eliminate manual roll calls and paper registers  
- Enable real-time attendance tracking via IoT devices  
- Use cloud automation for email reporting  
- Ensure accuracy and low-latency data processing  
- Demonstrate seamless IoT–Cloud integration using open-source tools  

---

##  System Architecture


**Architecture Diagram:**  


<img width="921" height="617" alt="image" src="https://github.com/user-attachments/assets/7ec41cb2-c7fe-43e5-a82a-07cea2b19e3d" />


---

##  Hardware & Wiring

### Components Used
| Component | Description |
|------------|-------------|
| **ESP32 Development Board** | Handles Wi-Fi, data transmission, and processing |
| **RC522 RFID Reader Module** | Reads RFID card UID values |
| **RFID Cards/Tags** | Unique student identifiers |
| **Buzzer** | Provides audible feedback on successful scans |

### Wiring Table

| RFID Pin | ESP32 Pin | Purpose |
|-----------|------------|----------|
| SDA (SS) | D5 | Slave Select |
| SCK | D18 | Clock |
| MOSI | D23 | Data to Reader |
| MISO | D19 | Data from Reader |
| RST | D27 | Reset |
| BUZZER | D12 | Digital output for feedback |

---

## Software Stack

| Layer | Technology | Role |
|--------|-------------|------|
| **Microcontroller** | ESP32 | RFID scanning, time sync, data transmission |
| **Firmware** | Arduino IDE | Code development and hardware interface |
| **Cloud Backend** | Google Apps Script | JSON processing, attendance logging |
| **Database** | Google Sheets | Real-time storage of attendance data |
| **Automation** | Azure Logic Apps | CSV handling and email dispatch |

---

## Code Components

### 1. ESP32 Firmware (`src/esp32_rfid_attendance.ino`)
- Connects to Wi-Fi and NTP server (for timestamp sync)
- Reads UID from the RC522 RFID module
- Packages data as JSON `{uid, time}`
- Sends data via HTTP POST to Google Apps Script endpoint
- Provides LED/Buzzer feedback to confirm scan

**Key Libraries:**
- `WiFi.h` – for Wi-Fi connectivity  
- `HTTPClient.h` – for REST API communication  
- `MFRC522.h` – for RFID reader interface  
- `time.h` – for NTP-based timestamping  

---

### 2. Google Apps Script (`cloud/google_apps_script.gs`)
- Acts as a Web App endpoint to receive data from ESP32
- Matches UID to student name using the “Students” sheet
- Checks if student is *On Time* or *Late*
- Logs data into “Attendance” sheet
- Exports the sheet as CSV and sends it to Azure Logic Apps

**Key Functions:**
| Function | Description |
|-----------|--------------|
| `doPost(e)` | Handles incoming JSON data and processes attendance |
| `getStudentName(uid)` | Maps UID to student name using the Master Sheet |
| `sendToAzure()` | Sends attendance data to Azure Logic App |
| `resetAttendanceSheet()` | Clears attendance sheet daily using triggers |

---

##  Cloud Integration Flow

| Platform | Action |
|-----------|--------|
| **ESP32** | Sends JSON data via HTTP POST |
| **Google Apps Script** | Parses JSON and logs to Google Sheets |
| **Google Sheets** | Stores attendance records |
| **Azure Logic Apps** | Sends automated email with CSV report |

### Azure Logic App Flow
1. **Trigger:** Receives HTTP POST from Apps Script  
2. **Compose:** Stores raw CSV (`@triggerOutputs()['body']`)  
3. **Encode:** Base64 encoding of CSV  
4. **Send Email (V2):**  
   - File Name: `Attendance_<Date>.csv`  
   - File Content: Encoded CSV  
   - Recipient: Professor’s email  

---

##  Automation Triggers

| Type | Function | Purpose |
|-------|-----------|----------|
|  **Time-Based Trigger** | `resetAttendanceSheet()` | Clears sheet daily post-class |
|  **On-Change Trigger** | `sendToAzure()` | Executes on new row addition to send CSV |

---

##  Master Sheet Logic

| Sheet | Purpose |
|--------|----------|
| **Students** | Stores mapping between `UID` and `Student Name` |
| **Attendance** | Receives real-time entries with timestamp and status |

If no match found → logged as **Unknown Student**.

---

##  Demo & Screenshots
<img width="468" height="351" alt="image" src="https://github.com/user-attachments/assets/b285f4ea-7ecc-4cb5-8970-2ec0e8b932f0" />

<img width="957" height="617" alt="image" src="https://github.com/user-attachments/assets/73d04262-c2b6-478a-9577-7cf7ce7795b8" />

<img width="1018" height="573" alt="image" src="https://github.com/user-attachments/assets/7935ace4-ca70-4286-a779-b056f6e6e49b" />

<img width="1043" height="611" alt="image" src="https://github.com/user-attachments/assets/357ae7e3-20f3-4433-bae2-33134793a365" />


---

##  Benefits

✅ Touchless and hygienic attendance collection  
✅ Reduces human error and time delay  
✅ Works seamlessly across classrooms and labs  
✅ Fully automated cloud integration — no manual intervention  
✅ Modular and reusable for offices, labs, and institutions  

---

##  Future Enhancements

- [ ] Add web dashboard for real-time analytics  
- [ ] Integrate facial recognition as a backup option  
- [ ] Implement database backend (e.g., Firebase or MySQL)  
- [ ] Enable offline caching for temporary network failures  
- [ ] Add multi-classroom support  

---

##  Conclusion

This project demonstrates an effective fusion of **IoT hardware** and **cloud computing** for solving real-world administrative challenges.  
By leveraging ESP32 and cloud automation, it delivers a **cost-efficient, real-time, and scalable** solution for modern classrooms and organizations.

The system is:
- **Accurate:** NTP-based timestamps ensure precision  
- **Automated:** No manual entries required  
- **Adaptable:** Easily modifiable for other domains like offices or events  

---


