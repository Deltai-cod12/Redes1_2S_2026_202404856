# Manual Técnico — QuetzalDev S.A.

## 1. Inventario de Equipos
 
| Departamento | PCs | Laptops | Servidores | Toma de red | Total |
|---|---|---|---|---|---|
| Recepción | 2 | 1 | 1 | Doble (2 puertos) | 4 |
| Recursos Humanos | 6 | 2 | 0 | 6 puertos | 8 |
| Legal | 3 | 1 | 0 | 3 puertos | 4 |
| Sala de Capacitación | 5 | 5 | 0 | 5 puertos | 10 |
| Diseño e Innovación | 6 | 1 | 1 | 6 puertos | 8 |
| Dirección General | 2 | 2 | 0 | Doble (2 puertos) | 4 |
| Backend | 6 | 0 | 1 | 6 puertos | 7 |
| Data Center | 0 | 0 | 3 | Patch panel 4 puertos (3 usados, 1 libre) | 3 |
| **TOTAL** | **30** | **12** | **6** | — | **48** |
 
Notas rápidas de cada área:
- **Recepción:** las PCs son para los puestos fijos y la laptop es para movilidad administrativa.
- **RRHH:** casi todo es trabajo de oficina, por eso predominan las PCs; las laptops son para reuniones.
- **Legal:** puestos fijos para el trabajo legal, una laptop para salir a reuniones.
- **Capacitación:** se dejó parejo entre PCs y laptops para que la sala sea flexible según el tipo de capacitación.
- **Diseño e Innovación:** aquí trabajan UI/UX, Data Analytics y QA, así que se necesitan más PCs por el procesamiento.
- **Dirección General:** equilibrio entre equipo fijo y móvil para presentaciones y juntas.
- **Backend:** casi todo son PCs fijas para desarrollo, es el área más "de escritorio".
- **Data Center:** se usó patch panel de 4 puertos en vez de toma triple porque queda más ordenado y deja espacio para un servidor más a futuro.

---

## 2. Formato de identificación
 
Formato base: Switch = SW-[AREA] | PC = PC-[AREA]-[N°] | Laptop = LAP-[AREA]-[N°] | Servidor = SRV-[AREA]-[N°] | Punto de red = PD-[AREA]-[N°]
 
| Área | Switch | PCs | Laptops | Servidores | Punto(s) de red |
|---|---|---|---|---|---|
| Recepción (REC) | SW-REC | PC-REC-01, PC-REC-02 | LAP-REC-01 | SRV-REC-01 | PD-REC-01, PD-REC-02 (toma doble) |
| RRHH | SW-RRHH | PC-RRHH-01 a 06 | LAP-RRHH-01, 02 | — | PD-RRHH-01 (6 puertos) |
| Legal (LEG) | SW-LEG | PC-LEG-01 a 03 | LAP-LEG-01 | — | PD-LEG-01 (3 puertos) |
| Capacitación (CAP) | SW-CAP | PC-CAP-01 a 05 | LAP-CAP-01 a 05 | — | PD-CAP-01 (5 puertos) |
| Diseño (DIS) | SW-DIS | PC-DIS-01 a 06 | LAP-DIS-01 | SRV-DIS-01 | PD-DIS-01 (6 puertos) |
| Dirección General (DIR) | SW-DIR | PC-DIR-01, 02 | LAP-DIR-01, 02 | — | PD-DIR-01 (toma doble) |
| Backend (BACK) | SW-BACK | PC-BACK-01 a 06 | — | SRV-BACK-01 | PD-BACK-01 (6 puertos) |
| Data Center (DC) | SW-DC | — | — | SRV-DC-01, 02, 03 | PD-DC-01 (patch panel 4 puertos, 3 usados) |
| **MDF** (no es un depto.) | SW-MDF | — | — | — | RTR-MDF, PP-MDF-01, PP-MDF-02, ODF-MDF-01, UPS-MDF-01 |

---

## 3. Justificación de la Ubicación del MDF

El cuarto de telecomunicaciones (MDF) lo ubique dentro del cuarto de Dirección General, debido a que en esta área se encuentra la Dirección General de QuetzalDev S.A. Esta ubicación permite mantener el equipo principal de comunicaciones en un espacio controlado y de acceso restringido, facilitando su supervisión y administración.

