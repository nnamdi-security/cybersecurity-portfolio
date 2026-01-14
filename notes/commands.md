## Linux CLI Command
To delete a directory: *rm -r*

To decompress a .XZ archive: *tar -xvJf tsetup.6.1.0.tar.xz
./tsetup*



**Using the nslookup command**

set keyword used to query for the mail server mx record within a domain:set querytype=mx or set type=mx
To find the domain name servers configured for cisco.com in nslookup interactive mode
> set type=ns

> cisco.com
To Change the server used to perform lookups.
> server 8.8.8.8

> set type=any

> netacad.com

The **any** query type can retrieve much, or all, of the information contained in the DNS record for a host name.
whois 72.163.5.201
dig netacad.com any
dig cisco.com 8.8.8.8 ns
dig cisco.com AAAA
Reserve DNS lookup
dig -x 72.163.5.201
Host can also be used to perform a quick IP address lookup for a known hostname
host hsrp-72-163-10-1.cisco.com

**To locate a place with coordinates on map: https://www.google.com/maps?q=35.78851,-78.6418**

You can get these coordinates from a photo metadata using exiftool.

**Find Information about Email Breaches.**

haveibeenpwned.com

**To SSH into another device running SSH server**

On powershell: ssh username@ip address


## SHODAN COMMANDS
- shodan init <paste your API key here>
- shodan -h
- shodan search webcam. Note: Searching with filters is not available with a free API key.
- shodan info
- shodan myip command to find the registered IP address that corresponds to your device.
- shodan stats webcam



**Safe Upgrade Routine**

### Make sure nothing is half-configured
- sudo dpkg --configure -a
- sudo apt -f install

### 1) See what would change
- apt list --upgradable
- apt -s full-upgrade     # dry-run (no changes)

### 2) Do the real upgrade (Kali prefers full-upgrade)
- sudo apt update
- sudo apt full-upgrade -y

### 3) Cleanup
- sudo apt autoremove --purge -y
- sudo apt clean



### Pairing Bluetooth Device

- sudo bluetoothctl
- power on
- agent on
- default agent
- scan on
- [bluetooth]# pair XX:XX:XX:XX:XX:XX
- [bluetooth]# trust XX:XX:XX:XX:XX:XX



### SCREENSHOT ON KALI

Fn + Alt + Shift → active window.


### Telegram

telegram &
The & puts it in the background so your terminal stays free


### Check release version & codename

Cat /etc/os-release

## SSH COMMANDS

### 1. Install OpenSSH server if not already installed
- sudo apt update
- sudo apt install -y openssh-server

### 2. Enable the SSH service to start on boot
- sudo systemctl enable ssh

### 3. Start the SSH service immediately
- sudo systemctl start ssh

### 4. Verify that SSH is running
- sudo systemctl status ssh

### 5. (Optional) Check that the port is listening (default: 22)
- ss -tulpn | grep ssh


## GOBUSTER COMMANDS

🔹 Example Command:
gobuster dir -u http://192.168.1.100/ -w /usr/share/wordlists/dirb/common.txt


🔹 Useful Options:
-x php,html,txt → Check for files with specific extensions.

 gobuster dir -u http://192.168.1.100/ -w /usr/share/wordlists/dirb/common.txt -x php,html,txt

-t 50 → Number of threads (increase speed, default is 10).


-o results.txt → Save output to a file.


-q → Quiet mode (only shows found results).


-k → Skip SSL/TLS certificate verification (useful with HTTPS and self-signed certs).



**Example with extensions and threading:**
gobuster dir -u http://targetsite.com/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt -t 50 -o gobuster_results.txt


We can use GoBuster to enumerate available subdomains of a given domain using the dns flag to specify DNS mode. First, let us clone the SecLists GitHub repo, which contains many useful lists for fuzzing and exploitation:
xulhack@htb[/htb]$ git clone https://github.com/danielmiessler/SecLists
xulhack@htb[/htb]$ sudo apt install seclists -y

Next, add a DNS Server such as 1.1.1.1 to the /etc/resolv.conf file. 
🔹 General Syntax for DNS Mode:
gobuster dns -d DOMAIN -w /path/to/wordlist.txt

xulhack@htb[/htb]$ gobuster dns -d inlanefreight.com -w /usr/share/SecLists/Discovery/DNS/namelist.txt


 Gobuster can also perform virtual host (vhost) enumeration to uncover hidden subdomains or hostnames pointing to the same server.

🔹 General Syntax for Vhost Enumeration
gobuster vhost -u http://example.com -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt
Sometimes vhost enumeration won’t work unless you pass a Host header. If you’re testing a target where the IP is known (e.g., 192.168.1.100), you can add it to /etc/hosts temporarily like this:
sudo nano /etc/hosts

Add:
192.168.1.100   example.com

Then run Gobuster vhost enumeration against example.com.

### NANO FILE EDITOR

Save file: *Ctrl + O*

Confirm save: *Enter*

Exit nano: *Ctrl + X*





