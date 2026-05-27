---
title: "Installing Nessus in Kali Linux: A Complete Vulnerability Scanning Guide"
author: Illusivehacks
date: 2026-03-16 10:00:00 +0300
description: A comprehensive guide on installing Nessus vulnerability scanner on Kali Linux and performing your first network scan against Metasploitable 2.
image: /assets/images/Third_blog/third_blog.jpg
categories: [Cybersecurity, Tutorial, Nessus, Vulnerability Scanning, Kali Linux]
tags: [Nessus, Kali Linux, Vulnerability Scanner, Metasploitable 2, Network Security, Tenable, Penetration Testing]
---

# Setting Up Your Vulnerability Scanner: Installing Nessus on Kali Linux

Welcome back, fellow security enthusiasts! In our previous lab session, we set up Metasploitable 2 as our vulnerable target. Today, we're adding the other crucial piece of our hacking lab: a professional-grade vulnerability scanner.

**Nessus** by Tenable is one of the industry's most trusted vulnerability scanners. Used by penetration testers, security analysts and system administrators worldwide, it helps identify weaknesses before attackers do. The best part? The free (Home) version gives us plenty of power for learning.

Let's get Nessus installed on Kali Linux and run our first vulnerability scan against Metasploitable 2!

---

## What's the Deal with Nessus?

### Q: What exactly is Nessus and why do I need it?

**A:** Think of Nessus as your automated security auditor. While manual testing is essential, Nessus rapidly checks thousands of potential vulnerabilities — misconfigurations, missing patches, default credentials and known exploits — and presents them in an organised, actionable report.

It's like having a team of security experts simultaneously checking every door, window and lock in a massive building, then handing you a detailed map of everything they found.

### Q: How do I get Nessus installed on Kali Linux?

**A:** Kali Linux doesn't come with Nessus pre-installed, but the installation process is straightforward. We'll use the command line (your best friend in cybersecurity) to download and set it up properly.

---

## Installation Steps

### Step 1: Downloading Nessus

First, we need to grab the latest Nessus package. Open a terminal in your Kali Linux VM and use `wget` to download directly from Tenable's servers.

```bash
wget https://www.tenable.com/downloads/api/v2/pages/nessus/files/Nessus-latest-debian10_amd64.deb
```

This command fetches the Debian-compatible package (which works perfectly on Kali, since Kali is Debian-based). The file might take a minute or two to download depending on your internet speed.

![Downloading Nessus with wget](/assets/images/Third_blog/image1.jpg)
*The screenshot shows the wget command in action, downloading the Nessus package.*

<p>
  <img src="/assets/images/Third_blog/image1.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 2: Installing the Package

Once the download completes, install the `.deb` package using Debian's package manager:

```bash
sudo dpkg -i Nessus-*.deb
```

The `-i` flag tells `dpkg` to install the package, and the `Nessus-*.deb` pattern matches whatever version you just downloaded — so you don't need to type the full filename.

![dpkg installation process](/assets/images/Third_blog/image2.jpg)
*The dpkg installation process showing Nessus being configured.*

<p>
  <img src="/assets/images/Third_blog/image2.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

You should see output indicating that Nessus is being installed and configured. If you encounter any dependency errors (unlikely but possible), run:

```bash
sudo apt-get install -f
```

This will fix any missing dependencies automatically.

---

### Step 3: Starting the Nessus Service

With Nessus installed, start its background service. This service runs the Nessus web interface and scanning engine.

```bash
sudo systemctl start nessusd
```

The `systemctl` command manages services on modern Linux systems. Here, we're telling it to start the Nessus daemon (the "d" stands for daemon — a background process).

![Starting the Nessus daemon](/assets/images/Third_blog/image3.jpg)
*Starting the Nessus daemon with systemctl.*

<p>
  <img src="/assets/images/Third_blog/image3.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 4: Enabling Auto-Start

Enable Nessus to start automatically whenever you boot up Kali, saving you from manually starting it every time.

```bash
sudo systemctl enable nessusd
```

This creates the necessary symlinks so that `nessusd` launches during system startup.

![Enabling Nessus auto-start](/assets/images/Third_blog/image4.jpg)
*Enabling Nessus to start automatically on boot.*

<p>
  <img src="/assets/images/Third_blog/image4.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 5: Verifying Nessus is Running

Confirm that everything is working correctly:

```bash
sudo systemctl status nessusd
```

You should see output showing that the service is **active (running)** and possibly some recent log entries. If you see errors, double-check that you ran the start command and that no other service is conflicting on port `8834`.

![Verifying Nessus is running](/assets/images/Third_blog/image5.jpg)
*Verifying that Nessus is running properly.*

<p>
  <img src="/assets/images/Third_blog/image5.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 6: Accessing the Nessus Web Interface

Now for the exciting part — accessing Nessus through your browser!

Open Firefox (or your preferred browser) in Kali and navigate to:

```
https://192.168.56.102:8834
```

> **Important Notes:**
> - Replace `192.168.56.102` with your Kali machine's actual Host-Only IP address.
> - The URL uses **HTTPS**, not HTTP.
> - Port `8834` is Nessus's default web interface port.

