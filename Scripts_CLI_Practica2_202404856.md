# Scripts CLI — Práctica 2 | Red de la Ciudad Comercial Cayalá

**Curso:** Redes de Computadoras 1  
**Estudiante:** Ángel Emanuel Rodriguez Corado  
**Registro:** 202404856  

> **Naturaleza del documento:** recopilación compacta y ordenada de la configuración trabajada y descrita durante la práctica. Es una **reconstrucción funcional de los comandos necesarios**, no un volcado literal de `show running-config` de los 15 equipos. Puertos, direcciones, prioridades, VLANs y modos VTP se basan en la topología y verificaciones compartidas. Antes de entregar los scripts como «código fuente completo» oficial, conviene contrastarlos con `show running-config` de cada switch. **No pegar estos bloques sobre la red terminada**: cambiar VTP, LACP o STP puede interrumpir una red que ya funciona.

## 1. Parámetros comunes

| Parámetro | Configuración |
|---|---|
| Dominio VTP | `202404856` |
| Versión VTP | `2` |
| Contraseña VTP | `Cayala2026` |
| VLANs de establecimientos | `16 CEMACO`, `26 BANCO`, `36 CINEPOLIS`, `46 ADIDAS`, `56 VINOTECA` |
| VLAN nativa | `99 NATIVA` |
| VLAN de puertos sin uso | `999 BLACKHOLE` |
| Spanning Tree | `rapid-pvst` |
| EtherChannel | `LACP`, `Port-channel1` entre CEMACO y Adidas |
| Trunk backbone | VLANs permitidas `16,26,36,46,56,99` |

**Orden recomendado en una instalación desde cero:** crear y propagar las VLANs por VTP antes de configurar los puertos Access; habilitar troncales; crear EtherChannel en los dos extremos; configurar STP; habilitar SVI y routing en SW-CEMACO. Los comandos de cada bloque están organizados como referencia final, no necesariamente en el orden histórico exacto en que se teclearon.

### 1.1 Creación de VLANs (ejecutada una vez en un VTP Server)

```cisco
enable
configure terminal
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode server
vlan 16
 name CEMACO
vlan 26
 name BANCO
vlan 36
 name CINEPOLIS
vlan 46
 name ADIDAS
vlan 56
 name VINOTECA
vlan 99
 name NATIVA
vlan 999
 name BLACKHOLE
end
```

> Las siete VLANs de usuario/nativa/blackhole, más las predeterminadas `1` y `1002–1005`, explican las **12 VLANs** mostradas por `show vtp status`. La creación de VLANs se realiza una vez y se distribuye por VTP; no repetir cambios de base de datos en cada servidor sin una razón.

## 2. Switches principales (Cisco 3560)

### 2.1 SW-CEMACO

**Enlaces:** Fa0/1–3 a CEM-ACC1–3; Fa0/4 a SW-BANCO; Fa0/5 a SW-VINOTECA; Fa0/6–7 a SW-ADIDAS por Po1; Fa0/8 a CEM-SERVER.

```cisco
enable
configure terminal
hostname SW-CEMACO
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode server
spanning-tree mode rapid-pvst
spanning-tree vlan 16 priority 4096
! Uplinks hacia switches de acceso
interface range fastEthernet 0/1 - 3
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,99
 switchport mode trunk
 no shutdown
 exit
! Trunks del anillo de distribución
interface fastEthernet 0/4
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
interface fastEthernet 0/5
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
! EtherChannel LACP (dos enlaces entre CEMACO y Adidas)
! Se configura el Port-channel lógico antes de agregar sus miembros.
interface port-channel 1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/6 - 7
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 channel-group 1 mode active
 no shutdown
 exit
! Si Packet Tracer indica un conflicto de VLAN mask al crear el canal,
! igualar paso a paso primero las configuraciones físicas y la lógica.
! Servidor local
interface fastEthernet 0/8
 description SERVIDOR_CEMACO
 switchport mode access
 switchport access vlan 16
 spanning-tree portfast
 no shutdown
 exit
! Puertos no utilizados
interface range fastEthernet 0/9 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
! Enrutamiento inter-VLAN centralizado
ip routing
interface vlan 16
 ip address 192.168.16.1 255.255.255.192
 no shutdown
 exit
interface vlan 26
 ip address 192.168.26.1 255.255.255.224
 no shutdown
 exit
interface vlan 36
 ip address 192.168.36.1 255.255.255.240
 no shutdown
 exit
interface vlan 46
 ip address 192.168.46.1 255.255.255.192
 no shutdown
 exit
interface vlan 56
 ip address 192.168.56.1 255.255.255.240
 no shutdown
 exit
end
copy running-config startup-config
```

