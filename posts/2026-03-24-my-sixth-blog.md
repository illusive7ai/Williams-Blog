---
title: "Locking Down Your Data: A Complete Guide to Disk Encryption with VeraCrypt"
author: Illusivehacks
date: 2026-04-17 10:00:00 +0300
description: A complete step-by-step walkthrough on performing disk encryption using VeraCrypt on Windows 11 — covering volume creation, encryption configuration, mounting and verifying that encrypted volumes remain hidden from attackers.
image: /assets/images/Sixth_blog/sixth_blog.png
categories: [Cybersecurity, Encryption, VeraCrypt, Windows, Data Protection]
tags: [VeraCrypt, Disk Encryption, AES, Encrypted Volume, Windows 11, Data Security, Cybersecurity Lab, File Container]
---

# Locking Down Your Data: A Complete Guide to Disk Encryption with VeraCrypt

Welcome back, security enthusiasts! In today's lab we're diving into one of the most practical and immediately useful skills in a cybersecurity toolkit — **disk encryption**. We'll be using **VeraCrypt**, a free, open-source and battle-tested encryption tool to create a fully encrypted virtual volume on a Windows 11 machine.

By the end of this walkthrough, you'll understand how to create an encrypted file container, configure strong encryption settings, mount your volume, store files inside it and verify that the encrypted data is completely invisible to anyone who doesn't have the password.

---

## What Are We Trying to Achieve?

### Q: What is disk encryption and why does it matter?

**A:** Disk encryption is the process of converting readable data into an unreadable format using a cryptographic algorithm, making it inaccessible to anyone who doesn't possess the correct decryption key or password. It is useful when users need to transmit sensitive information securely, prevent unauthorised access in the event of a theft or system compromise and minimise the chances of data being intercepted or leaked. The only way an attacker can access encrypted data is by successfully decrypting it — and without the password, that is computationally infeasible with strong algorithms like AES.

### Q: What is VeraCrypt?

**A:** VeraCrypt is a free, open-source disk encryption software derived from the discontinued TrueCrypt project. It supports creating encrypted file containers (virtual disks), encrypting entire partitions, and even encrypting the full system drive. It uses industry-standard encryption algorithms including AES-256, Serpent and Twofish and supports SHA-512 and Whirlpool for key derivation hashing. It runs on Windows, Linux and macOS, making it one of the most versatile encryption tools available.

### Q: What do I need to follow this lab?

**A:** You'll need:

