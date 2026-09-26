# Actividad Práctica 5
## Enrutamiento Inter-VLAN

**Nombre:** Ángel Emanuel Rodriguez Corado  
**Registro Académico:** 202404856  
**Curso:** Redes de Computadoras 1  

---

# Topología completa

En esta actividad se implementan dos métodos de enrutamiento Inter-VLAN:

- Escenario 1: Router-on-a-Stick (ROAS)
- Escenario 2: Switch de Capa 3 utilizando SVI

## ![alt text](image.png) ![alt text](image-1.png)

> Pegar aquí la captura donde se observen ambos escenarios completos.

---

# Escenario 1 - Router-on-a-Stick (ROAS)

Para este escenario se utiliza un Router 2911, un Switch 2960 y dos PCs.

Se utilizan las siguientes VLANs:

| VLAN | Nombre |
|---|---|
| 10 | Marketing |
| 20 | Engineering |

El switch utiliza puertos de acceso para las PCs y un enlace trunk hacia el router.

---

## Configuración de subinterfaces en el Router

Se configuraron dos subinterfaces sobre la interfaz física GigabitEthernet0/0.

La subinterfaz correspondiente a VLAN 10 utiliza:

```text
interface GigabitEthernet0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
```

La subinterfaz correspondiente a VLAN 20 utiliza:

```text
interface GigabitEthernet0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
```

### Captura

> ![alt text](image-2.png)

---

## Tabla de enrutamiento del Router

Se verificó la tabla de enrutamiento utilizando:

```text
show ip route
```

En ella deben aparecer las redes correspondientes a VLAN 10 y VLAN 20 como redes directamente conectadas.

### Captura

> ![alt text](image-3.png)

---

## Prueba de conectividad entre VLANs

Desde la PC perteneciente a VLAN 10 se realizó un ping hacia la PC perteneciente a VLAN 20.

Comando utilizado:

```text
ping 192.168.20.10
```

La prueba demuestra que el Router realiza correctamente el enrutamiento entre ambas VLANs.

### Captura

> ![alt text](image-4.png)

---

# Escenario 2 - Switch de Capa 3 utilizando SVI

Para este escenario se utiliza un Switch Multicapa 3650 y dos PCs.

Se utilizan las siguientes VLANs:

| VLAN | Nombre |
|---|---|
| 10 | Marketing |
| 20 | Engineering |

En este caso no se utiliza un router externo. El propio Switch de Capa 3 realiza el enrutamiento entre las VLANs.

---

## Configuración de interfaces VLAN

Se crearon las interfaces virtuales correspondientes a VLAN 10 y VLAN 20.

Configuración de VLAN 10:

```text
interface Vlan10
ip address 192.168.10.1 255.255.255.0
```

Configuración de VLAN 20:

```text
interface Vlan20
ip address 192.168.20.1 255.255.255.0
```

También se habilitó el enrutamiento de Capa 3 mediante:

```text
ip routing
```

### Captura

> ![alt text](image-5.png)

---

## Tabla de enrutamiento del Switch Capa 3

Se verificó la tabla de enrutamiento mediante:

```text
show ip route
```

En ella deben aparecer las redes de VLAN 10 y VLAN 20 como directamente conectadas.

### Captura

> ![alt text](image-6.png)

---

## Prueba de conectividad entre VLANs

Desde la PC perteneciente a VLAN 10 se realizó un ping hacia la PC perteneciente a VLAN 20.

Comando utilizado:

```text
ping 192.168.20.10
```

La prueba demuestra que el Switch de Capa 3 realiza correctamente el enrutamiento entre ambas VLANs.

### Captura

> ![alt text](image-7.png)


---

# Comparación entre Router-on-a-Stick y Switch de Capa 3

En Router-on-a-Stick, el tráfico entre VLANs debe salir del switch hacia un router mediante un enlace trunk. El router recibe las tramas etiquetadas con 802.1Q, realiza el enrutamiento mediante las subinterfaces y devuelve el tráfico hacia el switch.

En el escenario con Switch de Capa 3, el enrutamiento se realiza directamente dentro del switch mediante interfaces virtuales SVI y el comando `ip routing`.

Para una red empresarial con alto tráfico, el Switch de Capa 3 resulta más eficiente debido a que el procesamiento del tráfico entre VLANs se realiza directamente en el hardware del switch, evitando que todo el tráfico deba utilizar un único enlace físico hacia un router externo.


