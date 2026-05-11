# User Management
|**Command**|**Description**|
|---|---|
|`sudo`|Execute command as a different user.|
|`su`|The `su` utility requests appropriate user credentials via PAM and switches to that user ID (the default user is the superuser). A shell is then executed.|
|`useradd`|Creates a new user or update default new user information.|
|`userdel`|Deletes a user account and related files.|
|`usermod`|Modifies a user account.|
|`addgroup`|Adds a group to the system.|
|`delgroup`|Removes a group from the system.|
|`passwd`|Changes user password.|
# Package Management
| **Command** | **Description**                                                                                                                                                                                                                                                                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `dpkg`      | The `dpkg` is a tool to install, build, remove, and manage Debian packages. The primary and more user-friendly front-end for `dpkg` is aptitude.                                                                                                                                                                                                        |
| `apt`       | Apt provides a high-level command-line interface for the package management system.                                                                                                                                                                                                                                                                     |
| `aptitude`  | Aptitude is an alternative to apt and is a high-level interface to the package manager.                                                                                                                                                                                                                                                                 |
| `snap`      | Install, configure, refresh, and remove snap packages. Snaps enable the secure distribution of the latest apps and utilities for the cloud, servers, desktops, and the internet of things.                                                                                                                                                              |
| `gem`       | Gem is the front-end to RubyGems, the standard package manager for Ruby.                                                                                                                                                                                                                                                                                |
| `pip`       | Pip is a Python package installer recommended for installing Python packages that are not available in the Debian archive. It can work with version control repositories (currently only Git, Mercurial, and Bazaar repositories), logs output extensively, and prevents partial installs by downloading all requirements before starting installation. |
| `git`       | Git is a fast, scalable, distributed revision control system with an unusually rich command set that provides both high-level operations and full access to internals.                                                                                                                                                                                  |
## DPKG

We can also download the programs and tools from the repositories separately. In this example, we download 'strace' for Ubuntu 18.04 LTS.
Furthermore, now we can use both `apt` and `dpkg` to install the package. Since we have already worked with `apt`, we will turn to `dpkg` in the next example.
```
MarcosV999@htb[/htb]$ sudo dpkg -i strace_4.21-1ubuntu1_amd64.deb 

(Reading database ... 154680 files and directories currently installed.)
Preparing to unpack strace_4.21-1ubuntu1_amd64.deb ...
Unpacking strace (4.21-1ubuntu1) over (4.21-1ubuntu1) ...
Setting up strace (4.21-1ubuntu1) ...
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...
```

# Service and Process Management
Services, also known as daemons, are fundamental components of a Linux system that run silently in the background "without direct user interaction".
## Systemctl
```
MarcosV999@htb[/htb]$ systemctl start ssh
```
After we have started the service, we can now check if it runs without errors.
```
MarcosV999@htb[/htb]$ systemctl status ssh
```
To add OpenSSH to the SysV script to tell the system to run this service after startup, we can link it with the following command:
```
MarcosV999@htb[/htb]$ systemctl enable ssh

Synchronizing state of ssh.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable ssh
```
Once we reboot the system, the OpenSSH server will automatically run. We can check this with a tool called `ps`.
```
MarcosV999@htb[/htb]$ ps -aux | grep ssh

root       846  0.0  0.1  72300  5660 ?        Ss   Mai14   0:00 /usr/sbin/sshd -D
```
We can also use `systemctl` to list all services.
```
MarcosV999@htb[/htb]$ systemctl list-units --type=service
```

## Kill a Process
A process can be in the following states:

- Running
- Waiting (waiting for an event or system resource)
- Stopped
- Zombie (stopped but still has an entry in the process table).

Processes can be controlled using `kill`, `pkill`, `pgrep`, and `killall`. To interact with a process, we must send a signal to it. We can view all signals with the following command:
```
MarcosV999@htb[/htb]$ kill -l

 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 2) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
3) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
4) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
5) SIGTTIN     22) SIGTTOU     ........
```