Consideración para una futura mejora: Se podría reorganizar o dividir parte de la Sala de Capacitación para habilitar un espacio exclusivo para el MDF. Esta alternativa permitiría ubicar el cuarto de telecomunicaciones en una posición más central dentro del edificio, reduciendo las distancias promedio del cableado hacia los diferentes departamentos y facilitando la distribución del cableado troncal. Además, permitiría mantener el MDF en un espacio independiente de las actividades administrativas de Dirección General, mejorando su organización, seguridad y facilidad de mantenimiento.

---

## 4. Justificación de la Topología Física Seleccionada por Área

### 4.1 Topología de los Departamentos: Estrella

En todos los departamentos se utiliza una **topología física en estrella**, donde cada dispositivo final posee una conexión independiente hacia el switch correspondiente a su área. Esta elección es adecuada porque permite mantener una estructura sencilla, organizada y fácil de administrar.

#### ¿Por qué utilice estrella en todos los departamentos?

1. **Independencia de fallos:** Si el cable de una computadora se daña, se desconecta o presenta algún problema, únicamente ese dispositivo pierde conectividad. Los demás equipos continúan funcionando normalmente.
2. **Centralización:** Cada dispositivo se conecta directamente al switch del departamento. Esto permite identificar fácilmente cada conexión y facilita las tareas de mantenimiento, diagnóstico y administración de la Capa 1.
3. **Escalabilidad:** La topología permite agregar nuevos dispositivos utilizando puertos disponibles del switch o ampliando el switch cuando sea necesario, sin tener que modificar toda la estructura del departamento.
4. **Costo:** La estrella representa un equilibrio adecuado entre costo, facilidad de implementación y rendimiento. No requiere las grandes cantidades de cable o infraestructura adicional que podrían ser necesarias en topologías más complejas como malla.
5. **Administración:** La centralización de las conexiones en el switch permite identificar rápidamente qué dispositivo presenta problemas y facilita el etiquetado de los cables y puertos.

6. **Laptops:** Decidi no colocar las laptops conectadas directamente por medio de una conexion fisica a la red pensando en que no unicamente se puede usar en una locacion fisica del departamento, por lo que tenia pensado usar algun repetidor wifi o algun otro router para la conexion de las laptops.

---

## 5. Tipo y Categoría de Cable Utilizado por Segmento

| Segmento | Tipo de Cable | Justificación |
|---|---|---|
| Cableado horizontal — PCs de los departamentos | UTP Cat 6 | Buen rendimiento, costo accesible y capacidad para soportar Gigabit Ethernet en las distancias habituales de un edificio |
| Cableado horizontal — Servidores | UTP Cat 6 | Proporciona suficiente capacidad para las conexiones de los servidores y mantiene un estándar uniforme en la infraestructura |
| Cableado troncal — MDF a switches departamentales | Fibra óptica multimodo | Mayor capacidad, menor susceptibilidad a interferencias electromagnéticas y posibilidad de crecimiento futuro |
| Cableado dentro del Data Center — Switch a servidores | UTP Cat 6 | Adecuado para las conexiones de corta distancia dentro del Data Center, instalación sencilla y organizada |
| Conexiones de administración del rack | UTP Cat 6 | Se utiliza para las conexiones Ethernet de los equipos de administración, manteniendo compatibilidad con el resto del cableado estructurado |

**Estándar general:** Se utilizará **T568B** para las terminaciones del cableado UTP, manteniendo el mismo estándar en ambos extremos del cableado horizontal.

---

## 6. Distancias Estimadas de Cableado y Cálculo de Bobinas

Basado en la medicion y escalado de los planos, se llegaron a mediciones exactas para el cableado troncal, por lo que las estimaciones para los demas cableados se hicieron de la manera correcta.

### 6.1 Cableado Troncal (Línea azul punteada) — Fibra Óptica Multimodo

