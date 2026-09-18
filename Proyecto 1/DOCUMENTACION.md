# Manual Técnico — SmartCity Tech Park

**Curso:** Redes de Computadoras 1

**Proyecto:** Proyecto 1 — SmartCity Tech Park

**Estudiante:** Dorbal Emilio Aldana Ramos

**Carné:** 202400388

**Semestre:** Segundo Semestre 2026

---

## Descripción general

El proyecto implementa en Cisco Packet Tracer una red LAN jerárquica para el complejo **SmartCity Tech Park**. La topología se divide en cuatro áreas principales: Centro de Datos, Centro de Investigación y Desarrollo (I+D), Edificio Corporativo y Planta de Producción.

La solución utiliza segmentación mediante VLANs, administración de VLANs con VTP, enlaces troncales entre switches, STP para evitar bucles en las zonas con redundancia y un segmento Legacy basado en Hub para demostrar un dominio de colisión compartido.

> **Observación sobre la versión implementada:** *la topología actual utiliza una sola conexión física FastEthernet entre cada par de switches. Por esta decisión de diseño no se implementó EtherChannel. Asimismo, todos los enlaces FastEthernet tienen la misma capacidad nominal. Por ello, esta versión no reproduce literalmente los requisitos del enunciado relacionados con evitar una única conexión física para la granja de servidores, disponer de mayor capacidad hacia I+D y presentar un EtherChannel activo. Esta observación se incluye para que la documentación corresponda con la topología realmente implementada.*

---

## Topología completa

![Topología completa](docs/01_topologia_completa.png)

La topología parte de un switch central ubicado en el Centro de Datos. Desde este equipo se conectan los switches de distribución correspondientes a I+D, Edificio Corporativo y Planta de Producción, además del switch utilizado para la granja de servidores.

La estructura general es jerárquica. En I+D y en el Edificio Corporativo se agregan enlaces laterales entre switches de acceso para generar rutas redundantes de Capa 2.

---

## Centro de Datos

![Centro de Datos](docs/02_centro_datos.png)

El Centro de Datos contiene el switch **SW-CORE**, que funciona como núcleo de la red y como **VTP Server**. El switch **SW-SERVERS** conecta cuatro servidores críticos.

### Conexiones

| Equipo origen | Puerto | Equipo destino | Puerto destino | Tipo | VLANs |
|---|---|---|---|---|---|
| SW-CORE | Fa0/1 | SW-SERVERS | Fa0/1 | Trunk | 48, 98 |
| SW-SERVERS | Fa0/2 | SERVER-1 | Fa0 | Access | 48 |
| SW-SERVERS | Fa0/3 | SERVER-2 | Fa0 | Access | 48 |
| SW-SERVERS | Fa0/4 | SERVER-3 | Fa0 | Access | 48 |
| SW-SERVERS | Fa0/5 | SERVER-4 | Fa0 | Access | 48 |

### Direccionamiento

| Dispositivo | VLAN | Dirección IP | Máscara |
|---|---:|---|---|
| SERVER-1 | 48 | 192.168.48.10 | 255.255.255.0 |
| SERVER-2 | 48 | 192.168.48.11 | 255.255.255.0 |
| SERVER-3 | 48 | 192.168.48.12 | 255.255.255.0 |
| SERVER-4 | 48 | 192.168.48.13 | 255.255.255.0 |

---

## Centro de Investigación y Desarrollo (I+D)

![Centro de I+D](docs/03_id.png)

El área de I+D utiliza tres switches interconectados: **SW-ID-DIST**, **SW-ID-1** y **SW-ID-2**. Los dos switches de acceso se encuentran enlazados entre sí, además de su conexión hacia el switch de distribución, formando una malla completa de tres switches y proporcionando una ruta redundante de Capa 2.

Se conectan ocho estaciones de trabajo, distribuidas en cuatro equipos por switch de acceso.

### Conexiones entre switches

| Equipo origen | Puerto | Equipo destino | Puerto destino | Tipo | VLANs |
|---|---|---|---|---|---|
| SW-CORE | Fa0/2 | SW-ID-DIST | Fa0/1 | Trunk | 28, 98 |
| SW-ID-DIST | Fa0/2 | SW-ID-1 | Fa0/1 | Trunk | 28, 98 |
| SW-ID-DIST | Fa0/3 | SW-ID-2 | Fa0/1 | Trunk | 28, 98 |
| SW-ID-1 | Fa0/2 | SW-ID-2 | Fa0/2 | Trunk | 28, 98 |