The most commonly used signals are:

| **Signal** | **Description**                                                                                                          |
| ---------- | ------------------------------------------------------------------------------------------------------------------------ |
| `1`        | `SIGHUP` - This is sent to a process when the terminal that controls it is closed.                                       |
| `2`        | `SIGINT` - Sent when a user presses `[Ctrl] + C` in the controlling terminal to interrupt a process.                     |
| `3`        | `SIGQUIT` - Sent when a user presses `[Ctrl] + D` to quit.                                                               |
| `9`        | `SIGKILL` - Immediately kill a process with no clean-up operations.                                                      |
| `15`       | `SIGTERM` - Program termination.                                                                                         |
| `19`       | `SIGSTOP` - Stop the program. It cannot be handled anymore.                                                              |
| `20`       | `SIGTSTP` - Sent when a user presses `[Ctrl] + Z` to request for a service to suspend. The user can handle it afterward. |
For example, if a program were to freeze, we could force to kill it with the following command:
```
MarcosV999@htb[/htb]$ kill 9 <PID>
```

## Background a Process
Sometimes it will be necessary to put the scan or process we just started in the background to continue using the current session to interact with the system or start other processes. As we have already seen, we can do this with the shortcut `[Ctrl + Z]`. As mentioned above, we send the `SIGTSTP` signal to the kernel, which suspends the process.
Now all background processes can be displayed with the following command.
```
MarcosV999@htb[/htb]$ jobs
```
The `[Ctrl] + Z` shortcut suspends the processes, and they will not be executed further. To keep it running in the background, we have to enter the command `bg` to put the process in the background.
```
MarcosV999@htb[/htb]$ bg

[1]+ ping -c 10 www.hackthebox.eu &
```

## Foreground a Process

After that, we can use the `jobs` command to list all background processes. Backgrounded processes do not require user interaction, and we can use the same shell session without waiting until the process finishes first. Once the scan or process finishes its work, we will get notified by the terminal that the process is finished.

If we want to get the background process into the foreground and interact with it again, we can use the `fg <ID>` command.
```
MarcosV999@htb[/htb]$ fg 1
```

## Execute Multiple Commands

There are three possibilities to run several commands, one after the other. These are separated by:

- Semicolon (`;`)
- Double `ampersand` characters (`&&`)
- Pipes (`|`)

The semicolon (`;`) is a command separator and executes the commands by ignoring previous commands' results and errors.
```
MarcosV999@htb[/htb]$ echo '1'; echo '2'; echo '3'

1
2
3
```
For example, if we execute the same command but replace it in second place, the command `ls` with a file that does not exist, we get an error, and the third command will be executed nevertheless.
```
MarcosV999@htb[/htb]$ echo '1'; ls MISSING_FILE; echo '3'

1
ls: cannot access 'MISSING_FILE': No such file or directory
3
```

However, it looks different if we use the double AND characters (`&&`) to run the commands one after the other. If there is an error in one of the commands, the following ones will not be executed anymore, and the whole process will be stopped.
```
MarcosV999@htb[/htb]$ echo '1' && ls MISSING_FILE && echo '3'

1
ls: cannot access 'MISSING_FILE': No such file or directory
```

Pipes (`|`) depend not only on the correct and error-free operation of the previous processes but also on the previous processes' results.

# Task Scheduling
## Systemd

Systemd is a service used in Linux systems to start processes and scripts at a specific time. With it, we can set up processes and scripts to run at a specific time or time interval and can also specify specific events and triggers that will trigger a specific task. To do this, we need to take some steps and precautions before our scripts or processes are automatically executed by the system.

1. Create a timer (schedules when your `mytimer.service` should run)
2. Create a service (executes the commands or script)
3. Activate the timer

#### Create a Timer

