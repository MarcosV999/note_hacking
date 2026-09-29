```bash
contractor@airside-ws01:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
6: wlan2: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 02:00:00:00:02:00 brd ff:ff:ff:ff:ff:ff
7: wlan3: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 02:00:00:00:03:00 brd ff:ff:ff:ff:ff:ff
10: eth0@if11: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 00:16:3e:83:ea:41 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.159.143.45/24 metric 100 brd 10.159.143.255 scope global dynamic eth0
       valid_lft 3586sec preferred_lft 3586sec
    inet6 fd42:3ff5:6554:20e5:216:3eff:fe83:ea41/64 scope global mngtmpaddr noprefixroute 
       valid_lft forever preferred_lft forever
    inet6 fe80::216:3eff:fe83:ea41/64 scope link 
       valid_lft forever preferred_lft forever
```

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
- **`iw`**: The native Linux tool used to configure and diagnose wireless interfaces.
- **`dev`** _(short for device)_: to perform operations on physical or virtual network devices.
- **`wlan2`**: Specifies to `iw` which exact network interface to work on (`wlan2`).
- **`scan`**: Performs a radio frequency scan looking for nearby Access Points (APs).
- **`grep -i "SSID"`**: Filters the output to display only lines containing the word "SSID" (the network name), ignoring case sensitivity (`-i`).

```bash
# Connect to a WiFi Network using NetworkManager
contractor@airside-ws01:~$ sudo nmcli dev wifi connect "HTB International WiFi"
Device 'wlan2' successfully activated with '05a32170-e489-4ecb-b033-dffa49f594c4'.
```
- **`nmcli`**: The command-line interface tool for **NetworkManager**, used to control and report network status in Linux systems.
- **`dev`** _(short for device)_: Directs `nmcli` to perform operations on physical or virtual network devices.
- **`wifi`**: Specifies that the device action applies specifically to wireless network interfaces.
- **`connect`**: Initiates a connection request to a specified Access Point (AP).
- **`"HTB International WiFi"`**: The target network name (SSID). Quotes are used to ensure spaces in the network name are handled correctly by the shell.

```
contractor@airside-ws01:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
6: wlan2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 02:00:00:00:02:00 brd ff:ff:ff:ff:ff:ff
    inet 10.13.37.182/24 brd 10.13.37.255 scope global dynamic noprefixroute wlan2
       valid_lft 42152sec preferred_lft 42152sec
    inet6 fe80::d1b:cf33:71df:e33d/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
7: wlan3: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 02:00:00:00:03:00 brd ff:ff:ff:ff:ff:ff
10: eth0@if11: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 00:16:3e:83:ea:41 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.159.143.45/24 metric 100 brd 10.159.143.255 scope global dynamic eth0
       valid_lft 2336sec preferred_lft 2336sec
    inet6 fd42:3ff5:6554:20e5:216:3eff:fe83:ea41/64 scope global mngtmpaddr noprefixroute 
       valid_lft forever preferred_lft forever
    inet6 fe80::216:3eff:fe83:ea41/64 scope link 
       valid_lft forever preferred_lft forever
```