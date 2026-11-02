## Documentación del laboratorio

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/aa97f8a0-7bc2-41c9-ba6b-f8d13206ce38" />


# Documentación de instalación de la infraestructura virtualizada

## 1. Introducción

La infraestructura se desarrollará mediante Oracle VM VirtualBox, utilizando máquinas virtuales para simular una red informática completa.

El objetivo es instalar y configurar diferentes sistemas operativos y servidores de comunicación, permitiendo realizar pruebas de conectividad, seguridad y administración de redes.

## 2. Configuración de la red en VirtualBox

Se crearán dos redes principales:

- **Red exterior:** utilizará un adaptador NAT para proporcionar acceso a Internet.
- **Red interna:** se creará una red interna llamada `LAN-INTERNA`, que simulará el switch de comunicaciones.

Los servidores de comunicación tendrán dos adaptadores de red: uno conectado a la red exterior y otro a la red interna.

El resto de máquinas virtuales estarán conectadas a `LAN-INTERNA`.

## 3. Instalación de los servidores de comunicación

Se crearán tres máquinas virtuales independientes:

### 3.1. MikroTik RouterOS (CHR)

1. Descargar la imagen de MikroTik CHR.
2. Crear una máquina virtual en VirtualBox.
3. Asignar CPU, RAM y dos adaptadores de red.
4. Configurar el disco virtual e iniciar el sistema.
5. Configurar las interfaces, el direccionamiento IP y las reglas de firewall.

### 3.2. OPNsense

1. Descargar la imagen ISO de OPNsense.
2. Crear una máquina virtual de tipo BSD.
3. Asignar recursos y dos adaptadores de red.
4. Instalar el sistema en el disco virtual.
5. Configurar las interfaces WAN y LAN y acceder a su interfaz web.

### 3.3. Debian con NFTABLES

1. Descargar la ISO de Debian.
2. Crear una máquina virtual con dos adaptadores de red.
3. Instalar el sistema operativo.
4. Configurar las direcciones IP.
5. Instalar y configurar NFTABLES para gestionar el tráfico y las reglas del firewall.

## 4. Instalación de los clientes

Se crearán dos máquinas virtuales:

- **Windows:** instalar Windows mediante su imagen ISO y conectar su adaptador a `LAN-INTERNA`.
- **Linux:** instalar Ubuntu mediante su imagen ISO y conectarlo a la misma red interna.

Ambos equipos servirán para comprobar la conectividad y el acceso a los servicios.

## 5. Instalación del NAS

Se utilizará TrueNAS SCALE como sistema de almacenamiento.

1. Descargar su imagen ISO.
2. Crear una máquina virtual y asignar memoria RAM y CPU.
3. Añadir un disco para el sistema y otro para los datos.
4. Configurar el adaptador conectado a `LAN-INTERNA`.
5. Instalar TrueNAS y configurar el almacenamiento compartido mediante SMB o NFS.

## 6. Instalación del clúster de virtualización

Se crearán tres máquinas virtuales conectadas a `LAN-INTERNA`:

- **Windows Server:** instalación del sistema y configuración de servicios de administración, como Active Directory.
- **Linux de servicios:** instalación de Debian o Ubuntu Server para alojar servicios como DNS, DHCP o servidor web.
- **Servidor de bases de datos:** instalación de Linux y un gestor de bases de datos como MariaDB o PostgreSQL.

## 7. Configuración y pruebas

Se asignarán direcciones IP a los diferentes dispositivos y se comprobará la comunicación entre ellos.

Se realizarán las siguientes pruebas:

- Conectividad mediante `ping`.
- Acceso a Internet a través de los servidores de comunicación.
- Comprobación de las reglas del firewall.
- Acceso al almacenamiento compartido del NAS.
- Funcionamiento de los servicios de red.
- Conexión con el servidor de bases de datos.

Los tres servidores de comunicación se probarán individualmente para comparar sus funcionalidades y evitar conflictos de enrutamiento.

## 8. Conclusión

La infraestructura virtualizada mediante VirtualBox permite simular una red completa utilizando diferentes sistemas operativos y soluciones de comunicación.

Este entorno facilita el aprendizaje de administración de sistemas, seguridad informática, almacenamiento y servicios de red sin necesidad de disponer de hardware adicional.
