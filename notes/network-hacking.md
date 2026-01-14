## Pre-connection Attacks

**Changing MAC address**
Bring down the interface:
ifconfig wlan0 down
ifconfig wlan0 hw ether <new mac address>. ensure to start with 00
ifconfig wlan0 up ✅

**Note: MAC address will revert back to original after a restart**
To set an adapter to monitor mode
iwconfig
ifconfig wlan0 down
aimon-ng check kill
iwconfig wlan0 mode monitor
ifconfig wlan0 up  ✅
So all we need right now is a program that can capture these packets for us. The program that we're going to use is called Airodump-NG. It's part of the Aircrack-NG suit, and it's a packet-sniffer, so it's basically a program designed to capture packets while you're in monitor mode. So it will allow us to see all the wireless networks around us, and show us detailed information about its MAC address.

**To run airodump-ng:**
iwconfig
airodump-ng <int name>
To sniff on 5GH frequency(if the adapter supports it)
airodump-ng --band a wlan0
**To capture packet sent to and fro an access point:**
airodump-ng --bssid <value> --channel <value> --write <output file> wlan0  ✅


**To disconnect a device from a network:**
aireplay-ng --deauth 1000000 -a <access point mac> -c <victim Mac> wlan0  ❌
This is actually very, very handy in so many ways, it's very useful in social engineering cases, where you could disconnect clients from the target network and then call the user and pretend to be a person from the IT department and ask them to install a virus or a backdoor telling them that this would fix their issue. You could also create another fake access point and get them to connect to the fake access point and then start spying on them from that access point.

## Gaining Access

**To crack a WEP encryption:**
airodump-ng --bssid <value> --channel <value> --write basic_wep wlan0
aircrack-ng <filename.cap>


**To associate with an AP(Fake authentication attack):**
airodump-ng --bssid <value> --channel<value> --write arpreplay <int name>
aireplay-ng --fakeauth 0 -a <AP bssid> -h <adapter mac> wlan0

**To capture and retransmit ARP packets(ARP Request Replay Attack):**
While the airodump is still running, run the command to associate again
aireplay-ng --arpreplay -b <AP bssid> -h <adapter mac> wlan0
Once enough data packets have been generated, rerun the association command again and crack the .cap packet that have been generated
aircrack-ng arpreplay.cap
Hacking WPA/WPA2 without a wordlist(Exploiting WPS)

A feature that if enabled and misconfigured, can be exploited to recover the key without having to crack the actual encryption. The feature is called WPS.It allows devices to connect to the network easily without having to enter the key for the network. So it was designed to simplify the process of connecting printers and such devices. You can actually see a WPS button on most wireless enabled printers. If this button is pressed and then you press the WPS button on the router, you'll notice that the printer will connect to the router without you having to enter the key. This way, the authentication is done using an eight digit pin. So you can think of this as a password made up of only numbers and the length of this password is only eight. So this actually gives us a relatively small list of possible passwords and we can try all of these possible passwords within a relatively short time. Once we get this pin, it can be used to recover the actual WPA or WPA2 key. So for this to work, first of all, we need WPS to be enabled on the network because it can't be disabled. Also, it needs to be misconfigured. So it needs to be configured to use normal pin authentication and not push button authentication. If push button authentication is used, then the router will refuse any pins that we try unless the WPS button is pressed on the router. Therefore, the method will not work if push button or PBC is enabled.
So in most modern routers, PBC comes enabled by default or WPS will be disabled by default so this method might not work.
But because WPA and WPA2 are so secure and so challenging, it is always a good idea to check if WPS is enabled
View all networks with WPS enabled:
wash --interface wlan0 ✅

**Associate with the network:**
aireplay-ng --fakeauth 30 -a <AP bssid> -h<device mac> wlan0
Before executing the above command, run reaver which is the program that will brute force the pin for us and only then we will associate with the target because otherwise airereplay NG will fail to associate with the network. Reaver is the program that's going to brute force the pin. So it's gonna try every possible pin until it gets the right pin. Once it has the right pin, it'll use it to compute the actual WPA key.
reaver --bssid<AP bssid> --channel<AP channel> --interface wlan0 -vvv --no-associate



## Hacking WPA/WPA2 with a wordlist(Using Handshake)
if WPS is disabled on your target network, or if it's enabled, but configured to use push button or PBC, then the method  will not work. Therefore you will have to go and crack the actual WPA or WPA2 encryption using the four packet handshake.