| Área | Distancia |
|---|---|
| Recepción | 9.7899 m |
| Legal | 9.8229 m |
| Sala de Capacitación | 2.9769 m |
| Recursos Humanos | 10.8729 m |
| Dirección General | 10.6896 m |
| Diseño e Innovación | 38.985 m |
| Backend | 24.7571 m |
| Data Center | 24.5671 m |
| **Total de cableado troncal** | **131.4614 m** |

- **Bobina considerada:** 300 m
- **Cantidad de bobinas:** 1 bobina de fibra óptica multimodo

**Justificación:** Una bobina de 300 m cubre los 131.4614 m necesarios para los enlaces troncales y deja aproximadamente 168.5386 m de reserva para terminaciones, ajustes y crecimiento futuro.

### 6.2 Cableado Horizontal (Línea roja) — UTP Cat 6

| Área | Distancia |
|---|---|
| Recepción | 14.00 m |
| Recursos Humanos | 24.00 m |
| Legal | 14.00 m |
| Sala de Capacitación | 30.00 m |
| Diseño e Innovación | 28.00 m |
| Dirección General | 12.00 m |
| Backend | 21.00 m |
| Data Center | 7.50 m |
| **Total de cableado horizontal** | **150.50 m** |

- **Bobina estándar:** 305 m
- **Cantidad de bobinas:** 1 bobina de UTP Cat 6

**Justificación:** Una bobina de 305 m cubre los 150.50 m estimados de cableado horizontal, dejando aproximadamente 154.50 m de reserva para terminaciones, ajustes y futuras ampliaciones.

### 6.3 Resumen de Bobinas

| Tipo de cableado | Cantidad | Longitud |
|---|---|---|
| Horizontal (UTP Cat 6) | 1 bobina | 305 m |
| Troncal (Fibra óptica multimodo) | 1 bobina | 300 m |
| **Total** | **2 bobinas** | — |

---

## 7. Justificación de Equipos Activos

| Equipo | Función |
|---|---|
| Router | Conecta la red interna de QuetzalDev S.A. con Internet y otras redes externas |
| Switch principal | Centraliza las conexiones de los 8 switches departamentales y administra el tráfico de la red |
| Switches departamentales | Conectan los dispositivos de cada área y facilitan la administración y detección de fallos |
| Switch Data Center | Conecta los 3 servidores principales y permite agregar equipos posteriormente |
| Patch Panel | Organiza y facilita la administración de las conexiones del cableado horizontal |
| ODF | Organiza y termina la fibra óptica utilizada en los enlaces troncales |
| UPS | Mantiene los equipos de red funcionando ante cortes eléctricos |

---

## 8. Dimensionamiento

| Elemento | Cantidad |
|---|---|
| Puntos de red | 48 |
| Patch Panel | 2 × 24 puertos = 48 puertos |
| Switch principal | 48 puertos o superior |
| Switches departamentales | 8 |
| Switch Data Center | 1 |
| ODF | 1 (para enlaces de fibra) |
| UPS | 1 (para respaldo del equipo del MDF) |

---

## 9. Justificación del Medio de Transmisión Seleccionado para el Cableado Troncal

**Cableado troncal MDF a switches departamentales:** Fibra óptica multimodo.

- Se selecciona por su alta velocidad, mayor capacidad de transmisión y resistencia a interferencias electromagnéticas.
- Es adecuada para las distancias estimadas, especialmente para Diseño e Innovación (38.985 m) y Backend (24.7571 m).
- Permite escalabilidad futura y mantiene un enlace troncal estable entre el MDF y los diferentes departamentos.
- Aunque tiene un costo mayor que UTP, se justifica por su rendimiento y confiabilidad en el backbone de la empresa.

---

## 10. Justificación del Tipo de Canalización Utilizada

| Segmento | Canalización | Motivo |
|---|---|---|
| Cableado troncal | Escalerilla metálica abierta | Permite organizar y transportar la fibra óptica de forma ordenada, facilitando el mantenimiento y futuras ampliaciones |
| Cableado horizontal | Canaleta cerrada | Proporciona protección física y una instalación limpia y organizada hacia las tomas de red |

**Justificación general:** Esta combinación ofrece protección, organización, accesibilidad y facilidad de crecimiento en la infraestructura de cableado.

---

