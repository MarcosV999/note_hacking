Paso 1: Crear la red interna (internalbr0)
```
incus network create internalbr0 ipv4.address=10.10.10.1/24 ipv4.nat=false ipv6.address=none
```
