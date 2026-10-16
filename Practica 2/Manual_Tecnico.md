# Manual técnico — Red de la Ciudad Comercial Cayalá

**Universidad de San Carlos de Guatemala**  
**Facultad de Ingeniería — Escuela de Ciencias y Sistemas**  
**Curso:** Redes de Computadoras 1  
**Práctica:** Práctica 2 — Segundo semestre de 2026  
**Estudiante:** Dorbal Emilio Aldana Ramos  
**Carnet:** 202400388  
**Simulador:** Cisco Packet Tracer  
**Formato de entrega:** Markdown (`.md`)

---

## 1. Introducción

Este manual presenta el diseño y la evidencia disponible de la red simulada para cinco sectores de Ciudad Cayalá: **Plaza Cayalá, Lirios de Cayalá, Área Corporativa, Paseo Cayalá y Cardales de Cayalá**. Se utilizan switches Cisco 3560 como equipos principales de zona y Cisco 2960 como equipos de acceso, con conexiones redundantes en distintas partes de la topología.

La práctica solicita segmentación mediante VLANs, VTP, Rapid PVST+, EtherChannel, direccionamiento IP, pruebas de conectividad y análisis de dominios de colisión. Se usan las seis capturas de la topología proporcionadas inicialmente y **once capturas adicionales** de mapa, comandos y pruebas. La información se organiza distinguiendo entre **lo comprobado por las capturas**, **lo visible en la topología** y **lo que todavía no se ha demostrado**.

## 2. Objetivos y requerimientos

El propósito es modelar una infraestructura de capa 2 para sectores con diferente tráfico y criticidad, incorporar caminos alternativos y evaluar la comunicación entre hosts. El enunciado exige una topología híbrida que combine al menos dos esquemas, un switch principal por zona, VLAN nativa 99, VLAN Blackhole 999 y troncales configuradas explícitamente. Solicita VTP con dominio igual al carnet, Rapid PVST+ con root bridge por VLAN y EtherChannel mediante **LACP**, ya que el carnet termina en el dígito par **8**.

Los hosts solicitados por zona son **60, 28, 12, 50 y 7**, respectivamente (**157 en total**). En las capturas del archivo actual se ven **20 equipos finales**, utilizados como representación del escenario; este hecho se conserva como diferencia pendiente de aclarar para el cumplimiento integral del enunciado.

## 3. Referencia territorial y topología

### 3.1. Plano aportado como referencia

![Plano de referencia de Cayalá](img/mapa_referencia_cayala.png)

*Figura 1. Croquis aportado de Cayalá. Sirve como contexto visual de la distribución urbana; no identifica por sí mismo los switches, las VLANs ni la localización exacta de cada área de la red.*

### 3.2. Vista general implementada en Packet Tracer

![Topología general de Cayalá](img/topologia_general.png)

*Figura 2. Topología de las cinco áreas. Enlaces discontinuos: conexiones entre switches de zona y/o de acceso; enlaces hacia los hosts: conexiones de acceso.*

Se identifican dos tipos de estructura: una **estrella** hacia los equipos finales y una **interconexión redundante o parcialmente mallada** entre switches. En Plaza, Lirios y Paseo, los 2960 forman triángulos con el 3560 principal, por lo que el árbol de expansión resulta relevante para evitar bucles.

### 3.3. Inventario de dispositivos visibles

| Sector | Switches Cisco 3560 | Switches Cisco 2960 | Computadoras | Laptops | Equipos finales |
|---|---:|---:|---:|---:|---:|
| Plaza Cayalá | 1 | 2 | 4 | 0 | 4 |
| Lirios de Cayalá | 1 | 2 | 2 | 2 | 4 |
| Área Corporativa | 1 | 1 | 2 | 2 | 4 |
| Paseo Cayalá | 1 | 2 | 3 | 1 | 4 |
| Cardales de Cayalá | 1 | 0 | 4 | 0 | 4 |
| **Total** | **5** | **7** | **15** | **5** | **20** |

**Total de switches visibles: 12**. Los 157 hosts del enunciado son un requisito de la práctica, no el número de equipos que se alcanza a observar en esta representación.

### 3.4. Interconexiones principales visibles