## 11. Justificación del Rack

- Se propone un **rack de piso de 24U**, debido a la cantidad de equipos que se instalarán en el MDF.
- Permite alojar el switch principal, patch panels, ODF, router, UPS y organizadores de cableado.
- Ofrece espacio adicional para crecimiento futuro y facilita el mantenimiento y organización de los equipos.

---

## 12. Estimación del Consumo Eléctrico y Capacidad de UPS

| Equipo | Consumo Aproximado |
|---|---|
| Switch principal | ≈ 50 W |
| Router | ≈ 30 W |
| ODF y equipos auxiliares | ≈ 10 W |
| Otros equipos de administración | ≈ 30 W |
| **Consumo total estimado** | **≈ 120 W** |

**Recomendación:** UPS de **1000 VA / 600 W**, para proporcionar un margen de seguridad ante variaciones de consumo y futuras ampliaciones. Este UPS permitirá mantener funcionando temporalmente los equipos principales del MDF durante interrupciones eléctricas, evitando apagados repentinos y proporcionando tiempo suficiente para restablecer la energía o realizar un apagado controlado.

---

## 13. Tabla de Straight-Through / Crossover por Enlace

### 13.1 Straight-Through

| Enlace | Tipo | Justificación |
|---|---|---|
| Router a Switch principal | Straight-through | Conecta dispositivos de diferente tipo |
| Switch principal a Switch de Recepción | Straight-through | Conecta el switch principal con un switch departamental mediante el enlace troncal |
| Switch principal a Switch de Recursos Humanos | Straight-through | Conecta switches mediante el enlace troncal |
| Switch principal a Switch de Legal | Straight-through | Conecta switches mediante el enlace troncal |
| Switch principal a Switch de Capacitación | Straight-through | Conecta switches mediante el enlace troncal |
| Switch principal a Switch de Diseño e Innovación | Straight-through | Conecta switches mediante el enlace troncal |
| Switch principal a Switch de Dirección General | Straight-through | Conecta switches mediante el enlace troncal |
| Switch principal a Switch de Backend | Straight-through | Conecta switches mediante el enlace troncal |
| Switch principal a Switch de Data Center | Straight-through | Conecta switches mediante el enlace troncal |
| Switch a PC | Straight-through | Conecta un dispositivo final con un switch |
| Switch a Servidor | Straight-through | Conecta un servidor con un switch |

### 13.2 Crossover

No se requiere crossover en la topología propuesta, ya que las conexiones se realizan entre dispositivos de diferente tipo y los equipos modernos cuentan con **Auto-MDI/MDIX**.

> **Nota:** Para el cableado estructurado se utilizará T568B en ambos extremos, manteniendo una conexión straight-through.

---

## 14. Disposición de Pines Documentada

### 14.1 Ejemplo 1: Straight-Through — PC-RRHH-01 a SW-RRHH

La conexión entre la PC-RRHH-01 y el SW-RRHH, pasando por el punto de red PD-RRHH-01, utiliza un cable straight-through con estándar T568B en ambos extremos.

| Pin | Extremo A — PC-RRHH-01 (T568B) | Extremo B — SW-RRHH (T568B) |
|---|---|---|
| 1 | Blanco/Naranja | Blanco/Naranja |
| 2 | Naranja | Naranja |
| 3 | Blanco/Verde | Blanco/Verde |
| 4 | Azul | Azul |
| 5 | Blanco/Azul | Blanco/Azul |
| 6 | Verde | Verde |
| 7 | Blanco/Marrón | Blanco/Marrón |
| 8 | Marrón | Marrón |

**Justificación:** Ambos extremos utilizan T568B, por lo que corresponde a un cable straight-through. Este tipo de conexión se utiliza en los enlaces entre dispositivos finales y switches.

### 14.2 Ejemplo 2: Crossover — PC-BACK-01 a PC-BACK-02

Como ejemplo de conexión directa entre dispositivos del mismo tipo, se consideran las PC-BACK-01 y PC-BACK-02. El cable utiliza T568A en un extremo y T568B en el otro.