### Estaciones conectadas a SW-ID-1

| Puerto | Dispositivo | VLAN | IP | Máscara |
|---|---|---:|---|---|
| Fa0/3 | PC-ID-1 | 28 | 192.168.28.10 | 255.255.255.0 |
| Fa0/4 | PC-ID-2 | 28 | 192.168.28.11 | 255.255.255.0 |
| Fa0/5 | PC-ID-3 | 28 | 192.168.28.12 | 255.255.255.0 |
| Fa0/6 | PC-ID-4 | 28 | 192.168.28.13 | 255.255.255.0 |

### Estaciones conectadas a SW-ID-2

| Puerto | Dispositivo | VLAN | IP | Máscara |
|---|---|---:|---|---|
| Fa0/3 | PC-ID-5 | 28 | 192.168.28.14 | 255.255.255.0 |
| Fa0/4 | PC-ID-6 | 28 | 192.168.28.15 | 255.255.255.0 |
| Fa0/5 | PC-ID-7 | 28 | 192.168.28.16 | 255.255.255.0 |
| Fa0/6 | PC-ID-8 | 28 | 192.168.28.17 | 255.255.255.0 |

---

## Edificio Corporativo

![Edificio Corporativo](docs/04_corporativo.png)

El Edificio Corporativo posee un switch de distribución, dos switches de acceso para las dos alas y un switch independiente para la red de visitantes.

Los switches **SW-CORP-1** y **SW-CORP-2** se encuentran conectados entre sí, además de estar enlazados con **SW-CORP-DIST**, formando una malla de tres switches para proporcionar redundancia de Capa 2.

El segmento de visitantes se conecta a **SW-GUEST**, el cual trabaja en modo VTP Transparent. Desde este switch se conecta un Access Point con SSID **VISITANTES**, al que se asocian dos laptops de invitados.

### Conexiones

| Equipo origen | Puerto | Equipo destino | Puerto destino | Tipo | VLANs |
|---|---|---|---|---|---|
| SW-CORE | Fa0/3 | SW-CORP-DIST | Fa0/1 | Trunk | 18, 58, 98 |
| SW-CORP-DIST | Fa0/2 | SW-CORP-1 | Fa0/1 | Trunk | 18, 98 |
| SW-CORP-DIST | Fa0/3 | SW-CORP-2 | Fa0/1 | Trunk | 18, 98 |
| SW-CORP-DIST | Fa0/4 | SW-GUEST | Fa0/1 | Trunk | 58, 98 |
| SW-CORP-1 | Fa0/2 | SW-CORP-2 | Fa0/2 | Trunk | 18, 98 |
| SW-CORP-1 | Fa0/3 | PC-CORP-1 | Fa0 | Access | 18 |
| SW-CORP-2 | Fa0/3 | PC-CORP-2 | Fa0 | Access | 18 |
| SW-GUEST | Fa0/2 | Access Point | Port 0 | Access | 58 |

### Direccionamiento

| Dispositivo | VLAN | Dirección IP | Máscara |
|---|---:|---|---|
| PC-CORP-1 | 18 | 192.168.18.10 | 255.255.255.0 |
| PC-CORP-2 | 18 | 192.168.18.11 | 255.255.255.0 |
| Access Point | 58 | 192.168.58.2 | 255.255.255.0 |
| LAPTOP-GUEST-1 | 58 | 192.168.58.10 | 255.255.255.0 |
| LAPTOP-GUEST-2 | 58 | 192.168.58.11 | 255.255.255.0 |

### Configuración inalámbrica

| Parámetro | Valor |
|---|---|
| SSID | VISITANTES |
| Banda | 2.4 GHz |
| Canal | 6 |
| Seguridad | Deshabilitada para la práctica |
| Switch asociado | SW-GUEST |
| Puerto del switch | Fa0/2 |
| VLAN | 58 |

---

## Planta de Producción

![Planta de Producción](docs/05_produccion.png)

La Planta de Producción incorpora un segmento Legacy mediante un **Hub-PT**. El Hub se conecta al switch **SW-PROD-ACC**, mientras que los equipos Legacy se conectan directamente al Hub. Esto conserva un dominio de colisión compartido en Capa 1.

En la topología implementada se utilizaron cuatro equipos Legacy.

### Conexiones

