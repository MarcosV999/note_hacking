```bash
# local port forwarding (access an internal service from your machine)
ssh -L <local-port>:<service-ip>:<service-port> user@$target
# remote port forwarding (expose a local service from your machine to the target network)
ssh -R <remote-port>:<local-ip>:<local-port> user@$target
# dynamic port forwarding (create a SOCKS proxy to route traffic dynamically) 
ssh -D <local-socks-port> user@$target
```