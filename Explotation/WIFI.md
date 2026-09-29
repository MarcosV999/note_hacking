```bash
# Scan WiFi Networks by SSID
contractor@airside-ws01:~$  sudo iw dev wlan2 scan| grep -i "SSID"
	SSID: HTB International WiFi
		 * Multiple BSSID
		 * SSID List
contractor@airside-ws01:~$ sudo iw dev wlan3 scan | grep -i "SSID"
	SSID: HTB International WiFi
		 * Multiple BSSID
		 * SSID List
```

```bash
# Connect to a WiFi Network using NetworkManager
contractor@airside-ws01:~$ sudo nmcli dev wifi connect "HTB International WiFi"
```

```bash
# set wlan3 as monitor mode
sudo ip link set wlan3 down
sudo iw dev wlan3 set type monitor
sudo ip link set wlan3 up

# check thw change
iw dev

Interface wlan3
		ifindex 7
		wdev 0x300000001
		addr 02:00:00:00:03:00
		type monitor
Interface wlan2
		ifindex 6
		wdev 0x200000001
		addr 02:00:00:00:02:00
		type managed
```

`wlan2` in Managed Mode (The Connection Interface)
`wlan3` in Monitor Mode (The Passive Sniffing Interface): To capture packets traveling through the air (such as plaintext HTTP POST requests from other users), your card cannot be limited only to packets addressed to its own MAC address. Monitor mode allows you to intercept traffic across the entire environment without being detected.
```bash
#First, find out which channel the network you connected to with wlan2 is operating on (you can see this by running iw dev or looking at the connection details in nmcli).
sudo iw dev wlan3 set channel <número_de_canal>
```