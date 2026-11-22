# Documentación: RAID0, RAID1 y RAID5 en TrueNAS (laboratorio VirtualBox)

## 1. Objetivo

Crear tres pools de almacenamiento en TrueNAS con distintos niveles de RAID y documentar el proceso:

| Pool | Tipo de RAID | Nombre en TrueNAS (layout) | Nº de discos | Tamaño por disco | Capacidad útil aprox. | Tolerancia a fallos |
|---|---|---|---|---|---|---|
| `Copias_seguridad` | RAID 0 | Stripe | 1 | **80 GB** | ~80 GB | Ninguna |
| `raid1` | RAID 1 | Mirror | 2 | 2 GB | ~2 GB | 1 disco |
| `raid5` | RAID 5 | RAIDZ1 | 3 | 2 GB | ~4 GB | 1 disco |

> **Nota:** TrueNAS usa ZFS, por lo que los nombres de RAID son equivalentes:
> RAID0 → *Stripe*, RAID1 → *Mirror*, RAID5 → *RAIDZ1*.

El pool `Copias_seguridad` (RAID0) almacena las copias de seguridad de los servidores de comunicaciones:

- **OPNsense** (firewall)
- **MikroTik** (router)
- **nftables** (firewall Linux)

---

## 2. Entorno

- Hipervisor: **VirtualBox**
- Sistema: **TrueNAS** (SCALE o CORE; las capturas y menús descritos corresponden a SCALE, en CORE los nombres cambian ligeramente)
- Disco de arranque: 1 disco virtual (mín. 16 GB) para el sistema
- Discos de datos: **6 discos virtuales** (1 de **80 GB** para `Copias_seguridad` y 5 de 2 GB para `raid1` y `raid5`)

| Disco virtual | Uso |
|---|---|
| Disco SO (16 GB) | Sistema TrueNAS |
| `disco1` (**80 GB**) | RAID0 → `Copias_seguridad` |
| `disco2` (2 GB) | RAID1 → `raid1` |
| `disco3` (2 GB) | RAID1 → `raid1` |
| `disco4` (2 GB) | RAID5 → `raid5` |
| `disco5` (2 GB) | RAID5 → `raid5` |
| `disco6` (2 GB) | RAID5 → `raid5` |

---

## 3. Preparar los discos en VirtualBox

1. Apagar la máquina virtual de TrueNAS.
2. Abrir **Configuración → Almacenamiento**.
3. Seleccionar el controlador **SATA** y pulsar **Añadir disco duro**.
4. Elegir **Crear**:
   - Tipo: `VDI`
   - Almacenamiento: `Reservado dinámicamente`
   - Tamaño: `80 GB` para `disco1` y `2 GB` para `disco2` … `disco6`
   - Nombre: `disco1` … `disco6`
5. Repetir hasta tener los 6 discos y adjuntarlos al controlador SATA.
6. Arrancar la máquina virtual.

> Los discos aparecerán en TrueNAS como `sdb`, `sdc`, `sdd`, `sde`, `sdf`, `sdg` (el nombre puede variar).

### Verificar que TrueNAS detecta los discos

Desde la interfaz web: **Storage → Disks**. Deben aparecer los 6 discos sin usar: uno de 80 GB y cinco de 2 GB.

Desde la shell (opcional):

```bash
lsblk
```

---

## 4. Crear el RAID 0: pool `Copias_seguridad`

Un RAID0 (*Stripe*) reparte los datos entre discos sin redundancia. En este laboratorio se usa **1 solo disco**.

1. Ir a **Storage → Create Pool**.
2. En **Name** escribir: `Copias_seguridad`.
3. En **Data VDevs**, pulsar **Add VDev** / seleccionar el layout:
   - **Layout:** `Stripe`
4. Seleccionar **1 disco** (`disco1`, 80 GB) y moverlo a la lista de Data VDevs.
5. Pulsar **Create** (revisar el resumen y confirmar).
6. Confirmar el aviso de que **no hay redundancia**.

### Resultado esperado

- Pool: `Copias_seguridad`
- Estado: **ONLINE**
- Capacidad: ~80 GB (algo menos por la reserva de ZFS)

> ⚠️ **Aviso:** al no haber redundancia, si el disco falla se pierden todos los datos. En un entorno real, las copias de seguridad no deberían ir en RAID0 (ver sección 9).

---

## 5. Crear el RAID 1: pool `raid1`

Un RAID1 (*Mirror*) duplica los datos en 2 discos.

1. Ir a **Storage → Create Pool**.
2. **Name:** `raid1`.
3. **Layout:** `Mirror`.
4. Seleccionar **2 discos** (`disco2` y `disco3`).
5. Pulsar **Create** y confirmar.

### Resultado esperado

- Pool: `raid1`
- Estado: **ONLINE**
- Capacidad útil: ~2 GB (la mitad del total)
- Soporta el fallo de **1 disco**

---

## 6. Crear el RAID 5: pool `raid5`

Un RAID5 (*RAIDZ1*) reparte los datos y la paridad entre 3 discos como mínimo.

1. Ir a **Storage → Create Pool**.
2. **Name:** `raid5`.
3. **Layout:** `RAIDZ1`.
4. Seleccionar **3 discos** (`disco4`, `disco5` y `disco6`).
5. Pulsar **Create** y confirmar.

### Resultado esperado