| Equipo origen | Puerto | Equipo destino | Puerto destino | Tipo | VLAN |
|---|---|---|---|---|---:|
| SW-CORE | Fa0/4 | SW-PROD-DIST | Fa0/1 | Trunk | 38, 98 |
| SW-PROD-DIST | Fa0/2 | SW-PROD-ACC | Fa0/1 | Trunk | 38, 98 |
| SW-PROD-ACC | Fa0/2 | HUB-PROD | Fa0 | Access | 38 |
| HUB-PROD | Fa1 | LEGACY-1 | Fa0 | Ethernet | 38 |
| HUB-PROD | Fa2 | LEGACY-2 | Fa0 | Ethernet | 38 |
| HUB-PROD | Fa3 | LEGACY-3 | Fa0 | Ethernet | 38 |
| HUB-PROD | Fa4 | LEGACY-4 | Fa0 | Ethernet | 38 |

### Direccionamiento

| Equipo | VLAN | IP | Máscara |
|---|---:|---|---|
| LEGACY-1 | 38 | 192.168.38.10 | 255.255.255.0 |
| LEGACY-2 | 38 | 192.168.38.11 | 255.255.255.0 |
| LEGACY-3 | 38 | 192.168.38.12 | 255.255.255.0 |
| LEGACY-4 | 38 | 192.168.38.13 | 255.255.255.0 |

### Impacto del dominio de colisión Legacy

El Hub opera en Capa 1 y no separa dominios de colisión. Todos los equipos conectados al Hub comparten el mismo medio lógico, por lo que una transmisión ocupa el segmento compartido y una colisión afecta a los demás dispositivos del Hub.

El impacto queda parcialmente contenido porque el Hub se conecta únicamente al puerto Fa0/2 de **SW-PROD-ACC**. El switch separa este segmento Legacy del resto de la infraestructura conmutada, evitando que el dominio de colisión se extienda a otras áreas.

---

## Tabla de VLANs

El último dígito del carné 202400388 es 8, por lo que X = 8.

| VLAN ID | Nombre | Ubicación |
|---:|---|---|
| 18 | GERENCIA | Edificio Corporativo |
| 28 | INVESTIGACION | Centro de I+D |
| 38 | PRODUCCION | Planta de Producción |
| 48 | SERVIDORES | Centro de Datos |
| 58 | VISITANTES | Edificio Corporativo |
| 98 | NATIVA | Enlaces troncales |

---

## Configuración VTP

El penúltimo dígito del carné es 8, por lo que el dominio utilizado es **Smart_8**.

| Parámetro | Valor |
|---|---|
| Dominio | Smart_8 |
| Contraseña | proyecto12S2026 |
| Versión | VTP 2 |

### Modos por switch

| Switch | Modo VTP |
|---|---|
| SW-CORE | Server |
| SW-SERVERS | Client |
| SW-ID-DIST | Client |
| SW-ID-1 | Client |
| SW-ID-2 | Client |
| SW-CORP-DIST | Client |
| SW-CORP-1 | Client |
| SW-CORP-2 | Client |
| SW-GUEST | Transparent |
| SW-PROD-DIST | Client |
| SW-PROD-ACC | Client |

### Justificación del VTP Server

**Switch principal** se utiliza como VTP Server porque se encuentra en el núcleo de la red y conecta las diferentes áreas del campus. Las VLANs se crean de forma centralizada en este equipo y son propagadas por los enlaces troncales hacia los switches configurados como VTP Client.

**Switch de visitantes** se mantiene en modo Transparent con el objetivo de administrar localmente las VLANs necesarias para el segmento de visitantes.

### Captura de la configuración VTP del switch servidor

![Configuración VTP de SW-CORE](docs/17_vtp_sw_core.png)

---

## Tabla de asignación de puertos por switch

