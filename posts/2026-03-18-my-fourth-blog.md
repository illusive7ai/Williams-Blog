---
title: "Investigating an Android Device Using Andriller; A Mobile Forensics Guide"
author: Illusivehacks
date: 2026-03-18 10:00:00 +0300
description: A step-by-step guide on using Andriller to perform forensically sound, non-destructive data extraction from an Android emulator — covering installation, setup and report analysis.
image: /assets/images/Fourth_blog/fourth_blog.jpg
categories: [Cybersecurity, Tutorial, Andriller, Mobile Forensics, Android]
tags: [Andriller, Android, Mobile Forensics, ADB, Emulator, Android Studio, CHFI, Digital Forensics]
---

# Investigating an Android Device Using Andriller

Welcome back, security enthusiasts! In today's lab we're diving into **mobile forensics** — one of the fastest-growing areas in digital investigation. With billions of Android devices in the wild, knowing how to extract and analyse data from them is an essential skill for any forensic investigator or penetration tester.

**Andriller** is a lightweight but powerful Python-based utility that performs **read-only, forensically sound, non-destructive acquisition** from Android devices. It extracts databases and other sensitive artefacts and presents the findings in clean **HTML** and **Excel (.xlsx)** reports.

Let's walk through the full process — from installation to report analysis!

---

## What Are We Trying to Achieve?

### Q: What is Andriller and what does it do?

**A:** Andriller is a forensic acquisition tool for Android devices. Unlike destructive imaging methods, it uses Android's built-in backup mechanism (ADB) to pull app databases, shared storage and other sensitive data — all without modifying the device. Think of it as a forensic lens that lets you peer inside an Android device and document everything you find, in a court-admissible, non-tampered way.

### Q: What do I need to run this lab?

**A:** You'll need:

- A **Windows VM** (or similar Windows) host machine
- **Andriller** — we'll download it directly from GitHub
- **Android Studio** with an Android emulator
- **Administrative privileges** to run the tools

---

## Lab Environment Setup

### Lab Objectives

Extract databases and other sensitive information from an Android emulator using Andriller.

### Lab Duration

⏱ Estimated Time: **15 Minutes**

---

## Step-by-Step Walkthrough

### Step 1: Create the Output Folder

Start by creating a dedicated folder called **"Andriller"** on your Desktop. This is where Andriller will store all extracted logs, databases and reports.

![Creating the Andriller folder on the Desktop](/assets/images/Fourth_blog/image1.jpg)
*Create a new "Andriller" folder on the Desktop directory.*

---

### Step 2: Download Andriller from GitHub

Open any web browser and navigate to the official Andriller repository:

```
https://github.com/den4uk/andriller
```

Click the green **Code** button and select **Download ZIP**. Once the download completes, extract the ZIP file to your Downloads folder.

![Downloading Andriller from GitHub](/assets/images/Fourth_blog/image2.jpg)
*Download the ZIP from the Andriller GitHub repository.*

![Extracted Andriller folder](/assets/images/Fourth_blog/image3.jpg)
*The extracted andriller-master folder in Downloads.*

---

### Step 3: Install Andriller and Its Dependencies

Open **Command Prompt** and navigate to the extracted Andriller directory:

```
C:\Users\User\Downloads\andriller-master\andriller-master>
```

Run the following command to install Andriller and all its Python dependencies:

```bash
pip install andriller -U
```

You'll see pip downloading and installing all required packages — this may take a minute depending on your connection speed.

![Installing Andriller via pip](/assets/images/Fourth_blog/image4.jpg)
*pip installing Andriller and its dependencies.*

Once installation is complete, launch the Andriller GUI with:

```bash
python -m andriller
```

![Running Andriller GUI](/assets/images/Fourth_blog/image5.jpg)
*Launching the Andriller graphical interface from the command line.*

---

### Step 4: Andriller Main Window

The Andriller CE (Community Edition) main window will appear. You'll notice several key sections:

- **Global Output Location** — where extracted data will be saved
- **Extraction (USB)** — options to Check, Extract or Parse
- **Log area** — live output from Andriller's operations

![Andriller main window](/assets/images/Fourth_blog/image6.jpg)
*The Andriller CE 3.6.3 main window.*

---

### Step 5: Set the Output Location

Click the **Output...** button and navigate to the **Andriller** folder you created on the Desktop in Step 1. Select it and click **Select Folder**.

![Setting the output folder in Andriller](/assets/images/Fourth_blog/image7.jpg)
*Select the Andriller folder on the Desktop as the output destination.*

---

### Step 6: Check for a Connected Device

Click the **Check** button to see if Andriller detects a connected Android device.

> **Note:** On the first attempt, you will likely see a **"device not connected"** message. This is expected — you need to launch an Android emulator first. Proceed to Step 7.

![Device not connected on first check](/assets/images/Fourth_blog/image8.jpg)
*Andriller shows "device not connected" before the emulator is started.*

---

### Step 7: Install Android Studio and Launch an Emulator

Install **Android Studio** and use the **Virtual Device Manager** to create and launch a new Android emulator. Once the emulator has fully booted and is showing the home screen, return to Andriller.

![Android emulator running in Android Studio](/assets/images/Fourth_blog/image9.jpg)
*A freshly booted Android emulator ready for forensic acquisition.*

---

### Step 8: Verify Emulator Detection

Back in Andriller, click **Check** again. This time the emulator should be detected. You'll see it listed as **Serial ID: emulator-5554** (or similar) next to the Check button — confirming Andriller has successfully connected via ADB.

Also ensure the following options are checked:
- ✅ Use AB method (ignore root)
- ✅ Extract Shared Storage