Since we're using a self-signed certificate, your browser will show a security warning. This is completely normal for local installations. Click **Advanced** → **Accept Risk and Continue** (the exact wording varies by browser).

---

### Step 7: Initial Nessus Setup

The first time you access Nessus, complete the following setup steps:

1. **Choose installation type** — Select **Nessus Essential** (the free version). This gives us all the scanning capabilities we need for learning.

2. **Create an admin account** — You'll be prompted to create a username and password. Remember these credentials — you'll need them every time you log in.

3. **Activation code** — Nessus Essential requires a free activation code. Visit [Tenable's website](https://www.tenable.com) to get one (just provide your email). Copy that code and paste it when prompted.

4. **Plugin compilation** — Nessus will now download and compile its vulnerability plugins. This can take **10–20 minutes** depending on your internet speed and VM resources. Be patient — this only happens once.

Once compilation finishes, you'll be taken to the Nessus dashboard. **Congratulations — Nessus is now installed and ready to scan!**

---

## Running Your First Vulnerability Scan

Now that Nessus is up and running, let's put it to work scanning our Metasploitable 2 target. Remember, Metasploitable 2 is intentionally vulnerable, so this will produce some interesting results!

### Step 1: Create a New Scan

From the Nessus dashboard, click on **Scans** in the navigation bar, then click the **New Scan** button (usually a blue button in the top right).

![Navigate to New Scan](/assets/images/Third_blog/image6.jpg)
*Navigate to Scans → New Scan.*

<p>
  <img src="/assets/images/Third_blog/image6.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 2: Select a Scan Template

Nessus offers many specialised scan templates. For our purposes, we'll start with the most versatile option.

Select **Basic Network Scan** from the list of templates.

![Select Basic Network Scan](/assets/images/Third_blog/image7.jpg)
*Choose "Basic Network Scan" for general vulnerability discovery.*

<p>
  <img src="/assets/images/Third_blog/image7.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 3: Configure Scan Settings

Tell Nessus what to scan and give your scan a name:

| Field       | Value                              |
|-------------|------------------------------------|
| Name        | Metasploitable 2 - Full Scan       |
| Description | Scanning intentionally vulnerable target *(optional but good practice)* |
| Targets     | `192.168.56.103`                   |

> **Double-check that IP address!** This is where you'll scan, so make sure it matches your Metasploitable 2 VM's IP.

![Configure scan settings](/assets/images/Third_blog/image8.jpg)
*Fill in the scan name, description, and target IP.*

<p>
  <img src="/assets/images/Third_blog/image8.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 4: Save and Launch

Click the **Save** button at the bottom of the configuration page. This returns you to the Scans list, where you'll now see your newly created scan.

To launch the scan, click the **play button (▶)** next to your scan name.

![Launch the scan](/assets/images/Third_blog/image9.jpg)
*Click the play button to start your first vulnerability scan.*

<p>
  <img src="/assets/images/Third_blog/image9.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

## Understanding the Results

The scan will take several minutes to complete. During this time, Nessus is performing:

- Port scanning to discover open services
- Service version detection
- Vulnerability checks against thousands of plugins
- Configuration audits

When the scan finishes, you'll be presented with a wealth of information. Let's break down what you're seeing.

![Scan results dashboard](/assets/images/Third_blog/image10.jpg)
*The scan results dashboard showing vulnerability counts by severity.*

<p>
  <img src="/assets/images/Third_blog/image10.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

### Results Dashboard

The main results page shows:

- **Critical, High, Medium, Low** vulnerabilities — colour-coded by severity
- Total open ports discovered
- Service breakdown by protocol
- Remediation effort metrics

### Exploring Vulnerabilities

Click on any vulnerability to see detailed information including:

- Description of the issue
- Affected hosts and ports
- Risk information (CVSS score, exploitability)
- Solution/remediation steps
- References for further reading

![Vulnerability details](/assets/images/Third_blog/image11.jpg)
*Expanded view showing vulnerability details and remediation advice.*

<p>
  <img src="/assets/images/Third_blog/image11.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Sample Vulnerabilities You Might Find

When scanning Metasploitable 2, expect to find gems like:

- **UnrealIRCd Backdoor** — A classic backdoor in an old IRC server
- **vsftpd 2.3.4 Backdoor** — The famous FTP backdoor
- **Default credentials** (`msfadmin:msfadmin`) on various services
- **Outdated SSL/TLS configurations**
- **Multiple Samba vulnerabilities**

![Critical vulnerability 1](/assets/images/Third_blog/image12.jpg)

<p>
  <img src="/assets/images/Third_blog/image12.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

![Critical vulnerability 2](/assets/images/Third_blog/image13.jpg)

<p>
  <img src="/assets/images/Third_blog/image13.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

![Critical vulnerability 3](/assets/images/Third_blog/image14.jpg)

<p>
  <img src="/assets/images/Third_blog/image14.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

*Various critical vulnerabilities discovered on Metasploitable 2.*

---

> **Disclaimer:** The tools and techniques discussed in this guide are intended strictly for educational purposes and authorised penetration testing. Always ensure you have explicit permission before scanning any system or network.