| Switch | Puerto | Destino | Modo | VLAN(s) |
|---|---|---|---|---|
| SW-CORE | Fa0/1 | SW-SERVERS | Trunk | 48,98 |
| SW-CORE | Fa0/2 | SW-ID-DIST | Trunk | 28,98 |
| SW-CORE | Fa0/3 | SW-CORP-DIST | Trunk | 18,58,98 |
| SW-CORE | Fa0/4 | SW-PROD-DIST | Trunk | 38,98 |
| SW-SERVERS | Fa0/1 | SW-CORE | Trunk | 48,98 |
| SW-SERVERS | Fa0/2-5 | SERVER-1 a SERVER-4 | Access | 48 |
| SW-ID-DIST | Fa0/1 | SW-CORE | Trunk | 28,98 |
| SW-ID-DIST | Fa0/2 | SW-ID-1 | Trunk | 28,98 |
| SW-ID-DIST | Fa0/3 | SW-ID-2 | Trunk | 28,98 |
| SW-ID-1 | Fa0/1 | SW-ID-DIST | Trunk | 28,98 |
| SW-ID-1 | Fa0/2 | SW-ID-2 | Trunk | 28,98 |
| SW-ID-1 | Fa0/3-6 | PC-ID-1 a PC-ID-4 | Access | 28 |
| SW-ID-2 | Fa0/1 | SW-ID-DIST | Trunk | 28,98 |
| SW-ID-2 | Fa0/2 | SW-ID-1 | Trunk | 28,98 |
| SW-ID-2 | Fa0/3-6 | PC-ID-5 a PC-ID-8 | Access | 28 |
| SW-CORP-DIST | Fa0/1 | SW-CORE | Trunk | 18,58,98 |
| SW-CORP-DIST | Fa0/2 | SW-CORP-1 | Trunk | 18,98 |
| SW-CORP-DIST | Fa0/3 | SW-CORP-2 | Trunk | 18,98 |
| SW-CORP-DIST | Fa0/4 | SW-GUEST | Trunk | 58,98 |
| SW-CORP-1 | Fa0/1 | SW-CORP-DIST | Trunk | 18,98 |
| SW-CORP-1 | Fa0/2 | SW-CORP-2 | Trunk | 18,98 |
| SW-CORP-1 | Fa0/3 | PC-CORP-1 | Access | 18 |
| SW-CORP-2 | Fa0/1 | SW-CORP-DIST | Trunk | 18,98 |
| SW-CORP-2 | Fa0/2 | SW-CORP-1 | Trunk | 18,98 |
| SW-CORP-2 | Fa0/3 | PC-CORP-2 | Access | 18 |
| SW-GUEST | Fa0/1 | SW-CORP-DIST | Trunk | 58,98 |
| SW-GUEST | Fa0/2 | Access Point | Access | 58 |
| SW-PROD-DIST | Fa0/1 | SW-CORE | Trunk | 38,98 |
| SW-PROD-DIST | Fa0/2 | SW-PROD-ACC | Trunk | 38,98 |
| SW-PROD-ACC | Fa0/1 | SW-PROD-DIST | Trunk | 38,98 |
| SW-PROD-ACC | Fa0/2 | HUB-PROD | Access | 38 |

---

## Dominios de broadcast

Cada VLAN activa constituye un dominio de broadcast independiente.

| VLAN | Nombre | Dominio de broadcast |
|---:|---|---|
| 18 | GERENCIA | 1 |
| 28 | INVESTIGACION | 1 |
| 38 | PRODUCCION | 1 |
| 48 | SERVIDORES | 1 |
| 58 | VISITANTES | 1 |
| 98 | NATIVA | VLAN utilizada en enlaces troncales, sin hosts finales |

Los equipos pertenecientes a VLANs diferentes permanecen aislados porque en la topología actual no se implementa enrutamiento inter-VLAN.

---

## Dominios de colisión

Cada puerto activo de un switch representa un segmento conmutado independiente. Un enlace físico entre dos switches es un único segmento, aunque aparece como puerto activo en ambos extremos.

| Switch | Puertos activos | Cantidad de puertos activos |
|---|---|---:|
| SW-CORE | Fa0/1-4 | 4 |
| SW-SERVERS | Fa0/1-5 | 5 |
| SW-ID-DIST | Fa0/1-3 | 3 |
| SW-ID-1 | Fa0/1-6 | 6 |
| SW-ID-2 | Fa0/1-6 | 6 |
| SW-CORP-DIST | Fa0/1-4 | 4 |
| SW-CORP-1 | Fa0/1-3 | 3 |
| SW-CORP-2 | Fa0/1-3 | 3 |
| SW-GUEST | Fa0/1-2 | 2 |
| SW-PROD-DIST | Fa0/1-2 | 2 |
| SW-PROD-ACC | Fa0/1-2 | 2 |

### Dominio compartido del segmento Legacy