| Conexión | Enlaces físicos apreciables | Razón de diseño |
|---|---:|---|
| Plaza ↔ Paseo | 2 (interfaces `Gi0/1` y `Gi0/2` visibles) | Mayor capacidad y redundancia para zonas comerciales; LACP confirmado desde Paseo |
| Plaza ↔ Cardales | 1 | Entrada sencilla para un área con menor redundancia visible |
| Plaza ↔ Área Corporativa | 1 | Integra la zona administrativa |
| Área Corporativa ↔ Paseo | 1 | Proporciona otro recorrido posible |
| Área Corporativa ↔ Lirios | 1 | Conecta el sector Lirios por otra ruta |
| Paseo ↔ Lirios | 1 | Ruta alternativa desde Lirios |

El plano no sustituye un inventario completo de puertos. Para identificar las interfaces efectivas se usa la configuración CLI de cada equipo (`show interfaces status`, `show interfaces trunk` y `show running-config`).

## 4. Distribución por zonas

### 4.1. Plaza Cayalá

![Plaza Cayalá](img/zona_plaza_cayala.png)

*Figura 3. `SW-Plaza`, dos switches 2960 y las PC0–PC3.*

Este sector utiliza dos switches de acceso, interconectados entre sí y conectados al switch principal. El doble enlace hacia Paseo incrementa la capacidad disponible cuando está agregado correctamente, y las conexiones de acceso pueden mantener caminos alternativos controlados por STP. Se clasifica como de tráfico alto para efectos de diseño académico.

### 4.2. Lirios de Cayalá

![Lirios de Cayalá](img/zona_lirios.png)

*Figura 4. `SW-Lirios`, dos switches de acceso y los equipos PC10, PC11, Laptop2 y Laptop3.*

Se utiliza una estrella redundante: cada 2960 llega al principal y ambos 2960 mantienen una conexión entre sí. Hay más de un camino físico entre los dispositivos de esta zona; Rapid PVST+ debe asegurar que no se genere un bucle.

### 4.3. Área Corporativa

![Área Corporativa](img/zona_corporativa.png)

*Figura 5. `SW-Corp`, un 2960 y PC8, PC9, Laptop0 y Laptop1.*

El switch 2960 presenta dos enlaces físicos hacia el switch principal en la representación. La duplicación puede aprovecharse para redundancia o mayor ancho de banda si se configura EtherChannel compatible, o mantenerse bajo control de STP. La zona es considerada de mayor criticidad por su función de oficinas.

### 4.4. Paseo Cayalá

![Paseo Cayalá](img/zona_paseo.png)

*Figura 6. `SW-Paseo`, dos switches de acceso y PC12–PC14 más Laptop4.*

El sector utiliza una estructura interna similar a Plaza y enlaces interzona redundantes. La prueba CLI demuestra que los puertos `Gig0/1` y `Gig0/2` participan correctamente en un Port-channel LACP.

### 4.5. Cardales de Cayalá

![Cardales de Cayalá](img/zona_cardales.png)

*Figura 7. `SW-Cardales` y PC4–PC7, conectados directamente.*

Cardales usa una estrella de acceso simplificada. La vista muestra un solo enlace hacia Plaza; eso reduce complejidad, pero implica dependencia de esa conexión para comunicarse con los demás sectores.

## 5. Segmentación y direccionamiento

### 5.1. Tabla de VLANs realmente observadas

El último dígito del carnet **202400388** es **8**. Los resultados `show vlan brief` confirman los siguientes identificadores y nombres, que sustituyen la correspondencia provisional de versiones anteriores de este manual:

| Zona | Área | VLAN | Nombre visible en CLI | Hosts exigidos |
|---:|---|---:|---|---:|
| 1 | Plaza Cayalá | **18** | `Z1_PLAZA` | 60 |
| 2 | Lirios de Cayalá | **28** | `Z2_LIRIOS` | 28 |
| 3 | Área Corporativa | **38** | `Z3_CORPORATIVA` | 12 |
| 4 | Paseo Cayalá | **48** | `Z4_PASEO` | 50 |
| 5 | Cardales de Cayalá | **58** | `Z5_CARDALES` | 7 |
| — | Nativa / administración | **99** | `ADMIN_NATIVA` | — |
| — | Puertos sin uso | **999** | `BLACKHOLE` | — |

