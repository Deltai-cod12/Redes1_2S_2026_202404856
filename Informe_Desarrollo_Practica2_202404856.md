# Informe de Desarrollo – Práctica 2
## Red de la Ciudad Comercial Cayalá

**Estudiante:** Ángel Emanuel Rodriguez Corado  
**Carnet:** 202404856  
**Curso:** Redes de Computadoras 1  
**Herramienta:** Cisco Packet Tracer

## 1. Descripción del proyecto

Se diseñó una red para comunicar cinco establecimientos de Ciudad Cayalá: CEMACO, Banco Industrial, Cinépolis, Adidas y Vinoteca. El proyecto utiliza VLANs para separar cada establecimiento y un switch multicapa para permitir la comunicación entre redes.

Se implementó una topología híbrida: switches de acceso conectados en estrella a su switch principal, y enlaces redundantes entre los switches principales. Se configuraron VTP, Rapid PVST+, EtherChannel con LACP y enrutamiento inter-VLAN.

## 2. Dispositivos utilizados

| Dispositivo | Cantidad |
|---|---:|
| Switches principales Cisco 3560 | 5 |
| Switches de acceso Cisco 2960 | 10 |
| PCs de prueba (una por switch de acceso) | 10 |
| Servidores | 5 |
| **Total de dispositivos** | **30** |

Las redes se dimensionaron para los 157 hosts solicitados, pero en Packet Tracer se representó una computadora por switch de acceso para realizar las pruebas.

## 3. VLANs y direccionamiento IP

| Establecimiento | VLAN | Subred | Gateway | Servidor |
|---|---:|---|---|---|
| CEMACO | 16 | `192.168.16.0/26` | `192.168.16.1` | `192.168.16.62` |
| Banco Industrial | 26 | `192.168.26.0/27` | `192.168.26.1` | `192.168.26.30` |
| Cinépolis | 36 | `192.168.36.0/28` | `192.168.36.1` | `192.168.36.14` |
| Adidas | 46 | `192.168.46.0/26` | `192.168.46.1` | `192.168.46.52` |
| Vinoteca | 56 | `192.168.56.0/28` | `192.168.56.1` | `192.168.56.9` |

La VLAN **99** se utilizó como nativa en los trunks y la VLAN **999** se asignó a los puertos no utilizados.

En **SW-CEMACO** se habilitó `ip routing` y se configuraron las interfaces VLAN con los gateways de cada red.

Para revisar la configuración en **SW-CEMACO**:
```cisco
enable
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
```
## 4. VTP

Se configuró VTP versión 2 con el dominio `202404856`. Los cinco switches principales operan en modo **Server** y los diez switches de acceso en modo **Client**.

En los cinco principales se comprobaron la revisión **29**, la existencia de **12 VLANs** y la coincidencia del digest MD5. También se verificó el estado de los switches de acceso.

Comandos para revisar VTP en cualquier switch:
```cisco
enable
show vtp status
show vlan brief
```
## 5. STP y EtherChannel

Se implementó **Rapid PVST+** para evitar bucles en los enlaces redundantes. Los switches principales son Root Bridge de sus respectivas VLANs:

| Switch | VLAN | Prioridad configurada |
|---|---:|---:|
| SW-CEMACO | 16 | 4096 |
| SW-BANCO | 26 | 8192 |
| SW-CINEPOLIS | 36 | 12288 |
| SW-ADIDAS | 46 | 16384 |
| SW-VINOTECA | 56 | 20480 |

Para verificar el Root Bridge, ejecutar el comando que corresponda en cada switch principal:
```cisco
show spanning-tree vlan 16
show spanning-tree vlan 26
show spanning-tree vlan 36
show spanning-tree vlan 46
show spanning-tree vlan 56
```
**Nota:** cada comando debe ejecutarse en el switch principal de su respectiva VLAN, no necesariamente todos en el mismo.

Se configuró **EtherChannel con LACP** entre **SW-CEMACO** y **SW-ADIDAS**. Se agruparon `Fa0/6` y `Fa0/7` mediante `Port-channel1`.