Los cuatro equipos Legacy y la conexión hacia SW-PROD-ACC comparten **un solo dominio de colisión** debido al uso del Hub. El Hub no crea un dominio por puerto.

---

## STP y redundancia

En I+D existe una topología triangular entre SW-ID-DIST, SW-ID-1 y SW-ID-2. En el Edificio Corporativo existe otra topología triangular entre SW-CORP-DIST, SW-CORP-1 y SW-CORP-2.

Estas conexiones generan caminos redundantes de Capa 2. STP evita que dichos caminos generen bucles, manteniendo un camino alternativo disponible en caso de falla.

La configuración actual no fuerza manualmente un Root Bridge. Por ello, el comportamiento de STP se documenta mediante las capturas obtenidas en Packet Tracer para cada VLAN.

### 13.1 Capturas de STP por VLAN

#### VLAN 18 - GERENCIA

![STP VLAN 18](docs/18_stp_vlan_18.png)

#### VLAN 28 - INVESTIGACION

![STP VLAN 28](docs/19_stp_vlan_28.png)

#### VLAN 38 - PRODUCCION

![STP VLAN 38](docs/20_stp_vlan_38.png)

#### VLAN 48 - SERVIDORES

![STP VLAN 48](docs/21_stp_vlan_48.png)

#### VLAN 58 - VISITANTES

![STP VLAN 58](docs/22_stp_vlan_58.png)

---

## EtherChannel

En la versión actual de la topología **no se configuró EtherChannel**, debido a que se decidió utilizar una sola conexión física FastEthernet entre cada par de switches.

Por ello, el comando:

```text
show etherchannel summary
```

puede ejecutarse, pero no mostrará un Port-Channel activo.

*> Si se requiere cumplir literalmente con la evidencia de EtherChannel indicada en el enunciado, será necesario modificar la topología y agregar al menos dos enlaces físicos entre los switches seleccionados, configurándolos mediante LACP, ya que el carné es par.*

---

## Medios de transmisión utilizados

| Segmento | Medio | Tipo de cable / conexión | Justificación |
|---|---|---|---|
| Switch ↔ Switch | Cobre UTP | Copper Cross-Over | Interconexión FastEthernet entre dispositivos de conmutación |
| Switch ↔ PC/Servidor | Cobre UTP | Copper Straight-Through | Conexión de equipos finales a puertos de acceso |
| Switch ↔ Access Point | Cobre UTP | Copper Straight-Through | Enlace Ethernet hacia la red inalámbrica |
| Access Point ↔ Laptops | Inalámbrico | IEEE 802.11 / 2.4 GHz | Servicio inalámbrico para visitantes |
| Switch ↔ Hub | Cobre UTP | Copper Cross-Over | Interconexión del switch de acceso con el segmento Legacy |
| Hub ↔ equipos Legacy | Cobre UTP | Copper Straight-Through | Conexión de hosts al medio compartido |

No se utilizaron módulos de fibra ni enlaces de fibra óptica en la implementación mostrada.

---

## Configuración de switches

### SW-CORE

```cisco
enable
configure terminal
hostname SW-CORE
vtp version 2
vtp domain Smart_8
vtp password proyecto12S2026
vtp mode server
vlan 18
 name GERENCIA
exit
vlan 28
 name INVESTIGACION
exit
vlan 38
 name PRODUCCION
exit
vlan 48
 name SERVIDORES
exit
vlan 58
 name VISITANTES
exit
vlan 98
 name NATIVA
exit
interface fa0/1
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 48,98
 no shutdown
exit
interface fa0/2
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 28,98
 no shutdown
exit
interface fa0/3
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 18,58,98
 no shutdown
exit
interface fa0/4
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 38,98
 no shutdown
exit
end
write memory
```

### SW-SERVERS

```cisco
enable
configure terminal
hostname SW-SERVERS
vtp version 2
vtp domain Smart_8
vtp password proyecto12S2026
vtp mode client
interface fa0/1
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 48,98
 no shutdown
exit
interface range fa0/2-5
 switchport mode access
 switchport access vlan 48
 no shutdown
exit
end
write memory
```

### SW-ID-DIST

```cisco
enable
configure terminal
hostname SW-ID-DIST
vtp version 2
vtp domain Smart_8
vtp password proyecto12S2026
vtp mode client
banner motd #Acceso Restringido - TechPark_202400388#
interface fa0/1
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 28,98
 no shutdown
exit
interface fa0/2
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 28,98
 no shutdown
exit
interface fa0/3
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 28,98
 no shutdown
exit
end
write memory
```

