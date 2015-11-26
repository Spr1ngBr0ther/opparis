Welcome to the Netowk Mapper tutorial. 

Nmap is a well known and loved tool by hackers, and has remained popular for years. Nmap was used by trinity in the matrix for fucks sake. So lets discuss how to get it and what it is.

nmap can be found at the following url:		https://nmap.org/download.html

It comes in a few different flavors for many different operating systems. Assuming you have a recent copy of nmap, which you can download there or obtain through a package manager, you can begin by scanning your own computer.

`nmap localhost`

will return something like this:

`[kod@19b53ae5-14f9-c774-e344-e89c7712675c ~]$ nmap localhost`

`Starting Nmap 6.47 ( http://nmap.org ) at 2015-11-26 09:16 UTC`

`Nmap scan report for localhost (127.0.0.1)`

`Host is up (0.00014s latency).`

`Not shown: 999 closed ports`

`PORT   STATE SERVICE`

`22/tcp open  ssh`

`Nmap done: 1 IP address (1 host up) scanned in 27.79 seconds`


It will take a while depending on what you have running and the speed of your machine.

You can see that we have some information, not in real time, and not horribly detailed. Lets break down what we see.

First, we see a `Host is up (0.00014s latency).` line. This means nmap was able to successfully issue an ICMP packet and receive a response. 

Next we see `Not shown: 999 closed ports`. This means the scanner only hit 1000 ports, of common services. This doesnt mean nothing else is running on the machine, as there are over 60000 ports to choose from. If you need to scan all of them quickly, use masscan and the use nmap for a detailed report of each open service.

Next we get the service. `22/tcp open  ssh` means that a tcp socket was found on port 22, which is COMMONLY the ssh protocol. It could be anything, we didn't tell nmap to check. Lets do that.

`nmap localhost -A`

and we get

`[kod@19b53ae5-14f9-c774-e344-e89c7712675c ~]$ nmap localhost -A`

`Starting Nmap 6.47 ( http://nmap.org ) at 2015-11-26 09:21 UTC`

`Nmap scan report for localhost (127.0.0.1)`

`Host is up (0.000087s latency).`

`Not shown: 999 closed ports`

`PORT   STATE SERVICE VERSION`

`22/tcp open  ssh     OpenSSH 7.1 (protocol 2.0)`

`Service detection performed. Please report any incorrect results at http://nmap.org/submit/ .`

`Nmap done: 1 IP address (1 host up) scanned in 27.89 seconds`

Note that we can now see the SSH server's application version!

Lets see what we can discover about the host OS. This requires root because it uses the network stack at a low level.

`sudo nmap -O localhost`

`Device type: general purpose`

`Running: Linux 3.X`

`OS CPE: cpe:/o:linux:linux_kernel:3`

`OS details: Linux 3.7 - 3.15`

`Network Distance: 0 hops`


So we can tell what OS and the application protocol and version, this is very handy! But it is very slow!

We can speed this all up and get data in realtime from nmap by telling it to be very verbose, and combine all these directives into a single command.

`sudo nmap localhost -O -A -vv`

We can also use the magic of network masks to scan an entire network segment with a single, same command.

`sudo nmap 10.0.0.1/24 -O -A -vv`

This will scan 1000 ports for 10.0.0.1-10.0.0.255 and report in real time, and then issue a final formatted report when it is complete.

We can also decide that we are only interested in a specific port on a specific network.... let's say all telnet ports in syria?

`nmap -n -Pn -vv  -A -p 23 5.0.0.0/16 5.104.128.0/21 5.134.200.0/21 5.134.224.0/19 31.9.0.0/16 31.193.64.0/20 37.48.128.0/18 37.48.192.0/19 46.53.0.0/17 46.57.128.0/17 46.58.128.0/17 46.161.192.0/18 46.213.0.0/16 77.44.128.0/17 78.110.96.0/20 78.155.64.0/19 82.137.192.0/18 88.86.0.0/19 90.153.128.0/17 91.144.0.0/18 94.141.192.0/19 94.252.128.0/17 95.87.112.0/21 95.140.96.0/20 95.159.0.0/18 109.238.144.0/20 130.0.240.0/20 130.180.128.0/18 178.52.0.0/16 178.171.128.0/17 178.253.64.0/18 185.4.84.0/22 185.19.124.0/22 185.23.184.0/22 185.24.60.0/22 185.41.84.0/22 185.52.232.0/22 185.54.132.0/22 185.84.48.0/22 185.84.236.0/22 185.92.28.0/22 185.92.88.0/22 185.96.108.0/22 185.110.104.0/22 185.114.240.0/22 185.121.184.0/22 188.139.128.0/17 188.160.0.0/16 188.229.128.0/17 188.247.0.0/19 212.11.192.0/19 213.178.224.0/19 217.20.208.0/20 195.60.236.0/22 196.2.4.0/22 198.51.143.0/24 198.51.144.0/23 198.51.146.0/24`


