# Manual Técnico — Configuración de VLAN, VTP y enlaces Trunk en Packet Tracer

## 1. Objetivo

El objetivo de esta práctica es implementar una red con múltiples switches utilizando **VTP versión 2**, crear y distribuir VLAN, configurar puertos de acceso para los equipos finales y establecer enlaces troncales entre switches.

Al finalizar la configuración se debe comprobar que:

- Los equipos que pertenecen a la **misma VLAN** pueden comunicarse mediante `ping`.
- Los equipos que pertenecen a **VLAN distintas** no pueden comunicarse entre sí, debido a que en la topología no existe un router ni un switch capa 3 realizando **enrutamiento inter-VLAN**.
- El switch principal funciona como **VTP Server**.
- Los switches Cliente 1 y Cliente 2 funcionan como **VTP Client**.
- El Switch 4 funciona como **VTP Transparent** y contiene de forma local la VLAN 30.

---

## 2. Topología utilizada

![Topología de la práctica](topologia_vlan.png)

La topología está formada por cuatro switches Cisco 2960 y seis computadoras.

| Dispositivo | Función | VLAN utilizadas |
|---|---|---|
| Switch 1 | VTP Server / Switch principal | VLAN 10 y VLAN 20 |
| Switch 2 | VTP Client / Cliente 1 | VLAN 10 y VLAN 20 |
| Switch 3 | VTP Client / Cliente 2 | VLAN 10 y VLAN 20 |
| Switch 4 | VTP Transparent | VLAN 30 |
| PC0 | Usuario ADMIN | VLAN 10 |
| PC1 | Usuario MERCA | VLAN 20 |
| PC2 | Usuario ADMIN | VLAN 10 |
| PC3 | Usuario MERCA | VLAN 20 |
| PC4 | Usuario VENTAS | VLAN 30 |
| PC5 | Usuario VENTAS | VLAN 30 |

### VLAN creadas

| VLAN | Nombre | Uso |
|---:|---|---|
| 10 | ADMIN | Administración |
| 20 | MERCA | Mercadeo |
| 30 | VENTAS | Ventas |

---

## 3. Configuración del Switch Principal — Switch 1

El Switch 1 se configuró como **servidor VTP**. Este dispositivo es el encargado de crear las VLAN 10 y 20 y propagarlas hacia los switches configurados como clientes dentro del mismo dominio VTP.

### 3.1 Configuración VTP

```text
enable
configure terminal
vtp version 2
vtp domain tarea3
vtp password 123
vtp mode server
end
show vtp status
```

### 3.2 Creación de VLAN 10 y VLAN 20

```text
configure terminal
vlan 10
 name ADMIN
exit
vlan 20
 name MERCA
exit
end
show vlan brief
```

### 3.3 Configuración de enlaces Trunk

Los puertos FastEthernet 0/1, 0/2 y 0/3 del Switch 1 se utilizan para conectar los switches secundarios. Estos enlaces se configuran como troncales para transportar las VLAN permitidas.

```text
configure terminal
interface range fa0/1-3
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit
end
show running-config
```

> **Nota:** Con esta configuración, los enlaces troncales del Switch 1 permiten únicamente las VLAN 10 y 20. La VLAN 30 permanece local en el Switch 4, que trabaja en modo VTP Transparent.

---

## 4. Configuración del Switch Cliente 1 — Switch 2

El Switch 2 se configuró en modo **VTP Client**. Al pertenecer al dominio `tarea3` y utilizar la misma contraseña VTP, recibe del servidor la información correspondiente a las VLAN 10 y 20.

### 4.1 Configuración VTP

```text
enable
configure terminal
vtp version 2
vtp domain tarea3
vtp password 123
vtp mode client
end
show vtp status
```

### 4.2 Configuración de puertos de acceso

El puerto `Fa0/2` se asignó a la VLAN 10 para PC0 y el puerto `Fa0/3` se asignó a la VLAN 20 para PC1.

```text
enable
configure terminal
interface fa0/2
 switchport mode access
 switchport access vlan 10
exit
interface fa0/3
 switchport mode access
 switchport access vlan 20
exit
end
show running-config
```

---

## 5. Configuración del Switch Cliente 2 — Switch 3