To create a timer for systemd, we need to create a directory where the timer script will be stored.
```
MarcosV999@htb[/htb]$ sudo mkdir /etc/systemd/system/mytimer.timer.d
MarcosV999@htb[/htb]$ sudo vim /etc/systemd/system/mytimer.timer
```
Next, we need to create a script that configures the timer. The script must contain the following options: "Unit", "Timer" and "Install". The "Unit" option specifies a description for the timer. The "Timer" option specifies when to start the timer and when to activate it. Finally, the "Install" option specifies where to install the timer.
#### Mytimer.timer
```
[Unit]
Description=My Timer

[Timer]
OnBootSec=3min
OnUnitActiveSec=1hour

[Install]
WantedBy=timers.target
```
#### Create a Service
```
MarcosV999@htb[/htb]$ sudo vim /etc/systemd/system/mytimer.service
```
Here we set a description and specify the full path to the script we want to run. The "multi-user.target" is the unit system that is activated when starting a normal multi-user mode. It defines the services that should be started on a normal system startup.
```
[Unit]
Description=My Service

[Service]
ExecStart=/full/path/to/my/script.sh

[Install]
WantedBy=multi-user.target
```
After that, we have to let `systemd` read the folders again to include the changes.
#### Reload Systemd
```
MarcosV999@htb[/htb]$ sudo systemctl daemon-reload
```
After that, we can use `systemctl` to `start` the service manually and `enable` the autostart
#### Start the Timer & Service
```
MarcosV999@htb[/htb]$ sudo systemctl start mytimer.timer
MarcosV999@htb[/htb]$ sudo systemctl enable mytimer.timer
```
This way, `mytimer.service` will be launched automatically according to the intervals (or delays) you set in `mytimer.timer`.
## Cron

Cron is another tool that can be used in Linux systems to schedule and automate processes. It allows users and administrators to execute tasks at a specific time or within specific intervals.
With Cron, we can automate the same tasks, but the process for setting up the Cron daemon is a little different than Systemd. To set up the cron daemon, we need to store the tasks in a file called `crontab` and then tell the daemon when to run the tasks. Then we can schedule and automate the tasks by configuring the cron daemon accordingly. The structure of Cron consists of the following components: 

| **Time Frame**         | **Description**                                                       |
| ---------------------- | --------------------------------------------------------------------- |
| Minutes (0-59)         | This specifies in which minute the task should be executed.           |
| Hours (0-23)           | This specifies in which hour the task should be executed.             |
| Days of month (1-31)   | This specifies on which day of the month the task should be executed. |
| Months (1-12)          | This specifies in which month the task should be executed.            |
| Days of the week (0-7) | This specifies on which day of the week the task should be executed.  |

For example, such a crontab could look like this:
```
# System Update
0 */6 * * * /path/to/update_software.sh

# Execute Scripts
0 0 1 * * /path/to/scripts/run_scripts.sh

# Cleanup DB
0 0 * * 0 /path/to/scripts/clean_database.sh

# Backups
0 0 * * 7 /path/to/scripts/backup.sh
```

The first task, `System Update`, should be executed once every sixth hour. This is indicated by the entry `0 */6` in the hour column. The task is executed by the script `update_software.sh`, whose path is given in the last column.
The second task, `Execute Scripts`, is to be executed every first day of the month at midnight. This is indicated by the entries `0` and `0` in the minute and hour columns and `1` in the days-of-the-month column. The task is executed by the `run_scripts.sh` script, whose path is given in the last column.
The third task, `Cleanup DB`, is to be executed every Sunday at midnight. This is specified by the entries `0` and `0` in the minute and hour columns and `0` in the days-of-the-week column. The task is executed by the `clean_database.sh` script, whose path is given in the last column.
The fourth task, `Backups`, is to be executed every Sunday at midnight. This is indicated by the entries `0` and `0` in the minute and hour columns and `7` in the days-of-the-week column. The task is executed by the `backup.sh` script, whose path is given in the last column.
## Systemd vs. Cron