**Verificación principal:**
```cisco
show vtp status
show vlan brief
show spanning-tree vlan 16
show interfaces trunk
show etherchannel summary
show ip route
show ip interface brief
```

### 2.2 SW-BANCO

**Enlaces:** Fa0/1–2 a BAN-ACC1–2; Fa0/3 a SW-CEMACO; Fa0/4 a SW-CINEPOLIS; Fa0/5 a BAN-SERVER.

```cisco
enable
configure terminal
hostname SW-BANCO
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode server
spanning-tree mode rapid-pvst
spanning-tree vlan 26 priority 8192
! Uplinks hacia switches de acceso
interface range fastEthernet 0/1 - 2
 switchport trunk native vlan 99
 switchport trunk allowed vlan 26,99
 switchport mode trunk
 no shutdown
 exit
! Trunks del anillo de distribución
interface fastEthernet 0/3
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
interface fastEthernet 0/4
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
! Servidor local
interface fastEthernet 0/5
 description SERVIDOR_BANCO
 switchport mode access
 switchport access vlan 26
 spanning-tree portfast
 no shutdown
 exit
! Puertos no utilizados
interface range fastEthernet 0/6 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

**Verificación principal:**
```cisco
show vtp status
show vlan brief
show spanning-tree vlan 26
show interfaces trunk
```

### 2.3 SW-CINEPOLIS

**Enlaces:** Fa0/1 a CIN-ACC1; Fa0/2 a SW-BANCO; Fa0/3 a SW-ADIDAS; Fa0/4 a CIN-SERVER.

```cisco
enable
configure terminal
hostname SW-CINEPOLIS
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode server
spanning-tree mode rapid-pvst
spanning-tree vlan 36 priority 12288
! Uplinks hacia switches de acceso
interface range fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 36,99
 switchport mode trunk
 no shutdown
 exit
! Trunks del anillo de distribución
interface fastEthernet 0/2
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
interface fastEthernet 0/3
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
! Servidor local
interface fastEthernet 0/4
 description SERVIDOR_CINEPOLIS
 switchport mode access
 switchport access vlan 36
 spanning-tree portfast
 no shutdown
 exit
! Puertos no utilizados
interface range fastEthernet 0/5 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

**Verificación principal:**
```cisco
show vtp status
show vlan brief
show spanning-tree vlan 36
show interfaces trunk
```

### 2.4 SW-ADIDAS

**Enlaces:** Fa0/1–3 a ADI-ACC1–3; Fa0/4 a SW-CINEPOLIS; Fa0/5 a SW-VINOTECA; Fa0/6–7 a SW-CEMACO por Po1; Fa0/8 a ADI-SERVER.

```cisco
enable
configure terminal
hostname SW-ADIDAS
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode server
spanning-tree mode rapid-pvst
spanning-tree vlan 46 priority 16384
! Uplinks hacia switches de acceso
interface range fastEthernet 0/1 - 3
 switchport trunk native vlan 99
 switchport trunk allowed vlan 46,99
 switchport mode trunk
 no shutdown
 exit
! Trunks del anillo de distribución
interface fastEthernet 0/4
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
interface fastEthernet 0/5
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
! EtherChannel LACP (dos enlaces entre CEMACO y Adidas)
! Se configura el Port-channel lógico antes de agregar sus miembros.
interface port-channel 1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/6 - 7
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 channel-group 1 mode active
 no shutdown
 exit
! Si Packet Tracer indica un conflicto de VLAN mask al crear el canal,
! igualar paso a paso primero las configuraciones físicas y la lógica.
! Servidor local
interface fastEthernet 0/8
 description SERVIDOR_ADIDAS
 switchport mode access
 switchport access vlan 46
 spanning-tree portfast
 no shutdown
 exit
! Puertos no utilizados
interface range fastEthernet 0/9 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

**Verificación principal:**
```cisco
show vtp status
show vlan brief
show spanning-tree vlan 46
show interfaces trunk
show etherchannel summary
```

### 2.5 SW-VINOTECA

**Enlaces:** Fa0/1 a VIN-ACC1; Fa0/2 a SW-ADIDAS; Fa0/3 a SW-CEMACO; Fa0/4 a VIN-SERVER.

```cisco
enable
configure terminal
hostname SW-VINOTECA
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode server
spanning-tree mode rapid-pvst
spanning-tree vlan 56 priority 20480
! Uplinks hacia switches de acceso
interface range fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 56,99
 switchport mode trunk
 no shutdown
 exit