So in order to make this efficient, you can note the added -n and -Pn. -Pn will tell nmap not to ping the target first. -n will tell nmap to skip reverse dns. By skipping these 2 steps, we are able to immediatly start scanning for open ports.

Maybe you prefer looking for FTP servers in syria?

`nmap -n -Pn -vv  -A -p 21 5.0.0.0/16 5.104.128.0/21 5.134.200.0/21 5.134.224.0/19 31.9.0.0/16 31.193.64.0/20 37.48.128.0/18 37.48.192.0/19 46.53.0.0/17 46.57.128.0/17 46.58.128.0/17 46.161.192.0/18 46.213.0.0/16 77.44.128.0/17 78.110.96.0/20 78.155.64.0/19 82.137.192.0/18 88.86.0.0/19 90.153.128.0/17 91.144.0.0/18 94.141.192.0/19 94.252.128.0/17 95.87.112.0/21 95.140.96.0/20 95.159.0.0/18 109.238.144.0/20 130.0.240.0/20 130.180.128.0/18 178.52.0.0/16 178.171.128.0/17 178.253.64.0/18 185.4.84.0/22 185.19.124.0/22 185.23.184.0/22 185.24.60.0/22 185.41.84.0/22 185.52.232.0/22 185.54.132.0/22 185.84.48.0/22 185.84.236.0/22 185.92.28.0/22 185.92.88.0/22 185.96.108.0/22 185.110.104.0/22 185.114.240.0/22 185.121.184.0/22 188.139.128.0/17 188.160.0.0/16 188.229.128.0/17 188.247.0.0/19 212.11.192.0/19 213.178.224.0/19 217.20.208.0/20 195.60.236.0/22 196.2.4.0/22 198.51.143.0/24 198.51.144.0/23 198.51.146.0/24`

Or webservers?

`nmap -n -Pn -vv -A -p 80 5.0.0.0/16 5.104.128.0/21 5.134.200.0/21 5.134.224.0/19 31.9.0.0/16 31.193.64.0/20 37.48.128.0/18 37.48.192.0/19 46.53.0.0/17 46.57.128.0/17 46.58.128.0/17 46.161.192.0/18 46.213.0.0/16 77.44.128.0/17 78.110.96.0/20 78.155.64.0/19 82.137.192.0/18 88.86.0.0/19 90.153.128.0/17 91.144.0.0/18 94.141.192.0/19 94.252.128.0/17 95.87.112.0/21 95.140.96.0/20 95.159.0.0/18 109.238.144.0/20 130.0.240.0/20 130.180.128.0/18 178.52.0.0/16 178.171.128.0/17 178.253.64.0/18 185.4.84.0/22 185.19.124.0/22 185.23.184.0/22 185.24.60.0/22 185.41.84.0/22 185.52.232.0/22 185.54.132.0/22 185.84.48.0/22 185.84.236.0/22 185.92.28.0/22 185.92.88.0/22 185.96.108.0/22 185.110.104.0/22 185.114.240.0/22 185.121.184.0/22 188.139.128.0/17 188.160.0.0/16 188.229.128.0/17 188.247.0.0/19 212.11.192.0/19 213.178.224.0/19 217.20.208.0/20 195.60.236.0/22 196.2.4.0/22 198.51.143.0/24 198.51.144.0/23 198.51.146.0/24`


Maybe you just want to look for stuff across the entire internet.

`nmap -p 80 -vv -A -n -Pn -iR 0`

Happy scanning! 

- kernel zero day