El Switch 3 también se configuró en modo **VTP Client**, por lo que recibe las VLAN 10 y 20 creadas en el Switch 1.

### 5.1 Configuración VTP

```text
enable
configure terminal
vtp version 2
vtp domain tarea3
vtp password 123
vtp mode client
end
show vtp status
```

### 5.2 Configuración de puertos de acceso

El puerto `Fa0/2` se asignó a la VLAN 10 para PC2 y el puerto `Fa0/3` se asignó a la VLAN 20 para PC3.

```text
enable
configure terminal
interface fa0/2
 switchport mode access
 switchport access vlan 10
exit
interface fa0/3
 switchport mode access
 switchport access vlan 20
exit
end
show running-config
```

---

## 6. Configuración del Switch Transparente — Switch 4

El Switch 4 se configuró en modo **VTP Transparent**. En este modo, el switch no aprende automáticamente las VLAN creadas en el servidor VTP, por lo que la VLAN 30 debe crearse localmente.

### 6.1 Configuración VTP Transparent

```text
enable
configure terminal
vtp version 2
vtp domain tarea3
vtp password 123
vtp mode transparent
end
show vtp status
```

### 6.2 Creación local de la VLAN 30

```text
configure terminal
vlan 30
 name VENTAS
exit
end
show vlan brief
```

### 6.3 Configuración de puertos para usuarios de VENTAS

Los puertos `Fa0/2` y `Fa0/3` se configuraron como puertos de acceso pertenecientes a la VLAN 30. En ellos se encuentran conectadas PC4 y PC5.

```text
enable
configure terminal
interface range fa0/2-3
 switchport mode access
 switchport access vlan 30
exit
end
show running-config
```

---

## 7. Verificación de la configuración

Para comprobar que la configuración se aplicó correctamente se utilizaron los comandos `show vtp status`, `show vlan brief` y `show running-config`.

### 7.1 Verificación con `show vtp status`

En el Switch 1 debe observarse que el modo de operación VTP es **Server**.

```text
show vtp status
```

Datos importantes a comprobar:

- VTP Version: 2.
- VTP Domain Name: `tarea3`.
- VTP Operating Mode: `Server` en Switch 1.
- VTP Operating Mode: `Client` en Switch 2 y Switch 3.
- VTP Operating Mode: `Transparent` en Switch 4.

### Evidencia

> Insertar aquí la captura de `show vtp status` del Switch 1.

> Insertar aquí la captura de `show vtp status` del Switch 2.

> Insertar aquí la captura de `show vtp status` del Switch 3.

> Insertar aquí la captura de `show vtp status` del Switch 4.

---

### 7.2 Verificación con `show vlan brief`

En el Switch 1, Switch 2 y Switch 3 deben aparecer las VLAN:

- VLAN 10 — `ADMIN`.
- VLAN 20 — `MERCA`.

En el Switch 4 debe aparecer la VLAN creada localmente:

- VLAN 30 — `VENTAS`.

```text
show vlan brief
```

También debe verificarse la asignación de puertos:

| Switch | Puerto | VLAN |
|---|---|---:|
| Switch 2 | Fa0/2 | 10 — ADMIN |
| Switch 2 | Fa0/3 | 20 — MERCA |
| Switch 3 | Fa0/2 | 10 — ADMIN |
| Switch 3 | Fa0/3 | 20 — MERCA |
| Switch 4 | Fa0/2 | 30 — VENTAS |
| Switch 4 | Fa0/3 | 30 — VENTAS |

### Evidencia

> Insertar aquí las capturas de `show vlan brief` de los switches.

---

## 8. Pruebas de conectividad mediante Ping

Las pruebas de conectividad permiten comprobar la separación lógica creada por las VLAN.

### 8.1 Ping entre equipos de la misma VLAN

Los siguientes pings deben ser **exitosos**, siempre que las direcciones IP de cada pareja pertenezcan a la misma red IP.

| Origen | Destino | VLAN | Resultado esperado |
|---|---|---:|---|
| PC0 | PC2 | 10 — ADMIN | Exitoso |
| PC1 | PC3 | 20 — MERCA | Exitoso |
| PC4 | PC5 | 30 — VENTAS | Exitoso |