! Trunks del anillo de distribución
interface fastEthernet 0/2
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
interface fastEthernet 0/3
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,26,36,46,56,99
 switchport mode trunk
 no shutdown
 exit
! Servidor local
interface fastEthernet 0/4
 description SERVIDOR_VINOTECA
 switchport mode access
 switchport access vlan 56
 spanning-tree portfast
 no shutdown
 exit
! Puertos no utilizados
interface range fastEthernet 0/5 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

**Verificación principal:**
```cisco
show vtp status
show vlan brief
show spanning-tree vlan 56
show interfaces trunk
```

## 3. Switches de acceso (Cisco 2960)

Cada equipo utiliza `Fa0/1` como uplink Trunk al switch principal y `Fa0/2` para su PC representativa. Se conservan configurados los rangos de puertos destinados a una ampliación posterior de hosts, aunque solo se instaló **una PC por switch**.

### 3.1 CEM-ACC1 — VLAN 16

```cisco
enable
configure terminal
hostname CEM-ACC1
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode client
spanning-tree mode rapid-pvst
interface fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/2 - 21
 switchport mode access
 switchport access vlan 16
 spanning-tree portfast
 no shutdown
 exit
interface range fastEthernet 0/22 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

### 3.2 CEM-ACC2 — VLAN 16

```cisco
enable
configure terminal
hostname CEM-ACC2
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode client
spanning-tree mode rapid-pvst
interface fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/2 - 21
 switchport mode access
 switchport access vlan 16
 spanning-tree portfast
 no shutdown
 exit
interface range fastEthernet 0/22 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

### 3.3 CEM-ACC3 — VLAN 16

```cisco
enable
configure terminal
hostname CEM-ACC3
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode client
spanning-tree mode rapid-pvst
interface fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 16,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/2 - 21
 switchport mode access
 switchport access vlan 16
 spanning-tree portfast
 no shutdown
 exit
interface range fastEthernet 0/22 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

### 3.4 BAN-ACC1 — VLAN 26

```cisco
enable
configure terminal
hostname BAN-ACC1
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode client
spanning-tree mode rapid-pvst
interface fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 26,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/2 - 15
 switchport mode access
 switchport access vlan 26
 spanning-tree portfast
 no shutdown
 exit
interface range fastEthernet 0/16 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

### 3.5 BAN-ACC2 — VLAN 26

```cisco
enable
configure terminal
hostname BAN-ACC2
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode client
spanning-tree mode rapid-pvst
interface fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 26,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/2 - 15
 switchport mode access
 switchport access vlan 26
 spanning-tree portfast
 no shutdown
 exit
interface range fastEthernet 0/16 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

### 3.6 CIN-ACC1 — VLAN 36

```cisco
enable
configure terminal
hostname CIN-ACC1
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode client
spanning-tree mode rapid-pvst
interface fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 36,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/2 - 13
 switchport mode access
 switchport access vlan 36
 spanning-tree portfast
 no shutdown
 exit
interface range fastEthernet 0/14 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

### 3.7 ADI-ACC1 — VLAN 46

```cisco
enable
configure terminal
hostname ADI-ACC1
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode client
spanning-tree mode rapid-pvst
interface fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 46,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/2 - 18
 switchport mode access
 switchport access vlan 46
 spanning-tree portfast
 no shutdown
 exit
interface range fastEthernet 0/19 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

### 3.8 ADI-ACC2 — VLAN 46

```cisco
enable
configure terminal
hostname ADI-ACC2
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode client
spanning-tree mode rapid-pvst
interface fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 46,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/2 - 18
 switchport mode access
 switchport access vlan 46
 spanning-tree portfast
 no shutdown
 exit
interface range fastEthernet 0/19 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

### 3.9 ADI-ACC3 — VLAN 46

```cisco
enable
configure terminal
hostname ADI-ACC3
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode client
spanning-tree mode rapid-pvst
interface fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 46,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/2 - 17
 switchport mode access
 switchport access vlan 46
 spanning-tree portfast
 no shutdown
 exit
interface range fastEthernet 0/18 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

### 3.10 VIN-ACC1 — VLAN 56

```cisco
enable
configure terminal
hostname VIN-ACC1
no ip domain-lookup
vtp domain 202404856
vtp password Cayala2026
vtp version 2
vtp mode client
spanning-tree mode rapid-pvst
interface fastEthernet 0/1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 56,99
 switchport mode trunk
 no shutdown
 exit