Las capturas muestran estas VLANs definidas y activas. **No prueban que 157 terminales estén conectadas ni que todas las interfaces requeridas tengan direcciones IP válidas.**

### 5.2. Observaciones de los switches y sus VLANs

![VLANs y estado VTP en Plaza](img/vlan_vtp_plaza.png)

*Figura 8. `SW-Plaza`: VLAN 18 a 58, VLAN 99 y VLAN 999; modo VTP Server visible.*

![VLANs y estado VTP en Lirios](img/vlan_vtp_lirios.png)

*Figura 9. `SW-Lirios`: VLAN 18 a 58, VLAN 99 y VLAN 999; VTP versión 1, servidor y dominio 202400388.*

![VLANs y estado VTP en Corporativa](img/vlan_vtp_corporativa.png)

*Figura 10. `SW-Corp`: presencia de VLANs y puertos no utilizados asociados a VLAN 999.*

![VLANs y estado VTP en Paseo](img/vlan_vtp_paseo.png)

*Figura 11. `SW-Paseo`: VLANs solicitadas y `show vtp status` con dominio 202400388.*

![VLANs y estado VTP en Cardales](img/vlan_vtp_cardales.png)

*Figura 12. `SW-Cardales`: cuatro puertos de acceso Fa0/2–Fa0/5 asignados a VLAN 58.*

### 5.3. Subredes IP: datos vistos y propuesta compatible

En la salida VTP aparece un campo *Local updater ID*, asociado a una interfaz VLAN con una dirección de la red `192.168.0.0/24`. Esa información **no acredita por sí sola la máscara, el default gateway de las PCs ni que la dirección mostrada sea el gateway del sector**. Las cantidades de hosts del enunciado permiten plantear la siguiente tabla de direccionamiento VLSM compatible con los valores visibles:

| VLAN | Hosts exigidos | Local updater ID mostrado | Posible subred VLSM (por verificar) | Máscara propuesta |
|---:|---:|---|---|---|
| 18 — Plaza | 60 | `192.168.0.11` | `192.168.0.0/26` | 255.255.255.192 |
| 28 — Lirios | 28 | `192.168.0.129` | `192.168.0.128/27` | 255.255.255.224 |
| 38 — Corporativa | 12 | `192.168.0.161` | `192.168.0.160/28` | 255.255.255.240 |
| 48 — Paseo | 50 | `192.168.0.65` | `192.168.0.64/26` | 255.255.255.192 |
| 58 — Cardales | 7 | `192.168.0.177` | `192.168.0.176/28` | 255.255.255.240 |

**Atención:** las subredes anteriores son una **deducción técnica compatible**, no una lectura de `show ip interface brief` o de la ventana de configuración IP. Si en el archivo `.pkt` los rangos fueran distintos, prevalecen los valores reales del archivo. La captura del ping identifica una IP de destino `192.168.0.178`, compatible con la propuesta de Cardales, pero sin demostrar por sí sola la máscara.

## 6. VTP

El enunciado exige que el dominio VTP sea el carnet. Los **cinco switches principales** muestran modo `Server`; las capturas de `SW-Lirios`, `SW-Paseo` y `SW-Cardales` muestran expresamente el dominio `202400388`. Se observa una revisión de configuración `42` y VTP versión `1` en las capturas donde estos datos son legibles. Los switches 2960 deben operar como clientes de VTP, pero **no se adjuntó salida `show vtp status` de uno de ellos**.

La verificación solicitada para cada switch se realiza con:

```cisco
show vtp status
show vlan brief
```

La contraseña VTP no puede confirmarse desde las capturas proporcionadas; no se incluye ni se inventa una contraseña en esta documentación.

## 7. Enlaces troncales

![Trunks del switch corporativo](img/troncales_corporativa.png)

*Figura 13. `SW-Corp`: `Fa0/1` a `Fa0/5` están en modo trunk, con encapsulación 802.1Q y VLAN nativa 99.*

La evidencia muestra los siguientes conjuntos permitidos:

| Trunk observado en `SW-Corp` | VLANs permitidas mostradas |
|---|---|
| `Fa0/1`, `Fa0/2`, `Fa0/3` | `1,99` |
| `Fa0/4`, `Fa0/5` | `1,38,99` |

