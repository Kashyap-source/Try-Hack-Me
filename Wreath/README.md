# Wreath

![alt text](https://assets.tryhackme.com/room-banners/wreath_banner.png)

**Source:**  MuirlandOracle

***Description:***
  Learn how to pivot through a network by compromising a public facing web machine and tunnelling your traffic to access other machines in Wreath's network.
  
  ***Related Hosting Links***

- *TryHackMe.com*
  - Link: https://tryhackme.com/room/wreath
 
- **Task 1: Introduction**

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
 
 