![Emulator detected in Andriller](/assets/images/Fourth_blog/image10.jpg)
*Andriller detects the running emulator with its Serial ID.*

---

### Step 9: Start the Extraction

Click the **Extract** button to begin the forensic acquisition process. Andriller will start reading information and acquiring data from the emulator.

![Starting the extraction in Andriller](/assets/images/Fourth_blog/image11.jpg)
*Clicking Extract initiates the data acquisition process.*

---

### Step 10: Backup Confirmation Window

Immediately after clicking Extract, a small popup window will appear in Andriller titled **"Extraction via Backup method"**. It will instruct you to:

1. Unlock the screen on the emulator
2. Tap **"Back up my data"**
3. Click **OK** to continue

![Andriller extraction confirmation popup](/assets/images/Fourth_blog/image12.jpg)
*Andriller's extraction popup prompting you to authorise the backup on the device.*

---

### Step 11: Authorise the Backup on the Emulator

On the Android emulator, a **Full Backup** screen will appear asking whether to allow a connected desktop computer to perform a full backup. Tap **"Back up my data"** and wait for the backup to complete.

![Full backup screen on Android emulator](/assets/images/Fourth_blog/image13.jpg)
*The "Full backup" consent screen on the Android emulator.*

Once the backup finishes, the emulator will display a **"Backup finished"** notification. Return to Andriller and click **OK** on the popup to continue processing.

![Backup finished on the emulator](/assets/images/Fourth_blog/image14.jpg)
*The emulator confirms that the backup has completed successfully.*

---

### Step 12: Extraction Complete

Andriller will now process the backup — unpacking it, decoding databases, decoding shared filesystem data, and generating the final XLSX report. You'll see live progress in the log window:

```
Reading information...
Acquiring data...
Extracting data from source...
Unpacking backup...
Extracting from backup...
Decoding shared filesystem...
Decoding extracted data...
Generating XLSX report...
Finished.
```

Andriller creates a new directory inside your Andriller output folder, named after the device and timestamp — for example: `emulator-5554_shell_2026-03-18_01.10.06`.

![Extraction finished in Andriller](/assets/images/Fourth_blog/image15.jpg)
*Andriller showing "Finished" after completing the full extraction.*

---

## Analysing the Results

### Step 13: Open the Report Folder

Navigate to the **Andriller** folder on your Desktop and open the timestamped subdirectory. You'll find the following files and folders:

| Item | Description |
|------|-------------|
| `data/` | Extracted app databases |
| `shared/` | Extracted shared storage |
| `backup.ab` | Raw Android backup file |
| `backup.ab.zip` | Compressed backup archive |
| `DataStore.tar.md5` | Integrity hash file |
| `REPORT.html` | Human-readable HTML report |
| `REPORT.xlsx` | Excel spreadsheet report |
| `Shared Storage.html` | Shared storage detailed report |

![Andriller output folder contents](/assets/images/Fourth_blog/image16.jpg)
*The extraction output directory with all extracted files and reports.*

---

### Step 14: Open the HTML Report

Double-click **REPORT.html** to open it in your default browser. The report presents key device information in a clean, structured format:

| Field | Data |
|-------|------|
| Serial | emulator-5554 |
| Status | device |
| Permission | shell |
| Wifi Mac | 02:15:b2:00:00:00 |
| Local Time | 2026-03-18 01:10:05 E. Africa Standard Time |
| Device Time | 2026-03-18 01:10:04 EAT |
| Application | Shared Storage (8) |

The **Application** row shows a clickable link — in this case pointing to the extracted **Shared Storage** data.

![Andriller HTML report](/assets/images/Fourth_blog/image17.jpg)
*The Andriller HTML report showing device metadata and extraction summary.*

---

### Step 15: Explore Shared Storage

Click on the **Shared Storage** link in the Application row to open the shared storage report. This reveals a table of all files found in the device's shared storage, including:

| Index | Directory | Filename | Size | Modified |
|-------|-----------|----------|------|----------|
| 1 | shared/0/Movies/.thumbnails | .database_uuid | 36 | 2025-09-18 10:18:06 UTC |
| 2 | shared/0/Pictures/.thumbnails | .database_uuid | 36 | 2025-09-18 10:18:06 UTC |
| 3 | shared/0/Music/.thumbnails | .database_uuid | 36 | 2025-09-18 10:18:06 UTC |
| 4 | shared/1/Movies/.thumbnails | .database_uuid | 36 | 2025-09-18 10:18:04 UTC |
| 5 | shared/1/Pictures/.thumbnails | .database_uuid | 36 | 2025-09-18 10:18:04 UTC |
| 6 | shared/1/Music/.thumbnails | .database_uuid | 36 | 2025-09-18 10:18:04 UTC |

While this emulator doesn't contain real user data, on a real device this section would reveal photos, videos, music files, documents and more — all invaluable in a forensic investigation.

![Andriller Shared Storage report](/assets/images/Fourth_blog/image18.jpg)
*The Shared Storage report showing indexed files with timestamps and paths.*

---

## Key Takeaways

In this lab we successfully:

- Installed Andriller and its dependencies on a Windows machine
- Connected it to a running Android emulator via ADB
- Performed a **non-destructive, forensically sound** backup extraction
- Generated and analysed an **HTML forensic report** with device metadata and file listings

Andriller is an excellent first step in any mobile forensics workflow. Its use of Android's native ADB backup mechanism means the device is never modified — a critical requirement for evidence admissibility.

---

> **Disclaimer:** The tools and techniques discussed in this guide are intended strictly for educational purposes and authorised forensic investigations. Always ensure you have explicit legal permission before extracting data from any device.
