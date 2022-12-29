# Welcome to Wakanda  
**A New Breed of CTF!**  
Our mission is simple, the task is challenging, but the reward will be amazing!  
By the end of this mission, you will be 1 step closer to becoming an 3L1T3 H4CK3R! Will you take the challenge and rise to the top?  
  
The resources & knowledge I put into this is to help everyone else become an amazing hacker. If you have found this repo useful and helpful, please consider buying me a cup of coffee;  
BTC ADR:  
bc1q7np8xjmz40knvu6lma8xqmqkpwkssmwpmq9vw0  
  
## Wakanda Setup & Description  
**What is Wakanda?**  
Think of it like you are a special agent and you are trying to help secure the Marvel Paradise of Wakanda. We all know that they mine Vibranium right? But there are dangers lurking around every corner.  
  
**Wakanda Description:**  
"A new Vibranium market will soon be online in the dark net. Your goal, get your hands on the root file containing the exact location of the mine."  
  
**Wakanda Flags:**  
There are 3 Known flags inside the VM (flag1.txt, flag2.txt, root.txt)  
Our mission is to collect all 3 of these flags!  
The only intel you are allowed to have is;  
* DHCP: Enable This  
* IP Address: Automatically Assign This  
* Follow Your Intuitions  
* And Remember to Enumerate!  
**Happy Hacking**  
  
**Download Wakanda**  
We need to download the VM from the source. We are getting the Virtual Machine from a popular website "VulnHub" (https://www.vulnhub.com).  
If you are following this repo, we will be downloading the following Virtual Machine from that site;  
https://www.vulnhub.com/entry/wakanda-1,251/  
This download should already be a ".ova" file, so we just need double click on this ".ova" file and import this into our VirtualBox.   
  
If you do not already have VirtualBox, you can download it for free here;  
https://www.virtualbox.org/wiki/Downloads  
  
After you have downloaded & double clicked on the ".ova" to import the configurations, you need to make some minor adjustments to your network drivers on this VM.  
Set the Network Drivers to "Nat Network" and select the Hacking Network you have already setup & configured.  
  
Once that is done, fire up the machine and you should see a Debian boot loading screen, just click "enter" and then you will be directed to a Debian GNU/Linux Login Screen.  
  
This is where the fun begins. We need to crack this login screen to get access to the VM!  
  
## Detecting the Wakanda Virtual Machine  
**Congrats On Loading The VM!** If you have done everything correct, you should be faced with a default terminal style log in screen. And now the fun can begin!  
  
Now, the first thing we need to figure out, we do not even know what the IP Address of that VM is. So we will perform a simple netdiscover command like so;  
```
$ sudo netdiscover -r 10.0.0.0/24
 4 Captured ARP Req/Rep packets, from 4 hosts.   Total size: 240                                                                                                                                                                           
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
 10.0.0.1        52:54:00:12:35:00      1      60  Unknown vendor                                                                                                                                                                          
 10.0.0.2        52:54:00:12:35:00      1      60  Unknown vendor                                                                                                                                                                          
 10.0.0.3        08:00:27:31:f8:b3      1      60  PCS Systemtechnik GmbH                                                                                                                                                                  
 10.0.0.6        08:00:27:60:fb:91      1      60  PCS Systemtechnik GmbH
```
  
Now it is time to see what ports are opened up on our target. In my case, my target is 10.0.0.6. Let's open a new tab and use nmap in order to discover ports.  
  
```
$ sudo nmap -sS -sV -A -p- 10.0.0.6  
Nmap scan report for 10.0.0.6
Host is up (0.00032s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
80/tcp    open  http    Apache httpd 2.4.10 ((Debian))
|_http-title: Vibranium Market
|_http-server-header: Apache/2.4.10 (Debian)
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          39413/tcp   status
|   100024  1          41563/udp   status
|   100024  1          42132/tcp6  status
|_  100024  1          48816/udp6  status
3333/tcp  open  ssh     OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
| ssh-hostkey: 
|   1024 1c984756fcb814088f93ca36447fea7a (DSA)
|   2048 f1d50478d33a9bdc13df0f5f7ffbf426 (RSA)
|   256 d834415d9bfe51bcc64e02145ee108c5 (ECDSA)
|_  256 0ef58d293c7357c738086d5084b66c27 (ED25519)
39413/tcp open  status  1 (RPC #100024)
MAC Address: 08:00:27:60:FB:91 (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.32 ms 10.0.0.6

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.01 seconds

```
**ALRIGHT!** Looks like we have several ports that are opened up including "SSH" This should be pretty awesome to get into!  

So, what do we do now? I would recommend creating a new notes folder so that you can use it to save your information for later. In my case, this repo is indeed my notes! LOL  
  
Once you have created your file for note taking, copy/paste the results of your NMap scan.  
  
Now we are ready to analyze the results from the machine. 