| Pin | Extremo A — PC-BACK-01 (T568A) | Extremo B — PC-BACK-02 (T568B) |
|---|---|---|
| 1 | Blanco/Verde | Blanco/Naranja |
| 2 | Verde | Naranja |
| 3 | Blanco/Naranja | Blanco/Verde |
| 4 | Azul | Azul |
| 5 | Blanco/Azul | Blanco/Azul |
| 6 | Naranja | Verde |
| 7 | Blanco/Marrón | Blanco/Marrón |
| 8 | Marrón | Marrón |

**Justificación:** Al utilizar T568A en un extremo y T568B en el otro, se intercambian los pares de transmisión y recepción, formando un cable crossover. En la red de QuetzalDev S.A. no se requiere este tipo de cable, ya que los dispositivos se conectan mediante switches y cuentan con Auto-MDI/MDIX.

---

## 15. Tabla de Etiquetado de Cables

Siguiendo el formato establecido para QuetzalDev S.A., se utiliza:

| Tipo de cableado | Formato |
|---|---|
| Cableado horizontal | Área-PD-Número |
| Cableado troncal | MDF-Área |

### 15.1 Cableado Horizontal

| Área | Etiqueta | Dispositivos |
|---|---|---|
| Recepción | REC-PD-01 | PC-REC-01 |
| Recepción | REC-PD-02 | PC-REC-02 |
| Recursos Humanos | RRHH-PD-01 | PC-RRHH-01 a PC-RRHH-06 |
| Legal | LEG-PD-01 | PC-LEG-01 a PC-LEG-03 |
| Sala de Capacitación | CAP-PD-01 | PC-CAP-01 a PC-CAP-05 |
| Diseño e Innovación | DIS-PD-01 | PC-DIS-01 a PC-DIS-06 |
| Dirección General | DIR-PD-01 | PC-DIR-01 y PC-DIR-02 |
| Backend | BACK-PD-01 | PC-BACK-01 a PC-BACK-06 |
| Data Center | DC-PD-01 | SRV-DC-01 a SRV-DC-03 |

### 15.2 Cableado Troncal

| Etiqueta | Destino |
|---|---|
| MDF-REC | SW-REC |
| MDF-RRHH | SW-RRHH |
| MDF-LEG | SW-LEG |
| MDF-CAP | SW-CAP |
| MDF-DIS | SW-DIS |
| MDF-DIR | SW-DIR |
| MDF-BACK | SW-BACK |
| MDF-DC | SW-DC |

---

## 16. Comparación entre el Etiquetado Usado y el Estándar TIA/EIA-606

- **Etiquetado utilizado en la práctica:** Se utiliza un formato sencillo y propio, por ejemplo PC-RRHH-01, SW-RRHH, PD-RRHH-01 y MDF-RRHH. Esto permite identificar rápidamente el dispositivo, área y punto de red.
- **TIA/EIA-606:** Utiliza un sistema de identificadores únicos para los diferentes elementos de la infraestructura, incluyendo espacios de telecomunicaciones, racks, patch panels, cables, puertos y rutas. Además, estos identificadores deben relacionarse con registros de administración.

### 16.1 Diferencias Principales

| # | Aspecto | Etiquetado Utilizado | TIA/EIA-606 |
|---|---|---|---|
| 1 | Nivel de detalle | Identifica principalmente dispositivos, áreas y puntos de red | También contempla identificadores para racks, patch panels, puertos, rutas, espacios y cableado troncal |
| 2 | Documentación | Solamente se registra el nombre del cable o dispositivo | Requiere mantener registros relacionados con cada identificador (ubicación, tipo de cable, longitud, terminaciones y conexiones) |
| 3 | Escalabilidad | Diseñado específicamente para esta práctica y un único edificio | Permite administrar infraestructuras mucho más grandes mediante diferentes clases de administración y estructuras de identificación escalables |

---

## 17. Descripción del Flujo de Conexión End-to-End

