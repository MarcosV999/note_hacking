`Network File System` (`NFS`) is a network file system developed by Sun Microsystems and has the same purpose as SMB. Its purpose is to access file systems over a network as if they were local.
[NFS](https://en.wikipedia.org/wiki/Network_File_System) is used between Linux and Unix systems.
NFS is an Internet standard that governs the procedures in a distributed file system. While NFS protocol version 3.0 (`NFSv3`), which has been in use for many years, authenticates the client computer, this changes with `NFSv4`. Here, as with the Windows SMB protocol, the user must authenticate.
nlockmgr
## Footprinting the Service

When footprinting NFS, the TCP ports `111` and `2049` are essential. We can also get information about the NFS service and the host via RPC, as shown below in the example.
```shell
sudo nmap 10.129.14.128 -p111,2049 -sV -sC
sudo nmap --script nfs* 10.129.14.128 -sV -p111,2049
```
#### Show Available NFS Shares
```shell
MarcosV999@htb[/htb]$ showmount -e 10.129.14.128
```
#### Mounting NFS Share
```shell
MarcosV999@htb[/htb]$ mkdir target-NFS
MarcosV999@htb[/htb]$ sudo mount -t nfs $target:/ ./target-NFS/ -o nolock
MarcosV999@htb[/htb]$ cd target-NFS
MarcosV999@htb[/htb]$ tree .
```
#### Umount NFS Share
```shell
umount /tmp/nfs_share
```

```
Administratoradmindefaulten-US
Administratoradmindefaulten-USb22924d5-57de-468e-9df4-0961cf6aa30d
Administratoradminb8be16afba8c314ad33d812f22a04991b90e2aaa{"hashAlgorithm":"SHA1"}en-USf8512f97-cab1-4a4b-a49f-0a2054c47a1d
adminadmin@htb.localb8be16afba8c314ad33d812f22a04991b90e2aaa{"hashAlgorithm":"SHA1"}admin@htb.localen-USfeb1a998-d3bf-406a-b30b-e269d7abdf50
adminadmin@htb.localb8be16afba8c314ad33d812f22a04991b90e2aaa{"hashAlgorithm":"SHA1"}admin@htb.localen-US82756c26-4321-4d27-b429-1b5c7c4f882f
b8be16afba8c314ad33d812f22a04991b90e2aaa:baconandcheese / admin
```