### SW-ID-1

```cisco
enable
configure terminal
hostname SW-ID-1
vtp version 2
vtp domain Smart_8
vtp password proyecto12S2026
vtp mode client
interface fa0/1
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 28,98
 no shutdown
exit
interface fa0/2
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 28,98
 no shutdown
exit
interface range fa0/3-6
 switchport mode access
 switchport access vlan 28
 no shutdown
exit
end
write memory
```

### SW-ID-2

```cisco
enable
configure terminal
hostname SW-ID-2
vtp version 2
vtp domain Smart_8
vtp password proyecto12S2026
vtp mode client
interface fa0/1
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 28,98
 no shutdown
exit
interface fa0/2
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 28,98
 no shutdown
exit
interface range fa0/3-6
 switchport mode access
 switchport access vlan 28
 no shutdown
exit
end
write memory
```

### SW-CORP-DIST

```cisco
enable
configure terminal
hostname SW-CORP-DIST
vtp version 2
vtp domain Smart_8
vtp password proyecto12S2026
vtp mode client
banner motd #Acceso Restringido - TechPark_202400388#
interface fa0/1
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 18,58,98
 no shutdown
exit
interface fa0/2
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 18,98
 no shutdown
exit
interface fa0/3
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 18,98
 no shutdown
exit
interface fa0/4
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 58,98
 no shutdown
exit
end
write memory
```

### SW-CORP-1

```cisco
enable
configure terminal
hostname SW-CORP-1
vtp version 2
vtp domain Smart_8
vtp password proyecto12S2026
vtp mode client
interface fa0/1
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 18,98
 no shutdown
exit
interface fa0/2
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 18,98
 no shutdown
exit
interface fa0/3
 switchport mode access
 switchport access vlan 18
 no shutdown
exit
end
write memory
```

### SW-CORP-2

```cisco
enable
configure terminal
hostname SW-CORP-2
vtp version 2
vtp domain Smart_8
vtp password proyecto12S2026
vtp mode client
interface fa0/1
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 18,98
 no shutdown
exit
interface fa0/2
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 18,98
 no shutdown
exit
interface fa0/3
 switchport mode access
 switchport access vlan 18
 no shutdown
exit
end
write memory
```

### SW-GUEST

```cisco
enable
configure terminal
hostname SW-GUEST
vtp version 2
vtp domain Smart_8
vtp password proyecto12S2026
vtp mode transparent
vlan 58
 name VISITANTES
exit
vlan 98
 name NATIVA
exit
interface fa0/1
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 58,98
 no shutdown
exit
interface fa0/2
 switchport mode access
 switchport access vlan 58
 no shutdown
exit
end
write memory
```

### SW-PROD-DIST

```cisco
enable
configure terminal
hostname SW-PROD-DIST
vtp version 2
vtp domain Smart_8
vtp password proyecto12S2026
vtp mode client
banner motd #Acceso Restringido - TechPark_202400388#
interface fa0/1
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 38,98
 no shutdown
exit
interface fa0/2
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 38,98
 no shutdown
exit
end
write memory
```

### SW-PROD-ACC

```cisco
enable
configure terminal
hostname SW-PROD-ACC
vtp version 2
vtp domain Smart_8
vtp password proyecto12S2026
vtp mode client
interface fa0/1
 switchport mode trunk
 switchport trunk native vlan 98
 switchport trunk allowed vlan 38,98
 no shutdown
exit
interface fa0/2
 switchport mode access
 switchport access vlan 38
 no shutdown
exit
end
write memory
```

---

## Pruebas de conectividad

### I+D

Ejemplo:

```text
PC-ID-1 (192.168.28.10) -> PC-ID-8 (192.168.28.17)
```

El ping debe funcionar debido a que ambos dispositivos pertenecen a VLAN 28.

### Gerencia

```text
PC-CORP-1 (192.168.18.10) -> PC-CORP-2 (192.168.18.11)
```

El ping debe funcionar al pertenecer ambos a VLAN 18.

### Visitantes

```text
LAPTOP-GUEST-1 (192.168.58.10) -> LAPTOP-GUEST-2 (192.168.58.11)
```

Debe existir conectividad a través del Access Point y la VLAN 58.

### Producción

```text
LEGACY-1 (192.168.38.10) -> LEGACY-4 (192.168.38.13)
```

