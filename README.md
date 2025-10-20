# 🧩 Laboratorio DHCPv4 con VLANs y Configuración Básica

## 🧠 Objetivo
Implementar una red funcional en la que se apliquen conceptos fundamentales de **direccionamiento IP, VLANs y DHCPv4**, configurando los parámetros básicos de los dispositivos y verificando la asignación automática de direcciones IP a los hosts.

---

## 🛠️ Actividades realizadas

### 1. Cálculo de subredes
Se tomó una red base y se la **subdividió en subredes** más pequeñas para asignarlas a diferentes VLANs.  
- Determinación de la máscara adecuada según la cantidad de hosts por segmento.  
- Asignación de rangos de IP y direcciones de gateway para cada VLAN.  
- Documentación del esquema de direccionamiento.  
#### 🔹 Tabla de asignación de direcciones

| Dispositivo | Interfaz     | Dirección IP     | Máscara de subred   | Gateway predeterminado |
|--------------|---------------|------------------|----------------------|-------------------------|
| **R1** | G0/0/0 | 10.0.0.1 | 255.255.255.252 | N/D |
| | G0/0/1 | *No corresponde* | *No corresponde* |  |
| | G0/0/1.100 | 192.168.1.1 | 255.255.255.192 |  |
| | G0/0/1.200 | 192.168.1.65 | 255.255.255.224 |  |
| | G0/0/1.1000 | *No corresponde* | *No corresponde* |  |
| **R2** | G0/0 | 10.0.0.2 | 255.255.255.252 | N/D |
| | G0/1 | 192.168.1.97 | 255.255.255.240 |  |
| **S1** | VLAN 200 | 192.168.1.66 | 255.255.255.224 | 192.168.1.65 |
| **S2** | VLAN 1 | 192.168.1.98 | 255.255.255.240 | 192.168.1.97 |
| **PC-A** | NIC | DHCP | DHCP | DHCP |
| **PC-B** | NIC | DHCP | DHCP | DHCP |

---

### 2. Configuración básica de los dispositivos
hostname R1
no ip domain-lookup
service password-encryption
enable secret class
banner motd # Acceso a personal autorizado #
line console 0
 password cisco
 login
 line vty 0 4
 password cisco
 login

 ---

### 3. Configuración en losswitches
vlan 100
 name Clientes
vlan 200
 name Administracion
vlan 999
 name Parking_Lot
vlan 1000
 name Nativa
exit

interface FastEthernet0/6
 switchport mode access
 switchport access vlan 100

interface vlan 200
 ip address 192.168.1.66 255.255.255.224
 no shutdown

interface FastEthernet0/24
 switchport mode trunk
 switchport trunk native vlan 1000

 ### 4. Configuración del servidor DHCP (R1)
 ip dhcp excluded-address 192.168.1.1 192.168.1.10
ip dhcp excluded-address 192.168.1.65 192.168.1.70

ip dhcp pool CLIENTES
 network 192.168.1.0 255.255.255.192
 default-router 192.168.1.1
 dns-server 8.8.8.8
 domain-name laboratorio.local

ip dhcp pool ADMINISTRACION
 network 192.168.1.64 255.255.255.224
 default-router 192.168.1.65
 dns-server 8.8.8.8
 domain-name laboratorio.local

 ### 5.Configuración de retransmisión DHCP (R2)
 interface GigabitEthernet0/1
 ip address 192.168.1.97 255.255.255.240
 ip helper-address 10.0.0.1
 no shutdown

 ### 6.Configuración de retransmisión DHCP (R2)
 interface GigabitEthernet0/1
 ip address 192.168.1.97 255.255.255.240
 ip helper-address 10.0.0.1
 no shutdown

### 7. Verificacion
show ip dhcp binding
show ip dhcp pool
show run | section dhcp
show vlan brief
show ip interface brief

En los hosts:
ipconfig /renew (Windows)
 dhclient (Linux)
