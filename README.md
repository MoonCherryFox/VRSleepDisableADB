# 🌙 **VRSleepDisableADB**
### **Universal ADB‑based solutions for disabling Sleep Mode on VR headsets**  
**Pico 4 Ultra • Pico 4 / Neo 3 • Meta Quest 1/2/3**

---

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Android%2010%20%7C%2012%20%7C%2014-blue?style=for-the-badge">
  <img src="https://img.shields.io/badge/VR-Pico%204%20Ultra-orange?style=for-the-badge">
  <img src="https://img.shields.io/badge/VR-Pico%204%20%7C%20Neo%203-yellow?style=for-the-badge">
  <img src="https://img.shields.io/badge/VR-Quest%201%20%7C%202%20%7C%203-green?style=for-the-badge">
  <img src="https://img.shields.io/badge/ADB-Scripts-critical?style=for-the-badge">
</p>

---

## 📘 **About This Project**

**VRSleepDisableADB** is a research‑driven repository dedicated to bypassing forced sleep timers on modern VR headsets.  
It provides:

- **ADB WakeUp loops** for Pico 4 Ultra  
- **Hidden Android settings access** for Pico 4 / Neo 3  
- **Proximity sensor overrides** for Quest 1/2/3  
- **Scientific analysis** of Pico OS and Meta OS power‑management behavior  
- **Universal ADB commands** for diagnostics  
- Integration with the standalone app **Moon Wakeup Screen**  
- Integration with the **Pico Settings** app ([https://github.com/Pico-4/Settings](https://github.com/Pico-4/Settings))

This repository is designed for developers, modders, and VR power‑users who need **persistent wakefulness** for streaming, development, testing, kiosk mode, or long‑running VR tasks.

---

## 🧩 **Android Versions (2026)**

| Device | Android Version | Notes |
|--------|-----------------|-------|
| **Pico 4 Ultra** | **Android 14** | Locked root, Pico OS overrides sleep timers |
| **Pico 4** | Android 10 | Hidden Android settings accessible |
| **Pico Neo 3** | Android 10 | Same behavior as Pico 4 |
| **Meta Quest 1** | Android 7.1 | Legacy OS, proximity sensor override works |
| **Meta Quest 2** | Android 10 | Proximity sensor override works |
| **Meta Quest 3** | Android 12 | Proximity sensor override works |

---

## 🧩 **Why Pico 4 Ultra cannot disable Sleep Mode normally**

### 🔒 **1. Locked Root (Android 14)**  
Pico 4 Ultra uses a heavily restricted Android 14 build:

- `adb root` → disabled  
- `su` → not available  
- SELinux → enforcing  
- system settings → validated by `SettingsProvider.validateSystemSettingValue()`

Negative values (`-1`) for timeouts are rejected.  
Large values (`2147483647`) are accepted but **ignored**.

---

### 🧠 **2. Pico OS Power Daemon (`pvr_service`)**

The daemon checks:

- proximity sensor  
- IMU activity  
- controller activity  
- internal sleep timers  
- display state  

If inactivity is detected → **forced sleep**, regardless of Android settings.

This makes Pico 4 Ultra **immune to standard ADB timeout modifications**.

---

### 🧪 **3. Only one method works: activity simulation**

The only reliable bypass is:

- sending **KEYCODE_WAKEUP** every 120 seconds  
- resetting the internal Pico OS sleep timer  
- preventing forced shutdown  

This is the foundation of the **ADB WakeUp Loop**.

---

# 🌙 **2. Moon Wakeup Screen (Recommended)**

Moon Wakeup Screen is the **ultimate solution**:

- Creates persistent wakelocks  
- Prevents CPU and display sleep  
- Works without a PC  
- Compatible with Pico 4 Ultra  
- Ideal for kiosk mode, streaming, and long sessions  

This repository provides **ADB alternatives** for advanced users.

---

# ⚙️ **3. Pico 4 Ultra — ADB WakeUp Loop (Final Version)**

This is the **only fully reliable ADB method** for Pico 4 Ultra.

It sends a wake‑signal every 30 seconds, resetting the internal Pico sleep timer.

---

## 🟦 **Windows CMD**
```cmd
@echo off
title Pico 4 Ultra Awake Script
echo Pico Awake Script started...
echo Press CTRL+C to stop.
echo.

:loop
adb shell input keyevent 224
echo Sent wake signal at %time%
timeout /t 30 /nobreak >nul
goto loop
```

---

## 🟪 **PowerShell**
```powershell
while ($true) {
    adb shell input keyevent 224
    Write-Host "Sent wake signal at $(Get-Date)"
    Start-Sleep -Seconds 30
}
```

---

## 🟩 **Linux / macOS**
```bash
while true; do
    adb shell input keyevent 224
    echo "Sent wake signal at $(date)"
    sleep 30
done
```

---

# 📱 **4. Hidden Android Settings (Pico 4 / Neo 3 — Non‑Ultra)**

These devices allow access to hidden Android menus.

### 🔧 **Open main Android Settings**
```
adb shell am start -n com.android.settings/.Settings
```

### 🔧 **Open Display Settings**
```
adb shell am start -a android.settings.DISPLAY_SETTINGS
```

### 🔧 **Open Power Usage (hidden)**
```
adb shell am start -n com.android.settings/.PowerUsageSummary
```

### 🔧 **Open Developer Options**
```
adb shell am start -a com.android.settings.APPLICATION_DEVELOPMENT_SETTINGS
```

### 🛠️ Inside these menus, disable:
- **System Sleep Timeout → Never**  
- **Screen Off Timeout → Never**  
- **Smart Wake Mode → Off**  
- **Auto Sleep → Off**

Then **reboot the headset**.

---

# 🧩 **5. Additional Pico Solution — Pico Settings App**

The open‑source project:

### 🔗 [https://github.com/Pico-4/Settings](https://github.com/Pico-4/Settings)

Provides:

- access to hidden Pico OS menus  
- display timeout controls  
- developer options  
- power management toggles  

This app is a **secondary solution** for Pico 4 (non‑Ultra) and Pico Neo 3.

---

# 🕶️ **6. Meta Quest 1/2/3 — Disable Proximity Sensor**

Quest uses an IR sensor that shuts off the display when no face is detected.

### 🧪 **Disable via ADB**
```
adb shell setprop debug.oculus.disable_proximity 1
```

### 🧪 **Alternative commands**
```
adb shell settings put system oculus_proximity_disabled 1
adb shell settings put global device_provisioned 1
```

### 🧪 **Hardware workaround**
- Cover the sensor with opaque tape  
- Use a face mask that blocks the sensor  
- Install a 3D‑printed sensor blocker  

---

# 🧰 **7. Universal ADB Commands**

### 🔍 **Check current timeouts**
```
adb shell settings get system screen_off_timeout
adb shell settings get system system_sleep_timeout
```

### 🔧 **Set maximum timeouts (works on non‑Ultra Pico)**
```
adb shell settings put system screen_off_timeout 2147483647
adb shell settings put system system_sleep_timeout 2147483647
```


---

# 🧭 **Research Summary & Future Work**

This repository is part of an ongoing research project analyzing:

###  **Pico 4 Ultra (Android 14)**
- locked root  
- SELinux enforcing  
- aggressive power daemon  
- forced sleep cycles  
- ADB command interception  
- display driver behavior  
- IMU inactivity thresholds  
###  **Pico 4 / Neo 3 (Android 10)**
- accessible hidden menus  
- modifiable timeouts  
- partial wakelock support  
- compatibility with Pico Settings app  
###  **Meta Quest 1/2/3**
- proximity sensor architecture  
- Android base layer differences  
- Oculus system services  
- display driver behavior  
- kiosk‑mode limitations  
###  **Future Research Directions**
- reverse‑engineering Pico OS power daemon  
- exploring wireless ADB wake‑loops  
- testing wakelock injection via custom APKs  
- analyzing Quest 3 display driver behavior  
- building unified VR Sleep Control API  
- integrating Moon Wakeup Screen with ADB scripts  

This repository will continue to expand as new VR headsets and OS versions are released.

---

#  **Credits**

Created by **Sophia-MoonCherryFox**  
Research, testing, and engineering of VR sleep‑mode bypass techniques.
