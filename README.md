# Android-Flashing-Guide-macOS
A comprehensive guide and technical log for installing LineageOS 22 on a Redmi Note 9 Pro (curtana) from a macOS environment.
# Guide: Installing LineageOS 22 on Redmi Note 9 Pro (curtana)

A comprehensive guide and technical log detailing the process of unlocking the bootloader and flashing LineageOS 22 (Android 15) on a Redmi Note 9 Pro (India variant, codename: `curtana`) from a macOS environment.

*   **Author:** N.SHIVARAM
*   **Date:** July 2025
*   **Device:** Redmi Note 9 Pro (India) - `curtana`
*   **Host OS:** macOS
*   **Target OS:** LineageOS 22 (Android 15)

---

## ⚠️ Disclaimer
This documentation is for informational purposes only. The process of unlocking a bootloader and flashing a custom ROM will void your device's warranty and erase all data. Follow these steps at your own risk. I am not responsible for any damage, data loss, or "bricked" devices.

---

## Table of Contents
1.  [Prerequisites](#prerequisites)
2.  [Phase 1: Bootloader Unlock](#phase-1-bootloader-unlock)
3.  [Phase 2: LineageOS Installation](#phase-2-lineageos-installation)
4.  [Troubleshooting Log](#troubleshooting-log)
5.  [Final Result & Key Learnings](#final-result--key-learnings)

---

## Prerequisites
Before beginning, ensure you have the following:
*   A Redmi Note 9 Pro (`curtana`) with a charged battery.
*   A macOS computer.
*   A reliable USB data-transfer cable.
*   All necessary files downloaded:
    *   `platform-tools` (containing `adb` and `fastboot`)
    *   A third-party unlock tool (`MiUnlockTool.py`)
    *   The correct LineageOS ROM `.zip` file for your device.
    *   The LineageOS `recovery.img` file.
    *   The appropriate GApps package (e.g., MindTheGapps).

---

## Phase 1: Bootloader Unlock
This phase covers the preparation and official unlocking process via Xiaomi's servers.

#### Key Commands & Procedure:
1.  **Enable Developer Options** on the device.
2.  **Bind Mi Account** in `Mi Unlock Status` using a cellular data connection.
3.  **Prepare Python Environment** on macOS:
    ```bash
    # Navigate to the project directory
    cd /path/to/MiUnlockTool-main

    # Activate the local virtual environment
    source venv/bin/activate

    # Install required dependencies
    pip install urllib3 pycryptodome requests
    ```
4.  **Execute Unlock Script:** With the phone in Fastboot Mode (Volume Down + Power), run the script. This initiates the mandatory 7-day waiting period.
    ```bash
    python3 MiUnlockTool.py
    ```
5.  **Final Unlock:** After the waiting period, repeat step 4 to complete the unlock. The device will be wiped.

---

## Phase 2: LineageOS Installation
With the bootloader unlocked, this phase details the installation of the custom ROM.

#### Key Commands & Procedure:
1.  **Flash Custom Recovery:** With the phone in Fastboot Mode:
    ```bash
    # Navigate to the folder with adb/fastboot
    cd /path/to/platform-tools

    # Grant execute permissions (one-time macOS requirement)
    chmod +x adb
    chmod +x fastboot

    # Flash the recovery image
    ./fastboot flash recovery recovery.img
    ```
2.  **Boot into Recovery:** Manually reboot the phone directly into recovery (Volume Up + Power) to prevent it from being overwritten.
3.  **Format Data:** In LineageOS Recovery, use the `Factory Reset -> Format data / factory reset` option.
4.  **Sideload ROM & GApps:** In recovery, select `Apply update -> Apply from ADB`.
    ```bash
    # Sideload the LineageOS ROM
    ./adb sideload lineage-22.zip

    # Immediately after, re-enter ADB Sideload mode and flash GApps
    ./adb sideload MindTheGapps-15.zip
    ```
5.  **Reboot:** From the recovery main menu, select `Reboot system now`. The first boot will take several minutes.

---

## Troubleshooting Log
This project involved overcoming several real-world technical challenges.

*   **Issue #1: USB Connection Failure (`< waiting for any device >`)**
    *   **Diagnosis:** The `./fastboot devices` command returned an empty list.
    *   **Root Cause:** The USB cable being used was a charge-only cable, incapable of data transfer.
    *   **Resolution:** Switched to a high-quality USB data cable, which resulted in immediate device detection.

*   **Issue #2: Installation Aborted (`Status 2`)**
    *   **Diagnosis:** The `adb sideload` process failed on the device after the file transfer was complete.
    *   **Root Cause:** Improperly formatted partitions or a mismatch between the ROM's required firmware and the device's current firmware.
    *   **Resolution:** Re-running the `Format data / factory reset` step from within LineageOS Recovery before re-attempting the sideload resolved the issue by ensuring a perfectly clean state for the installation script.

*   **Issue #3: Failed Large File Download**
    *   **Diagnosis:** Downloading the GApps package repeatedly resulted in tiny, corrupted files.
    *   **Root Cause:** Unstable mobile hotspot connection interrupting the download.
    *   **Resolution:** Used the `curl` command-line tool with the `-C -` flag to resume the broken download, successfully retrieving the complete file.
    ```bash
    # Command used to resume the download
    curl -L -C - -o MindTheGapps-15.zip "https://..."
    ```

---

## Final Result & Key Learnings
The device now runs LineageOS 22 smoothly, providing a significant performance boost and a clean, ad-free Android experience.

This project was a valuable exercise in:
*   **Command-Line Proficiency:** Gained practical experience with `adb`, `fastboot`, `cd`, `chmod`, `pip`, and `curl`.
*   **Systematic Troubleshooting:** Learned to diagnose issues by isolating variables (e.g., testing the USB cable with `./fastboot devices`).
*   **Persistence:** Successfully navigated a multi-day process with multiple failure points.
