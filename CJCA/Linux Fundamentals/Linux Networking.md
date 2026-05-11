# Network Configuration
As a penetration tester, one of the essential skills is configuring and managing network settings on Linux systems. Mastering this allows us to efficiently set up testing environments, manipulate network traffic, and identify or exploit vulnerabilities. A solid understanding of Linux network configuration gives us the ability to tailor our testing approach to suit specific needs, helping optimize both our testing procedures and results.
A deep understanding of network protocols, including TCP/IP (the core protocol suite for Internet communications), DNS (domain name resolution), DHCP (for dynamic IP address allocation), and FTP (file transfer), is critical.
#### Network Access Control

Another vital component of network configuration is network access control (`NAC`). As penetration testers, we need to be well-versed in how NAC can enhance network security and the various technologies available. Key NAC models include:

| **Type**                             | **Description**                                                                                                           |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------- |
| Discretionary Access Control (`DAC`) | This model allows the owner of the resource to set permissions for who can access it.                                     |
| Mandatory Access Control (`MAC`)     | Permissions are enforced by the operating system, not the owner of the resource, making it more secure but less flexible. |
| Role-Based Access Control (`RBAC`)   | Permissions are assigned based on roles within an organization, making it easier to manage user privileges.               |
## Configuring Network Interfaces

When working with Ubuntu, you can configure local network interfaces using the `ifconfig` or the `ip` command. These powerful commands allow us to view and configure our system's network interfaces.

When it comes to activating network interfaces, `ifconfig` and `ip` commands are two commonly used tools. These commands allow users to modify and activate settings for a specific interface, such as `eth0`. We can adjust the network settings to suit our needs by using the appropriate syntax and specifying the interface name.
#### Activate Network Interface
```
MarcosV999@htb[/htb]$ sudo ifconfig eth0 up     # OR
MarcosV999@htb[/htb]$ sudo ip link set eth0 up
```
One way to allocate an IP address to a network interface is by utilizing the `ifconfig` command. We must specify the interface's name and IP address as arguments to do this. This is a crucial step in setting up a network connection. The IP address serves as a unique identifier for the interface and enables the communication between devices on the network.

#### Assign IP Address to an Interface
```
MarcosV999@htb[/htb]$ sudo ifconfig eth0 192.168.1.2
```
To set the netmask for a network interface, we can run the following command with the name of the interface and the netmask:

#### Assign a Netmask to an Interface
```
MarcosV999@htb[/htb]$ sudo ifconfig eth0 netmask 255.255.255.0
```
When we want to set the default gateway for a network interface, we can use the `route` command with the `add` option. This allows us to specify the gateway's IP address and the network interface to which it should be applied. By setting the default gateway, we are designating the IP address of the router that will be used to send traffic to destinations outside the local network. Ensuring that the default gateway is set correctly is important, as incorrect configuration can lead to connectivity issues.
#### Assign the Route to an Interface
```
MarcosV999@htb[/htb]$ sudo route add default gw 192.168.1.1 eth0
```
When configuring a network interface in Linux, it is often necessary to set Domain Name System (`DNS`) servers to ensure proper network functionality. DNS servers are responsible for translating domain names (like example.com) into IP addresses, which allows devices to locate and connect to one another on the internet. Proper DNS configuration is crucial for enabling devices to access websites, online services, and other networked resources.
On Linux systems, this can be achieved by updating the `/etc/resolv.conf` file, which is a simple text file containing the system’s DNS information. By adding the appropriate DNS server addresses (Google's public DNS - `8.8.8.8` or `8.8.4.4`), the system can correctly resolve domain names to IP addresses, ensuring smooth communication over the network.

#### Editing DNS Settings
```
MarcosV999@htb[/htb]$ sudo vim /etc/resolv.conf
```
After completing the necessary modifications to the network configuration, it is essential to ensure that these changes are saved to persist across reboots. This can be achieved by editing the `/etc/network/interfaces` file, which defines network interfaces for Linux-based operating systems. Thus, it is vital to save any changes made to this file to avoid any potential issues with network connectivity.
It’s important to note that changes made directly to the `/etc/resolv.conf` file are not persistent across reboots or network configuration changes. This is because the file may be automatically overwritten by network management services like `NetworkManager` or `systemd-resolved`. To make DNS changes permanent, you should configure DNS settings through the appropriate network management tool, such as editing network configuration files or using network management utilities that store persistent settings.

#### Editing Interfaces
```
MarcosV999@htb[/htb]$ sudo vim /etc/network/interfaces
```
This will open the `interfaces` file in the vim editor. We can add the network configuration settings to the file like this:
```
auto eth0
iface eth0 inet static
  address 192.168.1.2
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 8.8.8.8 8.8.4.4
```
By setting the `eth0` network interface to use a static IP address of `192.168.1.2`, with a netmask of `255.255.255.0` and a default gateway of `192.168.1.1`, we can ensure that your network connection remains stable and reliable. Additionally, by specifying DNS servers of `8.8.8.8` and `8.8.4.4`, we can ensure that our computer can easily access the internet and resolve domain names. Once we have made these changes to the configuration file, saving the file and exiting the editor is important. After that, we must restart the networking service to apply the changes.
#### Restart Networking Service
```
MarcosV999@htb[/htb]$ sudo systemctl restart networking
```

By implementing NAC, organizations can be confident in their ability to protect their assets and data from cybercriminals who always seek to exploit system vulnerabilities. The following are the different NAC technologies that can be used to enhance security measures:
## Network Access Control
- Discretionary access control (DAC)
- Mandatory access control (MAC)
- Role-based access control (RBAC)

These technologies are designed to provide different levels of access control and security. Each technology has its unique characteristics and is suitable for different use cases. As a penetration tester, it is essential to understand these technologies and their specific use cases to test and evaluate the network's security effectively.

## Hardening
Several mechanisms are highly effective in securing Linux systems in keeping our and other companies' data safe. Three such mechanisms are SELinux, AppArmor, and TCP wrappers. These tools are designed to safeguard Linux systems against various security threats, from unauthorized access to malicious attacks.
#### Security-Enhanced Linux

Security-Enhanced Linux (`SELinux`) is a mandatory access control (`MAC`) system integrated into the Linux kernel. It provides fine-grained control over access to system resources and applications by enforcing security policies.
#### AppArmor

Like SELinux, `AppArmor` is a MAC system that controls access to system resources and applications, but it operates in a simpler, more user-friendly manner. AppArmor is implemented as a Linux Security Module (`LSM`) and uses application profiles to define what resources an application can access.

#### TCP Wrappers

`TCP wrappers` are a host-based network access control tool that restricts access to network services based on the IP address of incoming connections. When a network request is made, TCP wrappers intercept it, checking the request against a list of allowed or denied IP addresses. This is a simple yet effective way to control access to services, especially for blocking unauthorized systems from accessing networked resources.