1. **Dispositivo final:** Una PC, laptop o servidor inicia la comunicación.
2. **Punto de red:** El dispositivo se conecta a su respectivo PD-[AREA].
3. **Switch departamental:** El punto de red se conecta al SW-[AREA], que administra el tráfico del departamento.
4. **Cableado troncal:** El switch departamental envía el tráfico mediante fibra óptica hacia el SW-MDF.
5. **Switch principal:** SW-MDF recibe y dirige el tráfico hacia la red correspondiente.
6. **Router:** Cuando la comunicación es hacia una red externa o Internet, el SW-MDF envía el tráfico al RTR-MDF.
7. **Destino:** El router entrega la información a la red externa o Internet.

**Ejemplo:**
```
PC-RRHH-01 a PD-RRHH-01 a SW-RRHH a Fibra óptica a SW-MDF a RTR-MDF a Internet
```

**Justificación:** Este flujo permite una comunicación organizada y jerárquica, siguiendo la topología de árbol definida para QuetzalDev S.A.

---

## 18. Presupuesto Estimado del Proyecto

### 18.1 Equipos de Red e Infraestructura

| Ítem | Costo |
|---|---|
| 8 switches departamentales de 24 puertos | Q8,000 |
| 1 switch principal de 48 puertos | Q2,500 |
| 1 router | Q1,500 |
| 2 patch panels de 24 puertos | Q1,200 |
| 1 ODF para fibra óptica | Q800 |
| 1 rack de piso de 24U | Q2,000 |
| 1 UPS de 1000 VA / 600 W | Q1,200 |
| **Subtotal de equipos** | **Q17,200** |

### 18.2 Cableado y Materiales

| Ítem | Costo |
|---|---|
| 1 bobina de UTP Cat 6 de 305 m | Q1,200 |
| 1 bobina de fibra óptica multimodo de 300 m | Q2,500 |
| Canaleta cerrada | Q1,000 |
| Escalerilla metálica abierta | Q1,500 |
| 33 tomas/keystone/faceplates | Q825 |
| 48 patch cords Cat 6 | Q1,680 |
| Conectores y consumibles | Q500 |
| Organizadores de cableado | Q400 |
| Material de etiquetado | Q200 |
| **Subtotal de materiales** | **Q9,805** |

### 18.3 Instalación y Configuración

| Ítem | Costo |
|---|---|
| Instalación del cableado horizontal (150.50 m) | Q1,806 |
| Instalación del cableado troncal de fibra (131.4614 m) | Q2,366 |
| Ponchado, terminación y pruebas de conexiones | Q1,200 |
| Instalación del rack, patch panels y ODF | Q1,200 |
| Instalación y configuración básica de equipos de red | Q1,500 |
| Etiquetado y documentación de la infraestructura | Q600 |
| **Subtotal de instalación** | **≈ Q8,672** |

### 18.4 Mantenimiento

| Ítem | Costo |
|---|---|
| Mantenimiento preventivo anual | ≈ Q1,200 |

Incluye revisión de switches, router, UPS, rack, conexiones, cableado y limpieza básica de los equipos de comunicaciones.

### 18.5 Inventario Existente de QuetzalDev S.A.

| Ítem | Costo |
|---|---|
| 30 PCs de escritorio | Q0 — Inventario existente |
| 12 laptops | Q0 — Inventario existente |
| 6 servidores | Q0 — Inventario existente |
| **Subtotal** | **Q0** |

### 18.6 Resumen del Presupuesto

| Concepto | Costo |
|---|---|
| Equipos de red | Q17,200 |
| Cableado y materiales | Q9,805 |
| Instalación y configuración | ≈ Q8,672 |
| Mantenimiento primer año | Q1,200 |
| **Subtotal del proyecto** | **≈ Q36,877** |
| Contingencia 10% | ≈ Q3,688 |
| **Costo total estimado** | **≈ Q40,565** |

**Justificación:** El presupuesto contempla la implementación completa de la infraestructura de red, incluyendo equipos, cableado horizontal y troncal, canalización, rack, UPS, terminaciones, instalación, configuración, etiquetado y mantenimiento durante el primer año. El inventario tecnológico existente de QuetzalDev S.A. no se incluye como costo de adquisición.

> **Nota:** Los 33 puertos de las tomas de red corresponden a las tomas físicas definidas para las PCs. Los 48 puertos de patch panel se mantienen como dimensionamiento general para los puntos de red y crecimiento de la infraestructura.