Systemd and Cron are both tools that can be used in Linux systems to schedule and automate processes. The key difference between these two tools is how they are configured. With Systemd, you need to create a timer and services script that tells the operating system when to run the tasks. On the other hand, with Cron, you need to create a `crontab` file that tells the cron daemon when to run the tasks.

# Network Services

## NFS
Network File System (`NFS`) is a network protocol that allows us to store and manage files on remote systems as if they were stored on the local system.

## Web Server
A web server is software that delivers data, documents, applications, and various functions over the Internet. It utilizes the Hypertext Transfer Protocol (`HTTP`) to transmit data to clients such as web browsers and to receive requests from these clients. The received data is then rendered as Hypertext Markup Language (`HTML`) within the client's browser, facilitating the creation of dynamic web pages that respond interactively to user requests. Consequently, a thorough comprehension of web server functionalities is vital for developing secure and efficient web applications and for maintaining overall system security.
#### Install Python & Web Server
```
MarcosV999@htb[/htb]$ sudo apt install python3 -y
MarcosV999@htb[/htb]$ python3 -m http.server
```
When we run this command, our Python Web Server will be started on the `TCP/8000` port, and we can access the folder we are currently in. We can also host another folder with the following command:
```
MarcosV999@htb[/htb]$ python3 -m http.server --directory /home/cry0l1t3/target_files
```
We can also host our Python web server on a port other than the default port:
```
MarcosV999@htb[/htb]$ python3 -m http.server 443
```
This will host our Python web server on port 443 instead of the default `TCP/8000` port. We can access this web server by typing the link in our browser.
## VPN

A Virtual Private Network (`VPN`) functions like a secure, invisible tunnel that connects us to another network, allowing seamless and protected access as if we were physically present within it. For penetration testers, OpenVPN offers invaluable capabilities. It allows testers to securely connect to internal networks, especially when direct access is not feasible due to geographical constraints. By utilizing OpenVPN, penetration testers can perform comprehensive security assessments of internal systems, identifying and addressing potential vulnerabilities.

# Working with Web Services

## CURL

`cURL` is a tool that allows us to transfer files from the shell over protocols like `HTTP`, `HTTPS`, `FTP`, `SFTP`, `FTPS`, or `SCP`, and in general, gives us the possibility to control and test websites remotely via command line.

## Wget

An alternative to curl is the tool `wget`. With this tool, we can download files from FTP or HTTP servers directly from the terminal, and it serves as a solid download manager.

### the command that starts the web server on the localhost (127.0.0.1) on port 8080.
php -S 127.0.0.1:8080
python3 -m http.server 8080

# File System Management
Linux is a versatile operating system that supports many different file systems, including ext2, ext3, ext4, XFS, Btrfs, and NTFS, among others. Each of these file systems has unique features and is suited to specific use cases.The best file system choice depends on the specific requirements of the application or user such as:

- `ext2` is an older file system with no journaling capabilities, which makes it less suited for modern systems but still useful in certain low-overhead scenarios (like USB drives).
- `ext3` and `ext4` are more advanced, with journaling (which helps in recovering from crashes), and ext4 is the default choice for most modern Linux systems because it offers a balance of performance, reliability, and large file support.
- `Btrfs` is known for advanced features like snapshotting and built-in data integrity checks, making it ideal for complex storage setups.
- `XFS` excels at handling large files and has high performance. It is best suited for environments with high I/O demands
- `NTFS`, originally developed for Windows, is useful for compatibility when dealing with dual-boot systems or external drives that need to work on both Linux and Windows systems.
# Containerization
Containerization is the process of packaging and running applications in isolated environments, typically referred to as containers. These containers provide lightweight, consistent environments for applications to run, ensuring that they behave the same way, regardless of where they are deployed. Technologies like Docker, Docker Compose, and Linux Containers (LXC) make containerization possible, primarily in Linux-based systems.

