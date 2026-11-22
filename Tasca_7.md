# Informe de consum de recursos i gestió del laboratori en VirtualBox

## 1. Màquines virtuals amb major consum i saturació de recursos

Dins de la infraestructura desplegada en **VirtualBox** (composta per tres servidors de comunicacions, un clúster de tres nodes Proxmox, un servidor TrueNAS i dos equips clients), el consum de recursos no és homogeni. S'han identificat clarament quines màquines saturen cada component físic:

* **Clúster Proxmox (els 3 nodes):** Són, amb diferència, les màquines que més **memòria RAM** i **CPU** acaparen. Com que gestionen el clúster i utilitzen virtualització imbricada (*nested virtualization*) per allotjar altres màquines o contenidors al seu interior, disparen l'ús del processador i bloquegen grans blocs de memòria només arrancar.

* **TrueNAS:** És la màquina més crítica pel que fa a **memòria RAM** i **accés a disc (I/O)**. El seu sistema de fitxers (ZFS) tendeix a reservar tota la memòria cau disponible, saturant la RAM ràpidament, alhora que genera pics intensos de lectura i escriptura en disc durant la gestió dels volums i les transferències de dades.

* **Equips Clients (2 màquines):** En disposar d'interfície gràfica d'usuari, consumeixen una quantitat elevada de **memòria RAM** i mantenen un ús constant de **CPU** i **recursos gràfics**, sent molt pesades quan coincideixen actives amb els servidors principals.

* **Servidors de comunicacions (OPNsense, MikroTik i tercer servidor):** Presenten consums molt desiguals entre ells:
  * **OPNsense:** Té un consum moderat-alt de **RAM** i **CPU** a causa dels serveis d'encaminament, tallafocs i inspecció de trànsit.
  * **Tercer servidor de comunicacions:** Consum moderat i estable, depenent dels serveis de xarxa actius.
  * **MikroTik:** És la màquina més lleugera de tot el laboratori; el seu consum de CPU i RAM és pràcticament residual i no satura cap recurs.

## 2. Rebaixa i ajust de recursos aplicats per poder treballar

Per adaptar aquest laboratori a l'entorn de treball de casa (amb molta menys capacitat que el de classe), ha estat imprescindible rebaixar dràsticament els recursos assignats a les màquines més pesades, prioritzant l'estalvi de **memòria RAM**:

| Màquina Virtual | Recurs rebaixat principalment | Ajust aplicat per evitar la saturació |
| :--- | :--- | :--- |
| **Nodes Proxmox (x3)** | **RAM i nuclis (vCPU)** | Reducció al mínim indispensable per arrancar el sistema base i formar el clúster (limitant a 1-2 nuclis i retallant la RAM al límit operacional), sacrificat rendiment en virtualització imbricada. |
| **TrueNAS** | **Memòria RAM** | Rebaixa severa de la memòria assignada per impedir que la memòria cau de ZFS acapare tota la RAM física del sistema amfitrió. |
| **Equips Clients (x2)** | **RAM i memòria de vídeo** | Retallada de memòria RAM al mínim funcional i reducció de l'acceleració gràfica per evitar colls d'ampolla en l'entorn d'escriptori. |
| **OPNsense i 3r Servidor** | **Memòria RAM** | Ajust a la baixa (al voltant d'1 GB) per mantenir els serveis bàsics d'encaminament sense penalitzar la resta del laboratori. |
| **MikroTik** | **RAM i CPU** | Configurat amb recursos mínims (128-256 MB de RAM i 1 nucli), ja que no requereix més potència per funcionar. |

## 3. Límit d'execució simultània i bloqueig del sistema

A diferència de l'entorn de classe —on és possible arrancar i operar amb **les 9 màquines virtuals alhora** sense perdre fluïdesa—, a casa els límits de maquinari imposen restriccions molt estrictes:

* **Límit màxim de 3 màquines actives:** Fins i tot després d'haver rebaixat al mínim la RAM de totes les màquines, l'equip de casa només suporta un **màxim de 3 màquines virtuals en execució simultània** (per exemple, una combinació lleugera com MikroTik, OPNsense i un sol node Proxmox o un client).
* **Col·lapse i congelació total:** Durant les proves de càrrega, en intentar arrancar una quarta màquina o combinar les més pesades (com els nodes de Proxmox juntament amb TrueNAS), **la memòria RAM física va arribar al 100%**. El sistema operatiu amfitrió va començar a paginar de manera massiva i **l'ordinador es va quedar completament congelat**, sense respondre al ratolí ni al teclat, fet que va obligar a forçar l'apagat de l'equip i a tornar a iniciar tot el laboratori des de zero.

## 4. Conclusions

El laboratori demostra que els sistemes de virtualització imbricada (**Proxmox**) i d'emmagatzematge avançat (**TrueNAS**) són els principals responsables de la saturació de **RAM** i **CPU**. Quan no es disposa d'un marge ampli de memòria física, rebaixar els recursos individuals de cada màquina virtual ajuda a guanyar estabilitat, però no evita el topall d'execució simultània (fixat en **3 màquines**), obligant a treballar de manera modular i seqüencial per evitar el bloqueig total de l'equip.