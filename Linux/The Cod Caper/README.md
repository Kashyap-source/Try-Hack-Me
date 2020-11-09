# The Cod Caper

![alt text]()

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