## Dockers
Docker is an open-source platform for automating the deployment of applications as self-contained units called containers.
Imagine Docker containers as a sealed lunchbox. You can eat the food (run applications) inside, but once you close the box (stop the container), everything resets. To make a new lunchbox (new container) with updated contents (modified configurations), you create a new recipe (Dockerfile) based on the original. When serving multiple lunchboxes in a restaurant (production), you'd use a kitchen system (Kubernetes/Docker Compose) to manage all the orders smoothly.
#### Install Docker-Engine

Installing Docker is relatively straightforward. We can use the following script to install it on a Ubuntu host:
```
#!/bin/bash

# Preparation
sudo apt update -y
sudo apt install ca-certificates curl gnupg lsb-release -y
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt update -y
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# Add user htb-student to the Docker group
sudo usermod -aG docker htb-student
echo '[!] You need to log out and log back in for the group changes to take effect.'

# Test Docker installation
docker run hello-world
```

Creating a Docker image is done by creating a [Dockerfile](https://docs.docker.com/engine/reference/builder/), which contains all the instructions the Docker engine needs to create the container. We can use Docker containers as our “file hosting” server when transferring specific files to our target systems. Therefore, we must create a `Dockerfile` based on Ubuntu 22.04 with `Apache` and `SSH` server running. With this, we can use `scp` to transfer files to the docker image, and Apache allows us to host files and use tools like `curl`, `wget`, and others on the target system to download the required files. Such a `Dockerfile` could look like the following:
```
# Use the latest Ubuntu 22.04 LTS as the base image
FROM ubuntu:22.04

# Update the package repository and install the required packages
RUN apt-get update && \
    apt-get install -y \
        apache2 \
        openssh-server \
        && \
    rm -rf /var/lib/apt/lists/*

# Create a new user called "docker-user"
RUN useradd -m docker-user && \
    echo "docker-user:password" | chpasswd

# Give the docker-user user full access to the Apache and SSH services
RUN chown -R docker-user:docker-user /var/www/html && \
    chown -R docker-user:docker-user /var/run/apache2 && \
    chown -R docker-user:docker-user /var/log/apache2 && \
    chown -R docker-user:docker-user /var/lock/apache2 && \
    usermod -aG sudo docker-user && \
    echo "docker-user ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Expose the required ports
EXPOSE 22 80

# Start the SSH and Apache services
CMD service ssh start && /usr/sbin/apache2ctl -D FOREGROUND
```
After we have defined our Dockerfile, we need to convert it into an image. With the `build` command, we take the directory with the Dockerfile, execute the steps from the `Dockerfile`, and store the image in our local Docker Engine. If one of the steps fails due to an error, the container creation will be aborted. With the option `-t`, we give our container a tag, so it is easier to identify and work with later.
```
MarcosV999@htb[/htb]$ docker build -t FS_docker .
```
```
MarcosV999@htb[/htb]$ docker run -p <host port>:<docker port> -d <docker container name>
```
```
MarcosV999@htb[/htb]$ docker run -p 8022:22 -p 8080:80 -d FS_docker
```
#### Docker Management

|**Command**|**Description**|
|---|---|
|`docker ps`|List all running containers|
|`docker stop`|Stop a running container.|
|`docker start`|Start a stopped container.|
|`docker restart`|Restart a running container.|
|`docker rm`|Remove a container.|
|`docker rmi`|Remove a Docker image.|
|`docker logs`|View the logs of a container.|
When working with Docker images, it's crucial to understand that any changes made to a running container based on an image are not automatically saved to the image. To preserve these changes, you need to create a new image that includes them. This is done by writing a new Dockerfile, which starts with the `FROM` statement (specifying the base image) and then includes the necessary commands to apply the changes. Once the Dockerfile is ready, you can use the `docker build` command to build the new image and assign it a unique tag to identify it. This process ensures that the original image remains unchanged, while the new image reflects the updates.
It's also important to remember that Docker containers are stateless by design, meaning that any changes made inside a running container (e.g., modifying files) are lost once the container is stopped or removed.

## Linux Containers

Linux Containers (`LXC`) is a lightweight virtualization technology that allows multiple isolated Linux systems (called containers) to run on a single host. LXC uses key resource isolation features, such as control groups (`cgroups`) and `namespaces`, to ensure that each container operates independently. Unlike traditional virtual machines, which require a full OS for each instance, containers share the host's kernel, making LXC more efficient in terms of resource usage.
LXC provides a comprehensive set of tools and APIs for managing and configuring containers, making it a popular choice for containerization on Linux systems. However, while LXC and Docker are both containerization technologies, they serve different purposes and have unique features.
There are some differences between the two that can be distinguished based on the following categories:

| **Category**     | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Approach`       | LXC is often seen as a more traditional, system-level containerization tool, focusing on creating isolated Linux environments that behave like lightweight virtual machines. Docker, on the other hand, is application-focused, meaning it is optimized for packaging and deploying single applications or microservices.                                                                                                                                                                                                                                         |
| `Image building` | Docker uses a standardized image format (Docker images) that includes everything needed to run an application (code, libraries, configurations). LXC, while capable of similar functionality, typically requires more manual setup for building and managing environments.                                                                                                                                                                                                                                                                                        |
| `Portability`    | Docker excels in portability. Its container images can be easily shared across different systems via Docker Hub or other registries. LXC environments are less portable in this sense, as they are more tightly integrated with the host system’s configuration.                                                                                                                                                                                                                                                                                                  |
| `Easy of use`    | Docker is designed with simplicity in mind, offering a user-friendly CLI and extensive community support. LXC, while powerful, may require more in-depth knowledge of Linux system administration, making it less straightforward for beginners.                                                                                                                                                                                                                                                                                                                  |
| `Security`       | Docker containers are generally more secure out of the box, thanks to additional isolation layers like AppArmor and SELinux, along with its read-only filesystem feature. LXC containers, while secure, may need additional configurations to match the level of isolation Docker offers by default. Interestingly enough, when misconfigured, both Docker and LXC can present a vector for local privilege escalation (these techniques are covered in depth in our [Linux Local Privilege Escalation module](https://academy.hackthebox.com/module/details/51). |

To install LXC on a Linux distribution, we can use the distribution's package manager. For example, on Ubuntu, we can use the `apt` package manager to install LXC with the following command:
```
MarcosV999@htb[/htb]$ sudo apt install lxc -y
```
#### Creating an LXC Container
```
MarcosV999@htb[/htb]$ sudo lxc-create -n linuxcontainer -t ubuntu
```

#### Managing LXC Containers
|Command|Description|
|---|---|
|`lxc-ls`|List all existing containers|
|`lxc-stop -n <container>`|Stop a running container.|
|`lxc-start -n <container>`|Start a stopped container.|
|`lxc-restart -n <container>`|Restart a running container.|
|`lxc-config -n <container name> -s storage`|Manage container storage|
|`lxc-config -n <container name> -s network`|Manage container network settings|
|`lxc-config -n <container name> -s security`|Manage container security settings|
|`lxc-attach -n <container>`|Connect to a container.|
|`lxc-attach -n <container> -f /path/to/share`|Connect to a container and share a specific directory or file.|

#### Securing LXC
Let us limit the resources to the container. In order to configure `cgroups` for LXC and limit the CPU and memory, a container can create a new configuration file in the `/usr/share/lxc/config/<container name>.conf` directory with the name of our container. For example, to create a configuration file for a container named `linuxcontainer`, we can use the following command:
```
MarcosV999@htb[/htb]$ sudo vim /usr/share/lxc/config/linuxcontainer.conf
```
In this configuration file, we can add the following lines to limit the CPU and memory the container can use.
```
lxc.cgroup.cpu.shares = 512
lxc.cgroup.memory.limit_in_bytes = 512M
```
To apply these changes, we must restart the LXC service.
```
MarcosV999@htb[/htb]$ sudo systemctl restart lxc.service
```