Esto confirma **listas explícitas de VLANs permitidas**, no `all`, y una **VLAN nativa 99** en los cinco puertos mostrados. Sin embargo, conviene revisar si VLAN 1 necesita permanecer permitida y si los trunks interzona requieren transportar VLANs adicionales para mantener el diseño de conectividad. No se puede deducir solo de esta captura que todos los trunks de toda la red estén correctos.

En el switch de acceso `PlazaA2` también se ve en `running-config` que `Fa0/1` y `Fa0/2` tienen VLAN nativa 99 y VLANs permitidas `1,18,99`.

## 8. Rapid PVST+

![Configuración del switch PlazaA2](img/configuracion_plaza_a2.png)

*Figura 14. Fragmento de `show running-config` de `PlazaA2` donde aparece `spanning-tree mode rapid-pvst`, trunks y puertos Blackhole administrativamente apagados.*

La captura acredita Rapid PVST+ **en `PlazaA2`** y asignaciones de puertos de acceso sin uso a la VLAN 999 con `shutdown`. Para comprobar que la implementación alcanza toda la red, faltan salidas de `show spanning-tree` de los switches principales y la evidencia de que cada raíz está ubicada en su zona.

| VLAN | Switch que debería ser root bridge según el enunciado | Evidencia de prioridad/root |
|---:|---|---|
| 18 | `SW-Plaza` | Pendiente |
| 28 | `SW-Lirios` | Pendiente |
| 38 | `SW-Corp` | Pendiente |
| 48 | `SW-Paseo` | Pendiente |
| 58 | `SW-Cardales` | Pendiente |

Una prioridad como `4096` es un ejemplo válido de configuración explícita, **pero las capturas no demuestran que esa prioridad esté aplicada**. Para verificar:

```cisco
show spanning-tree
show spanning-tree vlan 18
show spanning-tree vlan 28
show spanning-tree vlan 38
show spanning-tree vlan 48
show spanning-tree vlan 58
```

No deben aplicarse automáticamente esos cinco comandos de VLAN a todos los switches si no existe la VLAN correspondiente o no transportan tráfico de ella; cada salida debe interpretarse en su contexto.

## 9. EtherChannel LACP

![Resumen LACP en SW-Paseo](img/etherchannel_lacp_paseo.png)

*Figura 15. `SW-Paseo`: `Po1(SU)` con protocolo `LACP`, miembros `Gig0/1(P)` y `Gig0/2(P)`.*

Este resultado demuestra que el **Port-channel 1 está operativo como EtherChannel de capa 2** (`S`), en uso (`U`) y con ambos enlaces agrupados (`P`). También concuerda con el requisito LACP asociado al carnet terminado en 8. La captura no acredita la configuración de Port-channel en otros sectores ni la prueba de desconexión de un miembro; esas verificaciones siguen abiertas.

```cisco
show etherchannel summary
show interfaces port-channel 1 switchport
```

## 10. Pruebas de conectividad

### 10.1. Resultado exitoso desde PC5

![Ping correcto desde PC5](img/ping_pc5_exitoso.png)

*Figura 16. `PC5` hacia `192.168.0.178`: cuatro respuestas recibidas, cero paquetes perdidos y TTL 128.*

Resultado observado: **4 enviados, 4 recibidos, 0 perdidos (0 % pérdida)**. La prueba demuestra conectividad IP para este par origen-destino en el momento mostrado. No es suficiente, por sí sola, para validar las cinco VLANs.

### 10.2. Resultado parcial desde PC3

![Ping parcial desde PC3](img/ping_pc3_parcial.png)

*Figura 17. `PC3` hacia `192.168.0.178`: dos tiempos de espera y dos respuestas exitosas; TTL 126 en las respuestas.*

Resultado observado: **4 enviados, 2 recibidos, 2 perdidos (50 % pérdida)**. Los primeros intentos pueden verse afectados por procesos de descubrimiento ARP o convergencia, pero estas capturas **no prueban cuál fue la causa**. Se recomienda repetir el ping y registrar el porcentaje de pérdida posterior, así como comprobar la configuración IP y el camino utilizado.

El TTL mostrado es compatible con un recorrido enrutado; no basta para demostrar qué equipos concretos realizaron el encaminamiento. El enunciado también requiere al menos diez pings exitosos entre hosts dentro de las zonas; solo se dispone de estas dos ejecuciones de `ping`.

