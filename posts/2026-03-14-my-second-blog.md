---
title: Installing Metasploitable 2; A Step-by-Step VirtualBox Guide
author: Illusivehacks
date: 2026-03-14 10:00:00 +0300
description: A detailed guide on downloading, installing and troubleshooting Metasploitable 2 in VirtualBox for your cybersecurity lab.

image: /assets/images/Second_blog/second_blog.jpg

categories: [Cybersecurity, Tutorial, Metasploitable, VirtualBox]
tags: [Metasploitable 2, VirtualBox, Installation, Pentesting Lab, Ubuntu, Troubleshooting, Cybersecurity]
---

# Setting Up Your Hacking Lab: Installing Metasploitable 2 in VirtualBox
Welcome back to the lab! Every ethical hacker needs a safe place to practice their skills. Think of it as a digital dojo where you can spar without breaking anything important. Today, we're building the cornerstone of that dojo: Metasploitable 2.

This intentionally vulnerable virtual machine is the perfect target for practicing with tools like Metasploit, Nmap and more. Let's get it up and running in VirtualBox.

**What's the Deal with Metasploitable 2?**

### Q: So, what exactly is Metasploitable 2?
**Ans:** Imagine a training dummy for martial arts. It's designed to be hit. Metasploitable 2 is the digital equivalent—a Linux-based virtual machine packed with dozens of exploitable vulnerabilities. It’s a completely safe and legal playground for you to test attacks, understand how they work and learn how to defend against them without causing any real-world damage.

### Q: How do I get it and get it running in VirtualBox?
**Ans:** It's a straightforward process, but there's a common pitfall we need to navigate. Follow these steps closely.

First, grab the file. In your browser, head over to the official Rapid7 download page:

🔗 https://information.rapid7.com/metasploitable-download.html

Once the download is complete, extract the contents of the ZIP file to a folder on your computer. You'll find the crucial .vmdk (**Virtual Machine Disk**) file inside.

Now, let's build its new home in VirtualBox.

Create the New VM: Open VirtualBox and click on the "New" button. Give your new machine a name (like **"Metasploitable 2"**), choose a folder to store it and proceed with the default settings for now. You can adjust RAM later if needed, but the defaults work fine. Click "Finish" to create the shell.

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image1.jpg)

Attach the Metasploitable Hard Drive: Select your newly created "Metasploitable 2" VM from the list on the left and click the "Settings" button (**the orange gear**). Navigate to the Storage section. You'll see a controller. Click on the empty disk icon under "Controller: IDE" and then click the disk icon on the far right to "Choose a disk file." Browse to where you extracted the files and select the Metasploitable.vmdk. Click "OK" to save the settings. You're now ready to fire it up!

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image2.jpg)
### Q: I tried to boot it, but I got an error! The screen is frozen or has a bunch of text. What went wrong?
**Ans:** Ah, you've hit the classic snag. Metasploitable 2 is based on an older version of Ubuntu (8.04) and modern computers with their newer processors and hardware can confuse it. This results in a kernel panic or a boot loop. Don't worry, it's a rite of passage! You'll see a screen similar to this:

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image3.jpg)

But we have a fix. It's time to do some manual troubleshooting.

Enter the Boot Menu: Reboot the VM (from the VirtualBox menu: Machine -> Reset). As soon as the screen lights up again, start tapping the Esc key repeatedly until you are presented with a boot menu.

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image4.jpg)

Edit the Boot Entry: Use the arrow keys to highlight the first option, "Ubuntu 8.04, Kernel 2.6.24-16-server (**recovery mode**)" , but don't press Enter. Instead, press the e key to edit the boot commands.

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image5.jpg)

Find the Kernel Line: You'll see a few lines of text. Use the arrow keys to navigate down to the line that starts with kernel /vmlinuz-2.6.24-16-server... . It will look something like this:
*kernel /vmlinuz-2.6.24-16-server root=/dev/mapper/metasploitable-root ro quiet splash*

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image6.jpg)

Add the Magic Parameters: Press e again to edit this specific line. Use the arrow key to go to the very end of the line. Add a space and then type the following two words:
**noapic nolapic**
So the end of the line will now look like: ... ro quiet splash noapic nolapic

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image7.jpg)

Boot! Press Enter to save this temporary change and go back to the previous screen. Now, press the b key. This will boot the system with your new parameters.

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image8.jpg)

The VM should now boot successfully without any errors. You'll be greeted with a login prompt.

### Q: It worked! How do I log in?
Ans: The default credentials for Metasploitable 2 are famously simple. At the login prompt, type:

Username: msfadmin

Password: msfadmin

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image9.jpg)

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image10.jpg)

And there you have it! You are now logged into your very own vulnerable machine. From here, you can run ifconfig to find its IP address (make sure your VirtualBox network settings are correct, like using a Host-Only or NAT Network adapter) and start your scans and attacks from your Kali Linux machine.

### Q: Do I have to do that noapic nolapic dance every time I boot?
Ans: No, you don't! Let's make that fix permanent. It's just good system hygiene. Log in with the msfadmin credentials and follow these steps in the terminal:

Open the GRUB bootloader configuration file. Type this command and press Enter:
sudo nano /boot/grub/menu.lst

Find the two kernel lines. Scroll down in the file until you see two lines that start with kernel /vmlinuz-2.6.24-16-server. One ends with ro quiet splash and the other ends with ro single.

Edit them permanently. Just like before, go to the end of each of these lines and add a space followed by noapic nolapic. It should look like this:

*kernel /vmlinuz-2.6.24-16-server root=/dev/mapper/metasploitable-root ro quiet splash noapic nolapic*

*kernel /vmlinuz-2.6.24-16-server root=/dev/mapper/metasploitable-root ro single noapic nolapic*

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image11.jpg)

Save, Exit and Reboot. Press Ctrl+O to save the file, then Ctrl+X to exit nano. Finally, reboot your system to test it out:
sudo reboot

![Metasploitable 2 Installation Step](/assets/images/Second_blog/image12.jpg)

Now, your Metasploitable 2 VM will boot up perfectly every single time, no manual intervention needed.

You've successfully built your target. Now go ahead—scan it, poke it, break it and learn from it. Just remember to keep it confined to your own lab network!
