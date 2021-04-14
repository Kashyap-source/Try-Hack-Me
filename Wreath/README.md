# Wreath

![alt text](https://assets.tryhackme.com/room-banners/wreath_banner.png)

**Source:**  MuirlandOracle

***Description:***
  Learn how to pivot through a network by compromising a public facing web machine and tunnelling your traffic to access other machines in Wreath's network.
  
  ***Related Hosting Links***

- *TryHackMe.com*
  - Link: https://tryhackme.com/room/wreath
 
- **Task 1: Introduction**
   ![alt text](https://github.com/kashyap-source/Try-Hack-Me/blob/master/Wreath/Image/pic.png)

  Wreath is designed as a learning resource for beginners with a primary focus on:

    - Pivoting
    - Working with the Empire C2 (Command and Control) framework
    - Simple Anti-Virus evasion techniques

    The following topics will also be covered, albeit more briefly:

     - Code Analysis (Python and PHP)
     - Locating and modifying public exploits
     - Simple webapp enumeration and exploitation
     - Git Repository Analysis
     - Simple Windows Post-Exploitation techniques
     - CLI Firewall Administration (CentOS and Windows)
     - Cross-Compilation techniques
     - Coding wrapper programs
     - Simple exfiltration techniques
     - Formatting a pentest report

   These will be taught in the course of exploiting the Wreath network.

   This is designed as almost a sandbox environment to follow along with the teaching content; the focus will be on the above teaching points, rather than on initial access and    privilege escalation exploits (contrary to other boxes on the platform where the focus is on the challenge).
 
   ***Prerequisites:***
     This network is designed for beginners, but assumes basic competence in the Linux command line and fundamental hacking methodology. The ability to read and write a little      code will also be useful. Any other required knowledge will be linked throughout the tasks.
 
 
- ***Task 2: Accessing the Network***
 
    ***Controlling the Network:***

    The network has three states: Running, Stopped, and Resetting.

    The current state can be shown at the top right of the network box at the top of the page:
    ![alt text](https://assets.tryhackme.com/additional/wreath-network/fe129fa984de.png)

    - Running means that the network is fully operational and can be connected to at will
    - Stopped indicates that the network has gone to sleep. This happens when no one has pressed the "Extend" button within a set time limit so as to prevent the network from         being constantly running with no one using it. It can be restarted by pressing the "Start" button. This does not reset the network back to a clean copy, so anything stored       on the targets should still be there
    - Resetting indicates that the network is currently in the process of being wiped clean and resetting back to its default state. This can be used when something (or someone)       has happened to one of the targets rendering it broken

    The three buttons below the network map can be used to control this functionality:
 
    ![alt text](https://assets.tryhackme.com/additional/wreath-network/fbf6ced6514d.png)

    - The "Start" button restarts the network once stopped
    - The "Extend" button prevents the network from going to sleep. This button also contains a timer showing how long until the network shuts down
    - The "Reset" button initiates a full wipe of the network. This requires a percentage of users in the network to click the button, thus preventing a single person from             spamming resets 

- ***Task 3  Intro Backstory:***

   Out of the blue, an old friend from university: Thomas Wreath, calls you after several years of no contact. You spend a few minutes catching up before he reveals the real        reason he called:

   "So I heard you got into hacking? That's awesome! I have a few servers set up on my home network for my projects, I was wondering if you might like to assess them?"

   You take a moment to think about it, before deciding to accept the job -- it's for a friend after all.

   Turning down his offer of payment, you tell him:

- ***Task 4  Intro Brief:***
    
    Thomas has sent over the following information about the network:

    There are two machines on my home network that host projects and stuff I'm working on in my own time -- one of them has a webserver that's port forwarded, so that's your way     in if you can find a vulnerability! It's serving a website that's pushed to my git server from my own PC for version control, then cloned to the public facing server. See if     you can get into these! My own PC is also on that network, but I doubt you'll be able to get into that as it has protections turned on, doesn't run anything vulnerable, and     can't be accessed by the public-facing section of the network. Well, I say PC -- it's technically a repurposed server because I had a spare license lying around, but same       difference.

   From this we can take away the following pieces of information:

  - There are three machines on the network
  - There is at least one public facing webserver
  - There is a self-hosted git server somewhere on the network
  - The git server is internal, so Thomas may have pushed sensitive information into it
  - There is a PC running on the network that has antivirus installed, meaning we can hazard a guess that this is likely to be Windows- 
  - By the sounds of it this is likely to be the server variant of Windows, which might work in our favour
  - The (assumed) Windows PC cannot be accessed directly from the webserver

   Before we start, if you are using Kali, make sure that it's up to date:
     - sudo apt update && sudo apt upgrade


***Task 5  Webserver Enumeration:***
   
   As with any attack, we first begin with the enumeration phase. Completing the Nmap room (if you haven't already) will help with this section.
   Thomas gave us an IP to work with (shown on the Network Panel at the top of the page). Let's start by performing a port scan on the first 15000 ports of this IP.
   Note: Here (and in general), it's a good idea to save your scan results to a file so you don't have to re-run the same scan twice.
  
   How many of the first 15000 ports are open on the target?
   - 4
   
   Perform a service scan on these open ports.
   What OS does Nmap think is running?
   - CentOS

   Okay, we know what we're dealing with.
   Open the IP in your browser -- what site does the server try to redirect you to?
    
   - https://thomaswreath.thm
   
   You will have noticed that the site failed to resolve. Looks like Thomas forgot to set up the DNS!

   Add it to your hosts file manually. This can be accomplished by editing the /etc/hosts file on Linux/MacOS, or C:\Windows\System32\drivers\etc\hosts on Windows, to include t    the IP address, followed by a tab, then the domain name. Note: this must be done as root/Administrator.

   It should look something like this when done, although the IP address and domain name will be different:

   10.10.10.10 example.thm
     
   - No answer needed
 
   Reload the webpage -- it should now resolve, but it will give you a different error related to the TLS certificate. This occurs because the box is not really connected to the    internet and so cannot have a signed TLS certificate. In this instance it is safe to click "Advanced" -> "Accept Risk"; however, you should never do this in the real world.

   In real life we would perform a "footprinting" phase of the engagement at this point. This essentially involves finding as much public information about the target as            possible  and noting it down. You never know what could prove useful!

   Read through the text on the page. What is Thomas' mobile phone number?
     
   - +447821548812
  
  Let's have a look at the highest open port.
  Look back at your service scan results: what server version does Nmap detect as running here?
    
   - MiniServ 1.890 (Webmin httpd)

  Put your answer to the last question into Google.

  It appears that this service is vulnerable to an unauthenticated remote code execution exploit!

  What is the CVE number for this exploit?
    
   - CVE-2019-15107













