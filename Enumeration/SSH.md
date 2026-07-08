## Footprinting the Service
One of the tools we can use to fingerprint the SSH server is [ssh-audit](https://github.com/jtesta/ssh-audit). It checks the client-side and server-side configuration and shows some general information and which encryption algorithms are still used by the client and server. Of course, this could be exploited by attacking the server or client at the cryptic level later.
#### SSH-Audit
```shell
git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
./ssh-audit.py 10.129.14.132
```
#### Change Authentication Method
For potential brute-force attacks, we can specify the authentication method with the SSH client option `PreferredAuthentications`.
```shell
ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password
```

## Rsync

[Rsync](https://linux.die.net/man/1/rsync) is a fast and efficient tool for locally and remotely copying files. It can be used to copy files locally on a given machine and to/from remote hosts. It is highly versatile and well-known for its delta-transfer algorithm. This algorithm reduces the amount of data transmitted over the network when a version of the file already exists on the destination host. It does this by sending only the differences between the source files and the older version of the files that reside on the destination server. It is often used for backups and mirroring. It finds files that need to be transferred by looking at files that have changed in size or the last modified time. By default, it uses port `873` and can be configured to use SSH for secure file transfers by piggybacking on top of an established SSH server connection.
#### Probing for Accessible Shares

We can next probe the service a bit to see what we can gain access to.
```shell
nc -nv 127.0.0.1 873
```
#### Enumerating an Open Share
```shell
rsync -av --list-only rsync://127.0.0.1/dev
```
## R-Services

R-Services are a suite of services hosted to enable remote access or issue commands between Unix hosts over TCP/IP.