Debe existir conectividad dentro de VLAN 38 a través del Hub.

### Aislamiento inter-VLAN

Un equipo de VLAN 28 no debe poder comunicarse directamente con un servidor de VLAN 48 porque no existe enrutamiento inter-VLAN.

---

## Presupuesto estimado

Los valores siguientes son una estimación académica y no constituyen una cotización comercial.

| Elemento | Cantidad | Precio unitario estimado | Subtotal |
|---|---:|---:|---:|
| Switch de acceso/distribución equivalente | 11 | Q1,250.00 | Q13,750.00 |
| Access Point | 1 | Q450.00 | Q450.00 |
| Hub Ethernet para segmento Legacy | 1 | Q250.00 | Q250.00 |
| Caja UTP Cat6 305 m | 1 | Q900.00 | Q900.00 |
| Conectores/terminaciones RJ45 | 50 | Q2.00 | Q100.00 |
| Módulos de fibra | 0 | Q0.00 | Q0.00 |
| Fibra óptica | 0 m | Q0.00 | Q0.00 |
| **Total estimado** |  |  | **Q15,450.00** |


---

## Capturas de configuración de los switches

En este apartado se presentan las capturas PNG correspondientes a la configuración realizada en cada uno de los switches de la topología.

### SW-CORE

![Configuración de SW-CORE](docs/06_config_sw_core.png)
![Configuración de SW-CORE](docs/06_config_sw_core_1.png)
![Configuración de SW-CORE](docs/06_config_sw_core_2.png)

### SW-SERVERS

![Configuración de SW-SERVERS](docs/07_config_sw_servers.png)
![Configuración de SW-SERVERS](docs/07_config_sw_servers_1.png)
![Configuración de SW-SERVERS](docs/07_config_sw_servers_2.png)


### SW-ID-DIST

![Configuración de SW-ID-DIST](docs/08_config_sw_id_dist.png)
![Configuración de SW-ID-DIST](docs/08_config_sw_id_dist_1.png)
![Configuración de SW-ID-DIST](docs/08_config_sw_id_dist_2.png)

### SW-ID-1

![Configuración de SW-ID-1](docs/09_config_sw_id_1.png)
![Configuración de SW-ID-1](docs/09_config_sw_id_1_1.png)
![Configuración de SW-ID-1](docs/09_config_sw_id_1_2.png)

### SW-ID-2

![Configuración de SW-ID-2](docs/10_config_sw_id_2.png)
![Configuración de SW-ID-2](docs/10_config_sw_id_2_1.png)
![Configuración de SW-ID-2](docs/10_config_sw_id_2_2.png)

### SW-CORP-DIST

![Configuración de SW-CORP-DIST](docs/11_config_sw_corp_dist.png)
![Configuración de SW-CORP-DIST](docs/11_config_sw_corp_dist_1.png)
![Configuración de SW-CORP-DIST](docs/11_config_sw_corp_dist_2.png)

### SW-CORP-1

![Configuración de SW-CORP-1](docs/12_config_sw_corp_1.png)
![Configuración de SW-CORP-1](docs/12_config_sw_corp_1_1.png)
![Configuración de SW-CORP-1](docs/12_config_sw_corp_1_2.png)

### SW-CORP-2

![Configuración de SW-CORP-2](docs/13_config_sw_corp_2.png)
![Configuración de SW-CORP-2](docs/13_config_sw_corp_2_1.png)
![Configuración de SW-CORP-2](docs/13_config_sw_corp_2_2.png)

### SW-GUEST

![Configuración de SW-GUEST](docs/14_config_sw_guest.png)
![Configuración de SW-GUEST](docs/14_config_sw_guest_1.png)
![Configuración de SW-GUEST](docs/14_config_sw_guest_2.png)

### SW-PROD-DIST

![Configuración de SW-PROD-DIST](docs/15_config_sw_prod_dist.png)
![Configuración de SW-PROD-DIST](docs/15_config_sw_prod_dist_1.png)
![Configuración de SW-PROD-DIST](docs/15_config_sw_prod_dist_2.png)

### SW-PROD-ACC

![Configuración de SW-PROD-ACC](docs/16_config_sw_prod_acc.png)
![Configuración de SW-PROD-ACC](docs/16_config_sw_prod_acc_1.png)
![Configuración de SW-PROD-ACC](docs/16_config_sw_prod_acc_2.png)