interface range fastEthernet 0/2 - 8
 switchport mode access
 switchport access vlan 56
 spanning-tree portfast
 no shutdown
 exit
interface range fastEthernet 0/9 - 24
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
interface range gigabitEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
 exit
end
copy running-config startup-config
```

## 4. PCs y servidores — Direccionamiento aplicado

| Dispositivo | IPv4 | Máscara | Gateway | Switch/puerto |
|---|---|---|---|---|
| CEM-PC1 | `192.168.16.2` | /26 | `192.168.16.1` | CEM-ACC1 Fa0/2 |
| CEM-PC2 | `192.168.16.3` | /26 | `192.168.16.1` | CEM-ACC2 Fa0/2 |
| CEM-PC3 | `192.168.16.4` | /26 | `192.168.16.1` | CEM-ACC3 Fa0/2 |
| BAN-PC1 | `192.168.26.2` | /27 | `192.168.26.1` | BAN-ACC1 Fa0/2 |
| BAN-PC2 | `192.168.26.3` | /27 | `192.168.26.1` | BAN-ACC2 Fa0/2 |
| CIN-PC1 | `192.168.36.2` | /28 | `192.168.36.1` | CIN-ACC1 Fa0/2 |
| ADI-PC1 | `192.168.46.2` | /26 | `192.168.46.1` | ADI-ACC1 Fa0/2 |
| ADI-PC2 | `192.168.46.3` | /26 | `192.168.46.1` | ADI-ACC2 Fa0/2 |
| ADI-PC3 | `192.168.46.4` | /26 | `192.168.46.1` | ADI-ACC3 Fa0/2 |
| VIN-PC1 | `192.168.56.2` | /28 | `192.168.56.1` | VIN-ACC1 Fa0/2 |
| CEM-SERVER | `192.168.16.62` | /26 | `192.168.16.1` | SW-CEMACO Fa0/8 |
| BAN-SERVER | `192.168.26.30` | /27 | `192.168.26.1` | SW-BANCO Fa0/5 |
| CIN-SERVER | `192.168.36.14` | /28 | `192.168.36.1` | SW-CINEPOLIS Fa0/4 |
| ADI-SERVER | `192.168.46.52` | /26 | `192.168.46.1` | SW-ADIDAS Fa0/8 |
| VIN-SERVER | `192.168.56.9` | /28 | `192.168.56.1` | SW-VINOTECA Fa0/4 |

Las direcciones IPv4 de PCs y Server-PT se asignaron manualmente desde `Desktop → IP Configuration → Static`, no mediante CLI de los switches.

## 5. Comandos de diagnóstico y pruebas utilizados

```cisco
! En switches principales
show vtp status
show vtp password
show vlan brief
show interfaces status
show interfaces trunk
show spanning-tree vlan 16
show spanning-tree vlan 26
show spanning-tree vlan 36
show spanning-tree vlan 46
show spanning-tree vlan 56
show etherchannel summary
show ip route
show ip interface brief
show interfaces vlan 46
show arp
show mac address-table
show running-config
```

**Prueba de recuperación aplicada durante el diagnóstico del EtherChannel:**
```cisco
! En SW-CEMACO, durante la incidencia, no como acción rutinaria
configure terminal
interface port-channel 1
 shutdown
 no shutdown
end
```

Se comprobó mediante Simulation que una solicitud ARP era descartada porque `Port-channel1` aparecía bloqueado por STP. Tras la reconvergencia, `Po1` regresó a Forwarding para VLAN 46 y se recuperaron los pings de Adidas y Cinépolis.

**Comandos de prueba desde PC-PT o Server-PT:**
```bat
ipconfig
ping 192.168.16.1
ping 192.168.26.1
ping 192.168.36.1
ping 192.168.46.1
ping 192.168.56.1
arp -a
arp -d
```

## 6. Evidencias y exportación final

Para obtener los **scripts idénticos al estado final real de cada equipo**, exportar el resultado siguiente en los 15 switches (5 principales y 10 de acceso):
```cisco
enable
show running-config
```

Guardar los resultados en `scripts/SW-CEMACO.cfg`, `scripts/SW-BANCO.cfg`, etc. **No sustituir una exportación real por estos comandos reconstruidos** si el catedrático solicita la configuración completa y exacta de cada equipo.

Para capturas del manual técnico usar, como mínimo, `show vtp status`, `show vlan brief`, `show spanning-tree vlan <ID>`, `show etherchannel summary`, `show interfaces trunk`, `show ip route` y los pings completos entre PCs y servidores.

---
**Fin del documento de referencia CLI — Práctica 2, Cayalá.**