### 10.3. Enrutamiento entre VLANs

Para validar rutas e interfaces VLAN todavía son necesarias capturas de estos comandos en los switches de capa 3 que realmente enrutan el tráfico:

```cisco
show ip interface brief
show ip route
show running-config
```

Y en equipos finales:

```text
ipconfig
ping <IP_de_otro_host>
```

La presencia de `Local updater ID` en VTP no reemplaza esas pruebas.

## 11. Dominios de colisión y broadcast

Cada conexión Ethernet **punto a punto conmutada** delimita un segmento físico independiente. Se puede contar de manera aproximada un dominio de colisión por enlace, aunque en Ethernet full dúplex moderno no se producen colisiones CSMA/CD en la práctica.

| Segmento visible | Cantidad estimada |
|---|---:|
| Hosts ↔ switches | 20 |
| Enlaces locales entre switches dentro de zona | 11 |
| Enlaces entre principales de zonas | 7 |
| **Total de enlaces físicos / segmentos estimados** | **38** |

La cifra **38** es un recuento basado en las conexiones visibles, no la salida de una herramienta de inventario. El Port-channel de dos miembros se cuenta como **dos enlaces físicos**, aunque se gestione lógicamente como una interfaz agregada.

Las cinco VLANs de usuarios definen al menos **cinco dominios de broadcast**; la VLAN 99 constituye un dominio adicional allí donde esté configurada y transportada. La VLAN 999 se utiliza para aislar puertos inactivos y no presta servicios a hosts. Los switches separan segmentos físicos; los límites entre VLANs separan tráfico de difusión.

## 12. Comandos de comprobación para calificación

En los switches de cada zona:

```cisco
show vlan brief
show vtp status
show interfaces trunk
show spanning-tree
show etherchannel summary
show running-config
```

En el equipo multicapa con interfaces VLAN y enrutamiento configurados:

```cisco
show ip interface brief
show ip route
```

Para comprobar un root bridge concreto: `show spanning-tree vlan 18` (modificando el número según la zona).

## 13. Estado de la evidencia y aspectos pendientes

| Requisito | Estado respaldado por capturas |
|---|---|
| Cinco áreas y topología híbrida | **Visible** en topología general y detalles |
| VLAN 18/28/38/48/58, VLAN 99/999 | **Confirmado** por `show vlan brief` |
| VTP dominio 202400388 en principales | **Confirmado** en capturas de principales |
| Switches de acceso como clientes VTP | **Pendiente de captura** |
| Rapid PVST+ | **Confirmado en PlazaA2**; no se ha verificado en todos |
| Root bridge de cada VLAN y prioridades | **Pendiente de captura** |
| LACP en EtherChannel de Paseo | **Confirmado**: `Po1(SU)`, dos miembros `P` |
| Troncales 802.1Q con VLAN nativa 99 | **Confirmado en SW-Corp** y fragmento de `PlazaA2` |
| IP, máscara y gateway en todos los equipos | **Pendiente** |
| Al menos 10 pings exitosos intra-VLAN | **No acreditado con las dos capturas actuales** |
| Ping PC5 → 192.168.0.178 | **Exitoso (0 % pérdida)** |
| Ping PC3 → 192.168.0.178 | **Parcial (50 % pérdida)** |
| `show ip route` y rutas interzona | **Pendiente de captura** |
| 157 hosts físicos o requerimiento de capacidad | **20 hosts visibles; falta aclarar cumplimiento** |
| Scripts CLI completos de todos los switches | **No adjuntados a estos documentos** |

**Alcance de este manual:** documenta de forma completa la **evidencia recibida**, pero no inventa configuraciones no visibles ni sustituye el archivo `.pkt` o el respaldo de comandos CLI requerido para entregar la práctica.

## 14. Fuentes

- Universidad de San Carlos de Guatemala, Facultad de Ingeniería, *Redes de Computadoras 1: Práctica 2 — Red de la Ciudad Comercial Cayalá*, 2.º semestre de 2026.
- Capturas suministradas de Cisco Packet Tracer: seis vistas de topología y diez evidencias adicionales de configuración y conectividad.
- Plano de referencia proporcionado con las capturas adicionales.
- [Sitio oficial de Ciudad Cayalá](https://cayala.com/) (referencia general; no utilizado para comprobar el cableado).
