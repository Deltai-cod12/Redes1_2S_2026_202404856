# Informe de Desarrollo

Este documento detalla el proceso de diseño físico y lógico para la infraestructura de red de QuetzalDev S.A., fundamentando las decisiones técnicas adoptadas en la planificación de la Capa 1 y Capa 2.

## 1. Proceso de Diseño y Criterios de Selección

El diseño de la red se estructuró a partir del plano arquitectónico del edificio, distribuyendo los dispositivos de forma optimizada para garantizar la conectividad de los 48 puntos lógicos requeridos.

- **Criterios de topología:** Se seleccionó una topología física en estrella para todos los departamentos debido a su alta tolerancia a fallos, facilidad de administración y escalabilidad independiente.
- **Criterios de medios de transmisión:** Se evaluaron las distancias y requerimientos de ancho de banda para definir el uso de cableado UTP Cat 6 en el segmento horizontal y fibra óptica multimodo en el segmento troncal.
- **Criterios de equipos activos:** Se dimensionaron switches departamentales de 24 puertos y un switch principal de 48 puertos para centralizar el tráfico del edificio, respaldados por un sistema UPS para continuidad operativa ante fallos eléctricos.

## 2. Retos de Planificación Física

La interpretación del plano base presentó los siguientes desafíos principales:

- **Ubicación del MDF:** Se determinó su instalación inicial en la Dirección General por razones de control y seguridad, identificando a su vez la oportunidad de reubicación futura hacia un área más centralizada (como la Sala de Capacitación) para optimizar las distancias totales del cableado troncal.
- **Distancias y trayectorias:** Se compensaron las diferencias de metraje entre áreas cercanas (como Legal o Capacitación, menores a 10 metros) y zonas alejadas (como Diseño e Innovación, que alcanza los 38.98 m), asegurando que ninguna distancia horizontal o troncal supere los límites máximos estipulados por los estándares internacionales de cableado estructurado.
- **Decision de los cables:** Se decidio ir colocando los cables siguiendo las mismas paredes de la estructura. Las razones técnicas principales son:

    - **Protección física y mecánica:** Las paredes ofrecen una superficie firme y segura para fijar la canalización (bandejas, canaletas cerradas o tuberías). Si los cables pasaran directamente por el centro de un pasillo o área abierta, estarían expuestos a pisadas, golpes de mobiliario, sillas de oficina o el paso de personal, lo que dañaría la cubierta del cable y provocaría cortes o intermitencias en la red.
    - **Estándares de cableado estructurado (TIA/EIA-568):** Los normativos de telecomunicaciones exigen que el tendido horizontal siga rutas ordenadas, paralelas a las paredes y formando ángulos rectos (90°). Esto garantiza que el cableado sea predecible, fácil de rastrear y no interfiera con el tránsito cotidiano de las personas.
    - **Estética y orden (facilidad de mantenimiento):** Seguir el contorno de los muros permite ocultar o integrar el cableado mediante canaletas de superficie o zócalos de manera limpia. Cruzar un pasillo por el medio requeriría romper el piso para canalizar de forma subterránea (lo cual es muy costoso y complejo en un edificio existente) o dejar cables a la vista, lo que genera un riesgo grave de tropezones y una pésima presentación visual.
    - **Seguridad laboral y normas de evacuación:** Los pasillos son rutas de evacuación ante emergencias. Cruzar cables por los pasillos viola las normas básicas de seguridad e higiene industrial, ya que obstruye el libre tránsito y puede causar accidentes. Seguir los perímetros de las paredes mantiene las vías de escape completamente despejadas.


## 3. Justificación del Cableado Troncal

El enlace troncal (backbone) que conecta el MDF con los switches departamentales se diseñó utilizando fibra óptica multimodo.

- **Ancho de banda y velocidad:** Soporta con holgura la agregación de tráfico proveniente de todo el edificio, evitando cuellos de botella hacia el switch principal.
- **Distancias extendidas:** Permite cubrir de manera eficiente tramos largos sin atenuación significativa de la señal, destacando el trayecto hacia el Departamento de Diseño e Innovación (38.98 m) y el Departamento de Backend (24.76 m).
- **Inmunidad electromagnética:** Al ser un medio óptico, está libre de interferencias electromagnéticas (EMI), garantizando una comunicación estable y segura entre el cuarto de telecomunicaciones y las áreas operativas.