Ejemplo del comando desde una PC:

```text
ping <IP-del-equipo-destino>
```

Un resultado correcto debe mostrar respuestas similares a:

```text
Reply from <IP-destino>: bytes=32 time<1ms TTL=128
```

> **Observación:** El primer ping en Packet Tracer puede perder uno o más paquetes mientras se resuelve ARP. Al repetir el comando, la comunicación debería ser exitosa.

### Evidencia

> Insertar aquí captura del ping PC0 → PC2.

> Insertar aquí captura del ping PC1 → PC3.

> Insertar aquí captura del ping PC4 → PC5.

---

### 8.2 Ping entre equipos de VLAN diferentes

Los pings entre dispositivos de VLAN distintas deben **fallar**, porque únicamente se configuraron switches de capa 2 y no existe un dispositivo encargado de realizar enrutamiento inter-VLAN.

Ejemplos de pruebas:

| Origen | Destino | VLAN origen | VLAN destino | Resultado esperado |
|---|---|---:|---:|---|
| PC0 | PC1 | 10 | 20 | Fallido |
| PC0 | PC3 | 10 | 20 | Fallido |
| PC2 | PC1 | 10 | 20 | Fallido |
| PC4 | PC0 | 30 | 10 | Fallido |
| PC5 | PC3 | 30 | 20 | Fallido |

Un resultado fallido puede mostrar mensajes como:

```text
Request timed out.
```

Esto demuestra que las VLAN crean dominios de broadcast independientes y que, sin un router o switch capa 3, no es posible establecer comunicación directa entre VLAN diferentes.

### Evidencia

> Insertar aquí al menos una captura de un ping fallido entre VLAN 10 y VLAN 20.

> Insertar aquí al menos una captura de un ping fallido entre VLAN 30 y otra VLAN.

---

## 9. Evidencias de las actividades realizadas en Packet Tracer

Para completar la documentación de la práctica se recomienda incluir las siguientes capturas:

1. Topología completa de Packet Tracer.
2. `show vtp status` del Switch 1 mostrando modo **Server**.
3. `show vtp status` del Switch 2 mostrando modo **Client**.
4. `show vtp status` del Switch 3 mostrando modo **Client**.
5. `show vtp status` del Switch 4 mostrando modo **Transparent**.
6. `show vlan brief` del Switch 1 mostrando VLAN 10 y VLAN 20.
7. `show vlan brief` de los clientes mostrando VLAN 10 y VLAN 20 recibidas mediante VTP.
8. `show vlan brief` del Switch 4 mostrando VLAN 30.
9. Evidencia de los puertos de usuario asignados a las VLAN correspondientes.
10. Ping exitoso entre PC0 y PC2, ambos en VLAN 10.
11. Ping exitoso entre PC1 y PC3, ambos en VLAN 20.
12. Ping exitoso entre PC4 y PC5, ambos en VLAN 30.
13. Ping fallido entre equipos pertenecientes a VLAN diferentes.

---

## 10. Resumen de funcionamiento

La configuración implementada separa la red en tres VLAN. El Switch 1 administra las VLAN 10 y 20 mediante VTP en modo Server, mientras que los switches 2 y 3 reciben dichas VLAN como clientes VTP. El Switch 4 funciona en modo Transparent y administra localmente la VLAN 30.

Los enlaces entre switches permiten transportar el tráfico correspondiente a las VLAN configuradas, mientras que los puertos conectados a las computadoras se encuentran en modo access. Como resultado, los equipos que pertenecen a una misma VLAN pueden comunicarse entre sí, pero los equipos de VLAN diferentes permanecen aislados al no existir enrutamiento inter-VLAN.

---

## 11. Conclusión

La práctica permitió comprobar el funcionamiento de **VTP**, las **VLAN**, los puertos **access** y los enlaces **trunk** dentro de una red con switches Cisco. La correcta separación de los equipos por VLAN mejora la organización lógica de la red y limita los dominios de broadcast. Las pruebas de ping confirman que existe conectividad entre hosts de la misma VLAN y aislamiento entre hosts de VLAN distintas mientras no se configure un mecanismo de enrutamiento inter-VLAN.