- A **Windows 11** virtual machine (or physical machine)
- **VeraCrypt** installed — available for free at [https://www.veracrypt.fr](https://www.veracrypt.fr)
- **VirtualBox** or **VMware** if running inside a VM
- Administrator privileges on the machine
- Basic familiarity with Windows file navigation

---

## Lab Environment

### Lab Objective

Perform disk encryption using VeraCrypt by creating an encrypted file container (virtual volume), mounting it as a drive letter, storing a test file inside it and then unmounting and verifying that the volume contents are completely inaccessible without the password.

### Requirements

- Windows 11 virtual machine
- Web browser with an internet connection (for downloading VeraCrypt if not already installed)
- Administrator privileges to run the tools

### Lab Duration

⏱ Estimated Time: **10–15 Minutes**

---

## Overview of Disk Encryption

Disk encryption is useful when a user needs to send sensitive information securely, store confidential files on a system that may be accessed by others or protect data in the event of physical theft or a remote compromise. When users exchange or store encrypted information, it minimises the chances of the data being compromised. The only way an attacker could access the information is by decrypting it — and that requires the correct password or key.

Furthermore, encryption software installed on a user's system ensures the ongoing security of that system. It should be installed on any system that holds valuable information or that is exposed to significant data transfer activity, particularly in corporate or multi-user environments.

VeraCrypt works by creating an **encrypted file container** — a single file on your hard drive that acts like a virtual encrypted disk. When you mount it with the correct password, it appears in Windows as a regular drive letter and you can read, write and delete files just like on any normal disk. When unmounted, the file looks like random binary data and reveals absolutely nothing about its contents.

---

## Step-by-Step Walkthrough

### Step 1: Boot into the Windows 11 Virtual Machine

Start your Windows 11 VM in VirtualBox (or VMware). Allow it to boot fully until you reach the lock screen or desktop. In this lab, Windows 11 is already installed and running with VeraCrypt pre-installed.

![Windows 11 lock screen inside VirtualBox](/assets/images/Sixth_blog/image1.jpg)
*The Windows 11 virtual machine booted and ready inside Oracle VirtualBox.*

<p>
  <img src="/assets/images/Sixth_blog/image1.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


If VeraCrypt is not yet installed on your machine, open a web browser and navigate to:

```
https://www.veracrypt.fr/en/Downloads.html
```

Download the Windows installer, run it with administrator privileges and complete the installation wizard before continuing.

---

### Step 2: Launch VeraCrypt via the Start Menu

Once you're at the Windows 11 desktop, click the **Search** icon on the taskbar (or press `Win + S`) and type `verac` into the search bar. You will see **VeraCrypt – Encryption software** appear under **Best match**. Click it to launch the application.

![Searching for VeraCrypt in the Windows 11 Start menu](/assets/images/Sixth_blog/image2.jpg)
*Searching for "verac" in the Start menu reveals VeraCrypt ready to launch.*

<p>
  <img src="/assets/images/Sixth_blog/image2.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


> **Tip:** You may be prompted by Windows User Account Control (UAC) to allow VeraCrypt to make changes to the system. Click **Yes** — VeraCrypt requires elevated privileges to mount and unmount virtual encrypted drives.

---

### Step 3: Open the Volume Creation Wizard and Choose Volume Type

With VeraCrypt open, click the **Create Volume** button on the main VeraCrypt window. This launches the **VeraCrypt Volume Creation Wizard**.

On the first screen you will be presented with three options:

- **Create an encrypted file container** — Creates a virtual encrypted disk stored inside a single file. This is the recommended option for beginners and for portable encryption.
- **Encrypt a non-system partition/drive** — Encrypts an entire internal or external partition (e.g. a USB flash drive).
- **Encrypt the system partition or entire system drive** — Encrypts the entire Windows installation drive, requiring a password before Windows can even boot.

For this lab, select **Create an encrypted file container** and click **Next**.

![VeraCrypt Volume Creation Wizard — volume type selection](/assets/images/Sixth_blog/image3.jpg)
*The Volume Creation Wizard offers three encryption modes. We select "Create an encrypted file container" for this lab.*

<p>
  <img src="/assets/images/Sixth_blog/image3.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 4: Select Standard VeraCrypt Volume

On the next screen you are asked to choose between a **Standard VeraCrypt volume** and a **Hidden VeraCrypt volume**.

- **Standard VeraCrypt volume** — A normal encrypted volume that you unlock with a password.
- **Hidden VeraCrypt volume** — An advanced feature that creates a second secret volume inside the outer volume. If you are ever coerced into revealing your password, you can provide the outer password, which opens decoy content, while the truly sensitive data remains hidden inside.

For this lab, select **Standard VeraCrypt volume** and click **Next**.

![Volume Type selection — Standard vs Hidden VeraCrypt volume](/assets/images/Sixth_blog/image4.jpg)
*We choose a Standard VeraCrypt volume. The Hidden option is an advanced plausible-deniability feature.*

<p>
  <img src="/assets/images/Sixth_blog/image4.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 5: Specify the Volume Location

On the **Volume Location** screen, you need to tell VeraCrypt where to save the encrypted container file and what to name it. Click **Select File…** and navigate to a location of your choice — in this lab we use:

```
C:\Users\will\Downloads\MyVolume
```

Type the filename directly into the field and click **Save**. The file does not need to have any particular extension — VeraCrypt will treat it as a binary container regardless of name.

> **Important Warning:** If you navigate to an existing file and select it, VeraCrypt will **delete and replace** that file with the new encrypted container. Always create a new filename that doesn't already exist to avoid accidental data loss.

Click **Next** to proceed.

![Volume Location screen showing the file path C:\Users\will\Downloads\MyVolume](/assets/images/Sixth_blog/image5.jpg)
*The Volume Location screen. We set the container path to C:\Users\will\Downloads\MyVolume.*

<p>
  <img src="/assets/images/Sixth_blog/image5.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 6: Configure Encryption Options

This screen lets you choose the **Encryption Algorithm** and **Hash Algorithm** that VeraCrypt will use to encrypt and protect your volume.

**Encryption Algorithm:** The default and recommended choice is **AES** (Advanced Encryption Standard). AES is a FIPS-approved cipher published in 1998 and approved by the U.S. government for protecting classified information up to the Top Secret level. It uses a 256-bit key with a 128-bit block size and 14 rounds (AES-256 in XTS mode). You can click **Test** to see live encryption/decryption speed benchmarks on your hardware, or **Benchmark** to compare all available algorithms.

**Hash Algorithm:** The default is **SHA-512**, which is used for key derivation (Password-Based Key Derivation Function). SHA-512 produces a 512-bit hash and is extremely resistant to brute-force attacks, especially when combined with VeraCrypt's high iteration count during key derivation.

Leave both settings at their defaults (**AES** and **SHA-512**) and click **Next**.

![Encryption Options screen showing AES algorithm and SHA-512 hash](/assets/images/Sixth_blog/image6.jpg)
*The Encryption Options screen. AES with SHA-512 is the strongest and most widely trusted configuration.*

<p>
  <img src="/assets/images/Sixth_blog/image6.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 7: Set the Volume Size

On the **Volume Size** screen, specify how large you want your encrypted container to be. You can set the size in KB, MB, GB, or TB. For this lab, we set the size to **5 MB** — sufficient for testing purposes. VeraCrypt also shows you the available free space on the destination drive so you don't accidentally create a volume that is too large.

> **Note on minimum sizes:** The minimum volume size for a FAT filesystem is 292 KB, for exFAT it is 424 KB, for NTFS it is 3792 KB and for ReFS it is 642 MB. Since we are using FAT for this small test volume, 5 MB is well within range.

Enter `5` in the size field, make sure **MB** is selected, and click **Next**.

![Volume Size screen set to 5 MB](/assets/images/Sixth_blog/image7.jpg)
*Setting the volume size to 5 MB for this demonstration. In real use, size this to fit your data needs.*

<p>
  <img src="/assets/images/Sixth_blog/image7.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 8: Set the Volume Password

This is one of the most critical steps. On the **Volume Password** screen, type a password in the **Password** field and confirm it in the **Confirm** field.

VeraCrypt provides detailed guidance on what constitutes a strong password:

- Avoid single dictionary words or combinations of 2–4 words
- Do not use names, dates of birth, or personally identifiable information
- Use a random mix of uppercase letters, lowercase letters, numbers and special characters (`@ ^ ~ $ * + –` etc.)
- VeraCrypt recommends a password of **20 or more characters** — the longer, the better
- The maximum password length is 128 characters

Enter your password, confirm it, and click **Next**.

![Volume Password screen with password fields filled in](/assets/images/Sixth_blog/image8.jpg)
*Enter and confirm a strong password. VeraCrypt recommends 20+ characters for maximum security.*

<p>
  <img src="/assets/images/Sixth_blog/image8.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 9: Acknowledge the Short Password Warning (If Applicable)

If the password you entered is shorter than VeraCrypt's recommended length, a warning dialog will appear:

> **WARNING: Short passwords are easy to crack using brute force techniques! We recommend choosing a password consisting of 20 or more characters. Are you sure you want to use a short password?**

For this lab, click **Yes** to proceed with a shorter password for demonstration purposes. In a real-world scenario, always use the longest, most complex password you can reliably remember — or use a password manager to store it.

![Short password warning dialog from VeraCrypt](/assets/images/Sixth_blog/image9.jpg)
*VeraCrypt warns about short passwords and recommends 20+ characters. In production, always use a strong, lengthy password.*

<p>
  <img src="/assets/images/Sixth_blog/image9.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 10: Generate Randomness by Moving Your Mouse

The **Volume Format** screen is where the actual encryption key material is generated. VeraCrypt uses a technique called **cryptographic randomness collection** — you are instructed to move your mouse as randomly as possible within the VeraCrypt window. The longer and more erratically you move the mouse, the higher the cryptographic strength of the encryption keys.

As you move your mouse, you will see the **Random Pool**, **Header Key**, and **Master Key** fields update with new pseudorandom values in real time. The green **Randomness Collected from Mouse Movements** progress bar at the bottom of the window fills up as you continue moving. Make sure this bar is fully or mostly filled before proceeding.

You can also choose the **Filesystem** (FAT, exFAT, NTFS, or ReFS) and **Cluster** size. For a 5 MB test volume, **FAT** with **Default** cluster size is appropriate.

Once you're satisfied with the randomness collected, begin moving your mouse to generate entropy.

![Volume Format screen showing mouse randomness collection beginning](/assets/images/Sixth_blog/image10.jpg)
*Move your mouse randomly across the VeraCrypt window to seed the cryptographic key generation process.*

<p>
  <img src="/assets/images/Sixth_blog/image10.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


![Volume Format screen with randomness bar partially filled](/assets/images/Sixth_blog/image11.jpg)
*The randomness bar fills as you continue moving your mouse — the more entropy, the stronger the keys.*

<p>
  <img src="/assets/images/Sixth_blog/image11.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 11: Click Format to Create the Encrypted Volume

Once you have collected sufficient randomness (the progress bar is sufficiently filled), click the **Format** button. VeraCrypt will now:

1. Generate the final encryption keys using your password and the collected randomness
2. Write the encrypted volume header
3. Format the virtual disk inside the container with the chosen filesystem
4. Fill the entire container with encrypted random data

You will see the progress bar fill to **100%** with the speed displayed in MB/s. For a 5 MB volume this completes almost instantly.

![Volume Format in progress at 100% completion](/assets/images/Sixth_blog/image12.jpg)
*VeraCrypt formats the encrypted volume — the progress bar reaches 100% and shows the write speed.*

<p>
  <img src="/assets/images/Sixth_blog/image12.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 12: Volume Successfully Created

Once formatting is complete, a confirmation dialog appears:

> **The VeraCrypt volume has been successfully created.**

Click **OK** to dismiss the confirmation.

![VeraCrypt success dialog — volume has been successfully created](/assets/images/Sixth_blog/image13.jpg)
*VeraCrypt confirms the encrypted volume has been created successfully.*

<p>
  <img src="/assets/images/Sixth_blog/image13.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


The wizard then shows a final **Volume Created** screen confirming the volume is ready for use and giving you the option to create another volume or exit. Click **Exit** to close the wizard and return to the main VeraCrypt window.

![Volume Created screen showing the volume is ready for use](/assets/images/Sixth_blog/image14.jpg)
*The Volume Created screen. The encrypted container is now ready to be mounted and used.*

<p>
  <img src="/assets/images/Sixth_blog/image14.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 13: Select a Drive Letter and Locate the Volume File

Back on the main VeraCrypt window, you will see a list of available drive letters (E: through Z:) displayed in the central panel. You need to select a **free drive letter** that VeraCrypt will assign to your encrypted volume when it is mounted. In this lab, we select drive letter **I:** by clicking it to highlight it in blue.

Next, ensure the volume file path shown in the **Volume** section at the bottom of the window is correct. It should already be pointing to `C:\Users\will\Downloads\MyVolume` — the file we created. If not, click **Select File…** and navigate to your container file.

![Main VeraCrypt window with drive letter I: selected and MyVolume file path set](/assets/images/Sixth_blog/image15.jpg)
*Select an available drive letter (I: in this case) and confirm the volume file path is correct.*

<p>
  <img src="/assets/images/Sixth_blog/image15.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 14: Mount the Volume — Enter the Password

With the drive letter selected and the volume file path set, click the **Mount** button at the bottom-left of the VeraCrypt window.

A password prompt dialog will appear, asking:

> **Enter password for C:\Users\will\Downloads\MyVolume**

Type the password you set during the volume creation steps. You can also configure options like **Use PIM**, **Cache passwords in memory**, or **Use keyfiles** — but for this lab, simply enter the password and click **OK**.

![VeraCrypt password prompt dialog when mounting MyVolume](/assets/images/Sixth_blog/image16.jpg)
*VeraCrypt prompts for the volume password before mounting. Enter the password you set during creation and click OK.*

<p>
  <img src="/assets/images/Sixth_blog/image16.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 15: Confirm the Volume is Mounted

After entering the correct password and clicking OK, VeraCrypt decrypts the volume header, verifies the password and mounts the encrypted container as drive **I:**. You will see the mounted volume appear in the drive list on the main window, showing:

| Drive | Volume | Size | Encryption Algorithm | Type |
|-------|--------|------|----------------------|------|
| I: | C:\Users\will\Downloads\MyVolume | 4.8 MB | AES | Normal |

The volume is now live and accessible as a regular Windows drive. Any files you copy into drive I: will be transparently encrypted on-the-fly using AES-256.

![VeraCrypt main window showing MyVolume mounted as drive I: with AES encryption](/assets/images/Sixth_blog/image17.jpg)
*The encrypted volume is mounted as drive I:. It shows the volume path, size (4.8 MB), encryption algorithm (AES), and type (Normal).*

<p>
  <img src="/assets/images/Sixth_blog/image17.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

### Step 16: Open the Mounted Volume and Store a Test File

Open **File Explorer** (press `Win + E` or click the folder icon in the taskbar). In the left panel, navigate to **This PC** and you will see **Local Disk (I:)** listed alongside your regular drives. Double-click it to open the encrypted volume.

The drive will initially be empty. To verify that the volume is working correctly, create a test file inside it. Right-click inside the drive window, select **New → Text Document** and name it `test`. You can also open it and add some content to confirm that saving and reading data works as expected.

This confirms that your encrypted volume is fully functional — files stored here are encrypted in real time using AES-256 and will be inaccessible to anyone who doesn't have the password.

![File Explorer showing Local Disk (I:) with a test text document inside the encrypted volume](/assets/images/Sixth_blog/image18.jpg)
*Opening drive I: in File Explorer shows the mounted encrypted volume. A test.txt file has been created inside it — this file is stored in an AES-256 encrypted state on disk.*

<p>
  <img src="/assets/images/Sixth_blog/image18.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 17: Unmount the Volume

When you are done working with the encrypted volume, you must **unmount** it before powering off or walking away from the machine. Leaving a volume mounted means it remains accessible — the encryption only protects data when the volume is dismounted.

To unmount, switch back to the main VeraCrypt window. The mounted volume (I:) will still be highlighted. Click the **Unmount** button at the bottom-left of the window.

VeraCrypt will close the virtual drive, flush any pending writes, and remove drive I: from the system. The `MyVolume` file on your hard drive is now just an opaque block of encrypted binary data — no filesystem, no filenames, no data is visible without the password.

![VeraCrypt main window with the mounted volume still shown, ready to be unmounted](/assets/images/Sixth_blog/image19.jpg)
*After working with the volume, return to VeraCrypt and click Unmount to securely lock the encrypted container.*

<p>
  <img src="/assets/images/Sixth_blog/image19.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>


---

### Step 18: Verify the Volume is Fully Dismounted

After clicking Unmount, drive I: is removed from the drive list in the VeraCrypt window. The volume row that previously showed `C:\Users\will\Downloads\MyVolume` under drive I: will now be blank — confirming the volume is no longer accessible.

At this point, if you open File Explorer and check **This PC**, drive I: will no longer appear. The `MyVolume` file still exists on disk in the Downloads folder, but it is nothing more than an encrypted blob — completely unreadable without VeraCrypt and the correct password.

![VeraCrypt main window showing drive I: is now empty — the volume has been unmounted](/assets/images/Sixth_blog/image20.jpg)
*Drive I: is now empty in the VeraCrypt drive list, confirming the volume has been successfully unmounted and the data is once again fully encrypted and locked.*

<p>
  <img src="/assets/images/Sixth_blog/image20.jpg" alt="Windows 11 lock screen inside VirtualBox" style="max-width:100%; border-radius:8px;">
</p>

---

## Key Security Insight

> **Note:** This lab demonstrates that in cases of system compromise — whether through remote access, physical theft, or an insider threat — if an attacker gains access to the machine, they will **not** be able to find or access the encrypted volume's contents without the password. To an attacker, the `MyVolume` file looks like a meaningless block of random binary data. There is no header, no filesystem signature and no filenames visible. All sensitive information located within the encrypted volume is fully safeguarded by AES-256 encryption — one of the strongest symmetric encryption algorithms in existence.

This is the power of VeraCrypt: transparent, strong encryption that protects your data at rest without changing your workflow once the volume is mounted.

---

## Key Takeaways

In this walkthrough we successfully:

- **Launched VeraCrypt** from the Windows 11 Start menu and opened the Volume Creation Wizard
- **Created an encrypted file container** at `C:\Users\will\Downloads\MyVolume` using the Standard volume type
- **Configured AES-256 encryption** with SHA-512 key hashing — the strongest available default settings
- **Set a volume size** of 5 MB and secured it with a password
- **Generated cryptographic randomness** using mouse movement entropy to seed the encryption key generation
- **Formatted and finalised** the encrypted volume, receiving confirmation of successful creation
- **Mounted the volume** as drive I: using the correct password, making it accessible as a normal Windows drive
- **Stored a test file** inside the mounted volume, confirming live AES-256 encryption is working
- **Unmounted the volume** and verified that the drive is no longer accessible — leaving only an indecipherable encrypted file on disk

VeraCrypt is an excellent first step toward protecting sensitive data at rest. Whether you're a student, a professional, or just someone who values privacy, keeping sensitive information inside an encrypted container adds a powerful layer of protection that no attacker can bypass without your password.

---

> **Disclaimer:** The tools and techniques discussed in this guide are intended strictly for educational purposes and for protecting your own data. Always ensure you comply with the laws and policies of your organisation or jurisdiction when using encryption software. Never use these techniques to conceal illegal activity.
