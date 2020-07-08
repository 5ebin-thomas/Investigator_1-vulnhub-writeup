# Investigator:1 ~Vulnhub Writeup

Be the investigator to finish this machine,Its for only beginners, Share your Screen shot on telegram group, Group link will be in flag.

Author: Sivanesh Kumar

Download link- [https://download.vulnhub.com/investigator/Investigator.ova](https://download.vulnhub.com/investigator/Investigator.ova)

## SCANNING

Scanning target ip-address using nmap full port scanning

**nmap -p- 192.168.122.136**

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled.png)

Lets find out service version.

**nmap -sV -A 192.168.122.136**

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%201.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%201.png)

**nmap -sV -A -p 22000 192.168.122.136**

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%202.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%202.png)

After finding ip address lets do vulnerability scanning.

**nmap -sV -A --script vuln -p 5555,8080,22000 192.168.122.136**

```jsx
root@kali:~# nmap -sV -A --script vuln -p 5555,8080,22000 192.168.122.136
Starting Nmap 7.70 ( https://nmap.org ) at 2020-07-08 07:51 EDT
Nmap scan report for 192.168.122.136
Host is up (0.0014s latency).

PORT      STATE SERVICE  VERSION
5555/tcp  open  freeciv?
8080/tcp  open  http     PHP cli server 5.5 or later
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
|_http-majordomo2-dir-traversal: ERROR: Script execution failed (use -d to debug)
| http-slowloris-check: 
|   VULNERABLE:
|   Slowloris DOS attack
|     State: LIKELY VULNERABLE
|     IDs:  CVE:CVE-2007-6750
|       Slowloris tries to keep many connections to the target web server open and hold
|       them open as long as possible.  It accomplishes this by opening connections to
|       the target web server and sending a partial request. By doing so, it starves
|       the http server's resources causing Denial Of Service.
|       
|     Disclosure date: 2009-09-17
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2007-6750
|_      http://ha.ckers.org/slowloris/
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
22000/tcp open  ssh      Dropbear sshd 2014.66 (protocol 2.0)
MAC Address: 00:0C:29:5F:64:48 (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   1.42 ms 192.168.122.136

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 632.98 seconds
root@kali:~#
```

We found nothing usefull in vulnerability scanning.

## Enumeration

Lets open port 8080 in web browser.

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%203.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%203.png)

Running nikto. **nikto -h http://192.168.122.136:8080/**

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%204.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%204.png)

We found nothing usefull

After this i use **dirb** to retrieve more directories and found backdoor.php. But it says fake backdoor.

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%205.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%205.png)

After some research on port 5555, I found 5555 can be exploited using **adb.** 

To install adb:

1. in kali use command - **sudo apt-get install adb**
2. in windows download appie  

Lets connect android device using adb.

**adb connect 192.168.122.136:5555**

**adb shell**

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%206.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%206.png)

**su root** (used to substitute as root user)

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%207.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%207.png)

## Exploitation

After getting root shell, go to directory data/root

**cd /data/root**

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%208.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%208.png)

We found flag.txt.

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%209.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%209.png)

But this is the first level, We found some secret key. It can be password of lock screen.

I tried unlocking lockscreen password using secret key but failed to open it.

Then i remove gesture.key and password.key files to bypass lockscreen.

**cd /data/system**

**rm gesture.key**

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2010.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2010.png)

**rm password.key**

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2011.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2011.png)

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2012.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2012.png)

After deleting both files reboot android device using command

**adb reboot** 

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2013.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2013.png)

And then try to unlock device by pressing enter.

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2014.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2014.png)

After searching for flag, i found it in messaging app. It was also asking for password. I used secret key password as pattern to unlock it.

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2015.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2015.png)

Successfully found flag in (666) 666-6666 message.

![Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2016.png](Investigator%201%20Vulnhub%20writeup%20f102375c32c945db8104e44d4fe4fb7f/Untitled%2016.png)
