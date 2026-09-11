# 🏢 Proyecto de Red Empresarial Multi-Sede (Cisco Packet Tracer)

Infraestructura de red escalable y segura diseñada para una empresa con una sede central y dos sucursales remotas, implementada y documentada en Cisco Packet Tracer. Este proyecto integra enrutamiento dinámico, segmentación de tráfico, alta disponibilidad, seguridad de acceso y conectividad inalámbrica.

---

## 🛠️ Tecnologías y Protocolos Utilizados
* **Dispositivos Cisco:** Routers (Series 1800/1900/2800) y Switches Catalyst (2960).
* **Enrutamiento:** RIPv2 (Routing Information Protocol).
* **Segmentación:** VLANs (IEEE 802.1Q) y troncales (Trunking), Router-on-a-Stick.
* **Alta Disponibilidad:** HSRP (Hot Standby Router Protocol) para redundancia de Gateway en LAN.
* **Seguridad:** Port Security (Sticky MAC) en puertos de acceso y control perimetral.
* **Inalámbrico:** Configuración de Access Point (Linksys WRT300N) con cifrado WPA2-PSK.
* **Servicios:** DHCP dinámico automatizado en routers para múltiples subredes.

---

## 🌐 Topología y Plan de Direccionamiento IP

El diseño comprende una **Sede Principal** conectada mediante WAN serial a dos sucursales (Sede Izquierda y Sede Derecha), incorporando un enlace redundante directo entre los núcleos de las sucursales.

* **Enlaces WAN / Punto a Punto (/30):**
  * `R-PRINCIPAL-WAN` ↔ `R-SEDE1` : `10.0.12.0/30`
  * `R-PRINCIPAL-WAN` ↔ `R-SEDE2-CORE` : `10.0.23.0/30`
  * `R-PRINCIPAL-WAN` ↔ `R-SEDE3-CORE` : `10.0.24.0/30`
  * Enlace Redundante Core 2 ↔ Core 3 : `10.0.35.0/30`

* **Segmentación LAN por Sedes:**
  * **Sede Principal:** VLAN 10 (Soporte: `192.168.10.0/24`), VLAN 20 (Administración: `192.168.20.0/24`), VLAN 99 (Nativa).
  * **Sede Izquierda (HSRP):** Red LAN `192.168.30.0/24` con Gateway Virtual `192.168.30.1` respaldado por dos routers (Activo/Standby).
  * **Sede Derecha (LAN + Wi-Fi):** Red LAN `192.168.40.0/24` con distribución cableada e inalámbrica segura.

---

## ⚙️ Características Técnicas Destacadas

### 1. Enrutamiento Dinámico (RIPv2)
Se configuró el protocolo **RIP versión 2** en toda la topología con `no auto-summary` para asegurar la convergencia automática de las rutas en las redes `10.0.0.0` y las diferentes subredes LAN de las sucursales.

### 2. Alta Disponibilidad (HSRP) en Sede Izquierda
Para evitar puntos únicos de falla en la puerta de enlace de la sucursal izquierda:
* **`R-SEDE2-IZQ`**: Configurado como Router Activo (`priority 120`, `preempt`).
* **`R-SEDE2-DER`**: Configurado como Router Standby (`priority 100`).
* Ambas interfaces comparten la IP virtual `192.168.30.1`, garantizando continuidad operativa ante la caída del equipo principal.

### 3. Seguridad de Puerto (Port Security)
En la Sede Derecha (`SW-SEDE3`), el puerto `Fa0/1` cuenta con políticas estrictas de seguridad:
* Restricción de direcciones MAC mediante `maximum 1` y `mac-address sticky`.
* Violación configurada en modo `shutdown` para bloquear de inmediato cualquier dispositivo no autorizado que se conecte al puerto físico.

---

## 📂 Contenido del Repositorio
* `proyecto-redes-empresa.pkt`: Archivo fuente completo de la simulación en Cisco Packet Tracer.
* `comandos-utilizados.txt`: Guía detallada con la línea de comandos (CLI) aplicada en cada router y switch, incluyendo configuraciones, verificaciones y solución de problemas para entornos físicos.

---

## 🧪 Pruebas de Verificación Rápida
Puedes validar el funcionamiento del entorno ejecutando los siguientes comandos de diagnóstico en los equipos:
* **Conectividad global:** `ping` cruzados entre VLANs y sedes remotas.
* **Estado HSRP:** `show standby brief` (para comprobar roles Active/Standby).
* **Seguridad de Puertos:** `show port-security interface fastEthernet0/1`.
* **Rutas dinámicas:** `show ip route rip`.