- Pool: `raid5`
- Estado: **ONLINE**
- Capacidad útil: ~4 GB (se pierde 1 disco en paridad)
- Soporta el fallo de **1 disco**

---

## 7. Verificación de los pools

### Desde la interfaz web

**Storage → Dashboard**: los tres pools deben aparecer en estado **ONLINE** (verde).

### Desde la shell

```bash
zpool status
zpool list
```

Salida esperada (resumida):

```text
  pool: Copias_seguridad
 state: ONLINE
config:
        NAME              STATE
        Copias_seguridad  ONLINE
          sdb             ONLINE

  pool: raid1
 state: ONLINE
config:
        NAME        STATE
        raid1       ONLINE
          mirror-0  ONLINE
            sdc     ONLINE
            sdd     ONLINE

  pool: raid5
 state: ONLINE
config:
        NAME        STATE
        raid5       ONLINE
          raidz1-0  ONLINE
            sde     ONLINE
            sdf     ONLINE
            sdg     ONLINE
```

---

## 8. Almacenar las copias de seguridad en `Copias_seguridad`

### 8.1 Crear los datasets

Para tener las copias ordenadas, se crea un dataset por servidor:

1. Ir a **Datasets**.
2. Seleccionar el pool `Copias_seguridad` → **Add Dataset**.
3. Crear los siguientes datasets:

| Dataset | Contenido |
|---|---|
| `Copias_seguridad/opnsense` | Copias de OPNsense |
| `Copias_seguridad/mikrotik` | Copias de MikroTik |
| `Copias_seguridad/nftables` | Copias de nftables |

### 8.2 Crear un usuario para las copias

1. Ir a **Credentials → Local Users → Add**.
2. Crear, por ejemplo, el usuario `backup` con contraseña.
3. Dar permisos al usuario sobre los tres datasets (**Datasets → Edit Permissions**).

### 8.3 Compartir los datasets

Opción A: **SMB**

1. **Shares → Windows (SMB) Shares → Add**.
2. Seleccionar la ruta `/mnt/Copias_seguridad/opnsense` (repetir para cada dataset).
3. Guardar y **activar el servicio SMB** (**System → Services**).

Opción B: **SFTP/SSH** (recomendada para MikroTik y nftables)

1. **System → Services → SSH** → activar.
2. En la configuración de SSH, permitir la autenticación por contraseña para el usuario `backup`.

### 8.4 Realizar las copias

#### OPNsense

Desde la interfaz web: **System → Configuration → Backups → Download configuration** (genera `config.xml`).

Copiar el archivo al dataset:

```bash
scp config-OPNsense.xml backup@IP_TRUENAS:/mnt/Copias_seguridad/opnsense/
```

#### MikroTik

Desde la terminal de RouterOS:

```text
/export file=mikrotik_backup
/system backup save name=mikrotik_backup
```

Enviar los ficheros a TrueNAS:

```text
/tool fetch upload=yes mode=sftp src-path=mikrotik_backup.rsc dst-path=/mnt/Copias_seguridad/mikrotik/mikrotik_backup.rsc address=IP_TRUENAS user=backup password=TU_PASSWORD
```

#### nftables

En el servidor Linux:

```bash
sudo nft list ruleset > nftables_backup.nft
scp nftables_backup.nft backup@IP_TRUENAS:/mnt/Copias_seguridad/nftables/
```

> También puede copiarse `/etc/nftables.conf`.

### 8.5 Estructura final esperada

```text
/mnt/Copias_seguridad/
├── opnsense/
│   └── config-OPNsense.xml
├── mikrotik/
│   ├── mikrotik_backup.rsc
│   └── mikrotik_backup.backup
└── nftables/
    └── nftables_backup.nft
```

---

## 9. Observaciones y recomendaciones

- **RAID0 no es una copia de seguridad fiable:** no tiene redundancia. Si el disco falla, se pierden las copias. Se recomienda replicar el contenido de `Copias_seguridad` a `raid1` o `raid5` (los ficheros de configuración ocupan muy poco, pero ten en cuenta que esos pools solo tienen ~2 GB y ~4 GB frente a los 80 GB de origen) (por ejemplo con una tarea de **Replication** o **Rsync Task**).
- **RAID no sustituye a una copia de seguridad:** protege frente a fallos de disco, no frente a borrados accidentales o ransomware. Se pueden usar **Snapshots** periódicos (**Data Protection → Periodic Snapshot Tasks**).
- **Discos de 2 GB (`raid1` y `raid5`):** son válidos para laboratorio; ZFS reserva parte del espacio, por lo que la capacidad útil será ligeramente inferior a la teórica.
- **Sustituir un disco fallido (RAID1/RAID5):** **Storage → Manage Devices** → seleccionar el disco → **Replace**. (En laboratorio puede simularse desconectando un disco virtual).

---

## 10. Resumen

| Pool | RAID | Layout TrueNAS | Discos | Capacidad útil | Uso |
|---|---|---|---|---|---|
| `Copias_seguridad` | 0 | Stripe | 1 (80 GB) | ~80 GB | Copias de OPNsense, MikroTik y nftables |
| `raid1` | 1 | Mirror | 2 | ~2 GB | Pruebas de redundancia |
| `raid5` | 5 | RAIDZ1 | 3 | ~4 GB | Pruebas de redundancia con paridad |
