---
description: El Mainnet de Hedera se compone actualmente de nodos de consenso autorizados operados por el Consejo de Gobierno de Hedera
---

# Requisitos del nodo

Lo siguiente se proporciona para ayudar a los miembros de [_Hedera Governing ► l_](https://hedera.com/council) a implementar su nodo de consenso de red mainnet permitido. Tenga en cuenta que esta información no pretende aplicar a la transición de Hedera a una red sin permisos.

## Requisitos mínimos de la plataforma del nodo

Actualmente, el Mainnet de Hedera se ejecutará a una velocidad determinada por el nodo de menor rendimiento. Para garantizar un nivel común de rendimiento mínimo de los requerimientos de hardware, conectividad y alojamiento se han definido para los nodos del Consejo de Gobierno inicial.

{% hint style="warning" %}
Para garantizar una conformidad exacta con los requisitos mínimos, por favor proporcione hardware de nodos, conectividad y datos de alojamiento a Hedera antes de comprar (devops@swirldslabs.com).
{% endhint %}

- CPU: compatible con X86/X64 (Intel Xeon o AMD EPYC); reunión de 24 núcleos/48 hilos o excediendo las siguientes pruebas de rendimiento:
  - Banco de Geekbench 6 puntuación de un solo núcleo
    - Mínimo: 1000 o mayor
    - Recomendado: 1500 o superior
  - Calificación de un hilo de pasaje:
    - Mínimo para permanecer en Mainnet: 2300 o mayor
    - Recomendado: 2800 o mayor
- Conectividad a la red: Se ha mantenido el ancho de banda de 1Gb/s a través de una sola interfaz Ethernet de 1-Gigabit / 10-Gigabit
- Memoria: 256 GB PC4-21300 2666MHz DDR4 ECC Registrado DIMM o más rápido (mínimo), 320GB o superior PC4-25600 3200MHz (recomendado)
- Almacenamiento: Se recomienda montar 240 GB SSD con incursión 1 como volumen raíz `/` y luego proporcionar almacenamiento utilizable a través de diferentes dispositivos montados más tarde durante la instalación. Esto puede no ser posible en su hardware, por lo que alternativamente todo el almacenamiento requerido puede ser asignado al volumen de root.
  - Mínimo: 5TB de almacenamiento utilizable SSD NVMe
  - Recomendado:
    - 2 x 240GB SSD con RAID 1 para OS Storage
    - 2 x dispositivos NVMe como RAID 0 7.5TB (o 4x como arreglo RAID 10)
- Rendimiento de almacenamiento: Si se monta a volumen de raíz, el volumen de la raíz debe cumplir con estos requisitos. Si se proporciona a través de RAID, la matriz RAID debe cumplir estos requisitos:
  - Escritura secuencial sostenida:
    - Mínimo: 2.000 GB/s
    - Recomendado: 3,000GB/s
  - Lectura secuencial sostenida:
    - Mínimo: 3,000GB/s
    - Recomendado: 4,500GB/s
  - Lectura aleatoria, sincrónica:
    - Mínimo: 250.000 IOPS
    - Recomendado: 375.000 IOPS
  - Leer aleatoriamente, AIO:
    - Mínimo: 500,000 IOPS
    - Recomendado: 750,000 IOPS
  - Escritura aleatoria, sincrónica:
    - Mínimo 100,000 IOPS
    - Recomendado: 150.000 IOPS
  - latencia de lectura aleatoria de menos de 200μs, promedio
- Los nodos deben pasar la suite de pruebas de rendimiento de Hedera realizada al momento de la instalación

### **Sistema operativo del nodo:**

- Linux
  - Kernel mínimo versión 3.10+
  - Compatibilidad activa con la versión de soporte a largo plazo
    - Ubuntu LTS 22.04
    - RHEL 8

### **Software del nodo:**

- Motor Docker (`docker-ce` versión 20.10.6)
  - Desplegado con privilegios de root
  - Soporte de contenedor privado habilitado (opcional)
    - Si el soporte de contenedor privilegiado está deshabilitado, entonces la máquina del host debe ejecutar el Daemon de Havege
- Docker Compose (`docker-compose` versión 1.29.2)
- Soporte IPTables (`linux-kernel` versión 3.10+)
- Daemon de Havege (versión 'haveged' 1.9.14)
  - Si el soporte de contenedor privilegiado está habilitado, este requisito es opcional
- HashDeep Utilities (versión `hashdeep` 4.4)
  - Requerido para validación de integridad de actualización
- Coleccionista de Bindplane (versión 4+)
  - Requerido para la monitorización del registro del software del nodo
- JQ CLI (`jq` versión 1.5+)
  - Dependencia necesaria para las herramientas de administración de nodos
- GNU CoreUtils (`coreutils` versión 8.00+)
  - Dependencia necesaria para las herramientas de administración de nodos
- cURL CLI (`curl` versión 7.58.0+)
  - Dependencia necesaria para las herramientas de administración de nodos
- InCron Daemon (`incron` versión 0.5.12+)
  - Dependencia necesaria para las herramientas de administración de nodos
  - Requerido para la actualización automática de red
- CLI de Rsync (`rsync` versión 3.0.0+)
  - Dependencia necesaria para las herramientas de administración de nodos
  - Requerido para la actualización automática de red
- Herramientas de gestión de nodos (`node-mgmt-tools` versión 0.1.0+)
  - Actualizaciones implementadas mediante el proceso de actualización de node
  - Debe instalarse en la siguiente ruta: `/opt/hgcapp/node-mgmt-tools`
    - La ruta debe ser escribible y ejecutable por la cuenta de usuario `hgcadmin`

### **Cuentas de usuario del sistema:**

- _**Cuenta de software del nodo (obligatorio)**_
  - Especificación del usuario
    - Nombre: `hedera`
    - UID Unix: `2000`
    - Membresía de grupo
      - Primario: `hedera`
      - Segundo: `admin` o `wheel` _(dependiendo de la distribución de Linux)_
    - Permisos:
      - Lee, Escribe y Ejecuta el acceso a todo el árbol de carpetas `/opt/hgcapp`
  - Especificación del grupo
    - Nombre: `hedera`
    - Unix GID: `2000`

{% hint style="info" %}
**Nota:** Configuraciones de referencia disponibles en Apéndices B, C, D
{% endhint %}

### Proxy

El acceso al nodo a través de APIs públicas debe ser mediado por un proxy en línea. Debajo están las especificaciones para establecer este proxy.

- 2- CPU core-x86/x64
- 2GB de RAM
- 100GB de almacenamiento SSD
- Conectividad sostenida de red de 200Mb/s con dirección IP pública estática
- Docker soportado (Hedera para proporcionar imagen de Docker con HAProxy)

### Conectividad de red

Conectividad del nodo

- Conectividad a Internet de 1Gbps – sostenida (no rota)
  - Preferencias ilimitadas
  - Desplegado con acceso bloqueado por fuego a otros nodos de consenso de la red principal
- Nodo desplegado en red DMZ dedicada (aislada)
  - IP estática (FQDN no es compatible)
  - Puerto TCP 50111 abierto a 0.0.0.0/0
  - Puerto TCP 50211 abierto a 0.0.0.0/0
  - Puerto TCP 50212 abierto a 0.0.0.0/0
  - Puerto TCP 80 abierto desde 0.0.0.0/0 (para conectividad del repositorio de paquetes OS)
  - Puerto TCP 443 abierto desde 0.0.0/0 (para conectividad del repositorio del sistema operativo)
  - Puerto UDP 123 abierto y progreso a 0.0.0.0/0 (para sincronización del tiempo del sistema en el pool NTP)

Conectividad del proxy

- Dirección IP estática (FQDN no soportado)
- Conectividad a Internet 200Mb/s
- Puerto TCP 80 abierto desde 0.0.0.0/0 (para conectividad del repositorio de paquetes OS)
- Puerto TCP 443 abierto desde 0.0.0/0 (para conectividad del repositorio del sistema operativo)
- Puerto TCP 50211 abierto a 0.0.0.0/0
- Puerto TCP 50212 abierto a 0.0.0.0/0

Vinculación de interfaz (opcional)

- Si se utiliza el enlace a la interfaz, tenga en cuenta que TLS está en uso mutuo, y que las rutas basadas en políticas de capa 3 (PBR) con rutas duales no están soportadas. Sólo se admite la unión de interfaz de capa 2 usando el modo 1 (puertos autónomos usando una copia de seguridad activa) o el modo 4 (LACP 802.3ad activo/activo).

### Alojamiento

- Requisitos de alojamiento estándar de la industria para seguridad y disponibilidad
  - Instalación de Centro de Datos de Nivel 1
  - SAE 16 /18, SOC 2 Tipo 2 compatible
- Hedera intentará evitar duplicar los proveedores de alojamiento entre los miembros del Consejo

### Software e instalación

- Cualquier distribución Linux de 64 bits de soporte a largo plazo (LTS)
  - Distribuciones aprobadas:
    - Ubuntu
    - Red Hat Enterprise
    - Oracle Linux
    - CentOS (sólo hasta 2023)

## Topología de red /(Configuración típica del Datacenter Corporativo/)

![](../../../../.gitbook/assets/Network-topology.jpg)

## Pasos de despliegue

Los siguientes pasos describen el proceso para que los miembros del Consejo añadan su nodo de consenso a la red principal.

1. Contacto inicial con miembro del Consejo y entidad de alojamiento de nodos
   1. Identificar individuos clave y administradores de proyectos
   2. Establecer cadencia de reunión regular del equipo de despliegue
2. Transmisión de requisitos técnicos y discusión de opciones de implementación
3. Adquisición de plataforma de nodo
   1. Hardware o instancia virtual
   2. Conectividad de red
   3. Instalación de alojamiento
4. Configuración del sistema operativo en la plataforma
   1. Aprovisionamiento de cuentas como se especifica
   2. Aprovisionamiento del acceso a la red (listas de control de acceso/reglas/cortafuegos)
5. Transmisión de credenciales a Hedera
   1. Incluye instrucciones especiales para acceder a permisos como VPN
   2. Discusión de las vías de apoyo y escalada entre organizaciones
6. Hedera lleva a cabo la revisión de configuración
   1. Plataforma
   2. Conectividad
7. Despliegue del software de nodos de consenso de Hedera y bibliotecas de soporte requeridas
8. Añadir configuración de conexión para una red de pruebas de rendimiento de Hedera
   1. Hedera ejecuta pruebas de funcionalidad, estabilidad y rendimiento para todos los servicios de red
9. Revisión de los resultados de las pruebas y determinación de la preparación para la conectividad de la red principal
   1. Revisar documentación de gestión clave relacionada con las cuentas de los miembros del Consejo, incluyendo: cuenta de tarifas, cuenta de apostador para proxy, etc.
   2. Actualizar claves privadas usando herramientas proporcionadas
10. Programar conexión a la red principal
11. Mainnet en vivo