Para comprobarlo en ambos extremos:
```cisco
enable
show etherchannel summary
show interfaces trunk
```
El resultado correcto es `Po1(SU)` con los miembros `Fa0/6(P)` y `Fa0/7(P)`.

## 6. Problemas encontrados y soluciones
### 6.1 Problema al configurar EtherChannel

Al principio, los puertos físicos no se agrupaban porque había diferencias en la configuración de los trunks y las VLANs permitidas.

**Solución:** se igualaron los parámetros del enlace en ambos switches y se configuró el trunk de Port-channel1 correctamente. Después se comprobó la agrupación LACP.
### 6.2 Fallos de conectividad en Cinépolis y Adidas

En las primeras pruebas, algunas PCs no podían comunicarse con su gateway. En **Simulation** de Packet Tracer se encontró que SW-CEMACO descartaba tráfico porque `Port-channel1` aparecía bloqueado por STP.

**Solución:** se realizó un apagado y encendido administrativo controlado de `Port-channel1` en SW-CEMACO. Esto permitió la reconvergencia y las VLANs afectadas recuperaron conectividad. No se deshabilitó STP ni se eliminó EtherChannel.

Comandos empleados para esa recuperación:
```cisco
enable
configure terminal
interface port-channel 1
shutdown
no shutdown
end
```
Después se comprobó el estado con:
```cisco
show spanning-tree vlan 46
show etherchannel summary
show interfaces trunk
```
## 7. Pruebas de funcionamiento

Se comprobó la comunicación de las diez PCs de prueba con sus gateways, con otras PCs y con los servidores. También se verificó el acceso entre las cinco VLANs.

Los siguientes comandos pueden repetirse en **Desktop → Command Prompt** de las PCs o servidores indicados. Ejecutar cada `ping` por separado.
### 7.1 Ping entre PCs de la misma VLAN

Desde **CEM-PC1** hacia **CEM-PC2**:
```bat
ping 192.168.16.3
```
Desde **BAN-PC1** hacia **BAN-PC2**:
```bat
ping 192.168.26.3
```
Desde **ADI-PC2** hacia **ADI-PC3**:
```bat
ping 192.168.46.4
```
### 7.2 Ping entre PCs de diferentes VLANs

Desde **CEM-PC1** hacia **ADI-PC1**:
```bat
ping 192.168.46.2
```
Desde **CIN-PC1** hacia **VIN-PC1**:
```bat
ping 192.168.56.2
```
### 7.3 Ping de una PC a su gateway y a un servidor

Desde **CIN-PC1**:
```bat
ping 192.168.36.1
ping 192.168.36.14
ping 192.168.16.62
```
### 7.4 Ping entre servidores

Desde **VIN-SERVER**:
```bat
ping 192.168.16.62
ping 192.168.26.30
ping 192.168.36.14
ping 192.168.46.52
```
Esta última prueba se realizó con respuestas exitosas para los cuatro servidores.
### 7.5 Comandos adicionales de diagnóstico

En una **PC o servidor**:
```bat
ipconfig
arp -a
```
En un **switch principal o de acceso**, según lo que se quiera revisar:
```cisco
show interfaces status
show vlan brief
show interfaces trunk
show spanning-tree vlan 46
show mac address-table
```
En **SW-CEMACO**:
```cisco
show ip route
show ip interface brief
show arp
```
En una prueba de ping completa se esperan cuatro respuestas. Si la primera prueba pierde un paquete, repetirla y revisar ARP o STP antes de dar por estable la comunicación.

## 8. Conclusión

Se logró implementar una red funcional para los cinco establecimientos mediante VLANs, VTP, Rapid PVST+, EtherChannel y enrutamiento inter-VLAN. Las PCs y servidores configurados pudieron comunicarse con equipos de su propia zona y con otras redes.

La práctica también permitió identificar y resolver problemas de STP y EtherChannel utilizando comandos CLI y el modo Simulation de Packet Tracer.