First of all, as usual, you'd wanna run airodump-ng to see all the networks around you.
airodump-ng wlan0

Run airodump-ng on the target AP and store the data in a file
airodump-ng --bssid<target AP mac> --channel<target AP channel> --write wpa_handshake wlan0

Disconnect a client from the network in order to capture the handshake when the client tries to reconnect
aireplay-ng --deauth 4 -a <target Ap mac> -c <target client mac> wlan0

The handshake will be received once the client reconnects

Now the handshake does not contain any information that can help us to recover or recalculate the WPA key. The information in it can only be used to check whether a password is valid or not. Therefore, what we're going to do is to create a wordlist,
which is basically a big text file that contains a large number of passwords. Then go through this file, go through the passwords one by one, and use them with the handshake
in order to check whether this password is valid or not.
You can actually download ready wordlists from the internet or create one yourself
Creating a Wordlist

All you have to do is just put the name of the tool, and then you specify the minimum number of characters for the passwords to be generated. Then we're gonna specify the maximum number of characters for the password. Then you specify the characters
that you want to generate passwords from.
For example, you can put all lowercase characters, all uppercase, you can put numbers, digits, or you can just specify a smaller number to make the wordlist smaller.
You can also use the option T, which is an optional, to give a pattern.
So for example, let's say that you are looking at the person while they were typing their password, and you seen that the password would start with an A. So you can tell Crunch that the password will start with an A,
and then give me all possible combinations of passwords that start with an A. And after that, we use the -o option to specify the file name where the passwords are gonna be stored.

## Cracking WPA/WPA2 using a Word-list Attack

To do the cracking, Aircrack-ng is going to unpack the handshake and extract the useful information.
The MIC right here, or the message integrity code, is what's used by the access point
to verify whether a password is correct or not. So, it's going to separate this and put it to the side. And then it's going to use all
of the other information right here,
combined with the first password from the word list to generate an MIC, another message integrity code. And then, it's going to compare this MIC to the one that's already in the handshake. If the MIC generated using this information plus the first password is the same, then the password used to generate this MIC is the password for the network. Otherwise this password is wrong and it will move to the next password.
aircrack-ng wpa_handshake.cap -w <wordlist file.txt>


## Post-Connection Attack
Discovering Devices Connected to the Same Network With Me
Using Netdiscover:
netdiscover -r 192.168.0.1/24

Using Nmap(CLI)/Zenmap(GUI):

nmap -sn 192.168.0.1/24 [ping scan]

a lot of devices do not respond to ping requests even if they are alive.
So the list that you'll get in this scan
might not include all the devices connected to your network.
nmap -T4 -F 192.168.0.1/24 [quick scan]

nmap -sV -T4 -O -F --version-light 192.168.0.1/24 [quick scan plus]

By default iOS devices do not have an ssh server. Usually when you jailbreak the phone or the device it will automatically install an ssh server and the password for that server
is set to "alpine", by default.
That's A-L-P-I-N-E. Now since we know that this is an iPhone and it has port 22 open with an open ssh server, we know that this phone has been jailbroken. Now since the phone is jailbroken, we know the password to log into ssh is "alpine" unless the user changed it. Now most users do not even know about this, and even the ones that know about this, are too lazy to change it. So it's always worth a try if you discover
a phone like this in the same network.
It's always worth a try to go and try
to connect to it with the default password.

ARP Spoofing/ARP Poisoning
ARP spoofing allow us to redirect the flow of packets placing the attacker at the middle btw the access point and the victim(MITM)

Intercepting Network Traffic(using ARPSPOOF)

arpspoof -i eth0 -t 192.168.0.103 192.168.0.1

We need to enable port forwarding so that
An attacker machine would allow packets to flow through it just like a router.
echo 1 > proc/sys/net/ipv4/ip_forward
Intercepting Network Traffic(using Bettercap)

bettercap -iface wlan0
Interface is the one that is connected to the network that I want to run the attacks against.

net.show to see list devices connected to the same network after starting net.probe module using net.probe on
if we don't know how to use a module,all we have to do is type help followed by the module name: help arp.spoof
set arp.spoof.fullduplex true
set arp.spoof.targets 192.168.0.103
arp.spoof on
