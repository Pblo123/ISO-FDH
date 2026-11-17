
# Esquema i configuració de la xarxa virtualitzada

## 1. Objectiu

L'objectiu és crear una infraestructura de xarxa virtualitzada mitjançant Oracle VM VirtualBox, simulant una xarxa d'aula amb diferents servidors de comunicació, clients, un NAS i un entorn de virtualització.

## 2. Topologia de xarxa

La infraestructura estarà formada pels elements següents:

- Switch principal de l'aula, que proporcionarà accés a Internet.
- Tres servidors de comunicació: MikroTik, OPNsense i Debian amb NFTABLES.
- Switch de comunicacions que connectarà els dispositius de la xarxa interna.
- Dos equips clients: Windows i Linux.
- Un NAS amb TrueNAS SCALE.
- Tres servidors virtualitzats: Windows Server, Linux de serveis i servidor de bases de dades.


## 3. Configuració en VirtualBox

Per a simular la infraestructura s'utilitzarà el mode **Adaptador pont (Bridge Adapter)** de VirtualBox.

Aquest mode permet connectar les màquines virtuals directament a la xarxa física de l'aula, de manera que puguen comunicar-se amb altres dispositius de la mateixa xarxa i accedir a Internet.

Els servidors de comunicació disposaran de dos adaptadors de xarxa:

- Adaptador 1: Adaptador pont, connectat a la xarxa de l'aula.
- Adaptador 2: Xarxa interna `LAN-INTERNA`, per a comunicar-se amb els dispositius interns.

Els clients, el NAS i els servidors virtualitzats utilitzaran un adaptador connectat a `LAN-INTERNA`.

## 4. Configuració IP

La xarxa interna utilitzarà el direccionament següent:

- Xarxa: `192.168.12.0/24`
- Màscara: `255.255.255.0`
- Adreça de difusió: `192.168.12.255`

| Dispositiu | Adreça IP |
|---|---|
| MikroTik (LAN) | 192.168.12.2 |
| OPNsense (LAN) | 192.168.12.1 |
| Debian NFTABLES (LAN) | 192.168.12.3 |
| Client Windows | 192.168.12.30 |
| Client Linux | 192.168.12.5 |
| TrueNAS | 192.168.12.10 |
| Windows Server | 192.168.12.20 |

Les interfícies exteriors dels servidors de comunicació obtindran la seua adreça IP automàticament mitjançant DHCP de la xarxa de l'aula.

Les interfícies internes utilitzaran les adreces estàtiques indicades en la taula.

## 5. Funcionament de la xarxa

El switch principal de l'aula proporcionarà accés a Internet mitjançant la connexió pont de VirtualBox.

Els servidors de comunicació rebran la connexió exterior i gestionaran el trànsit cap a la xarxa interna `192.168.12.0/24`.

La xarxa interna connectarà els clients Windows i Linux, el NAS i les tres màquines virtuals de serveis.

Els tres servidors de comunicació es provaran individualment com a portes d'enllaç per a comparar les seues funcionalitats i configuracions.


## 6. Funcionament

Els servidors MikroTik, OPNsense i Debian amb NFTABLES permetran gestionar les comunicacions entre la xarxa interna i l'exterior.

Es provarà cada servidor de comunicació per separat, utilitzant-lo com a porta d'enllaç dels clients. D'aquesta manera es podran comparar les diferents solucions sense generar conflictes d'encaminament.

## 7. Proves de connectivitat

Una vegada configurada la infraestructura, es realitzaran les proves següents:

- Comprovar les adreces IP amb `ipconfig` o `ip addr`.
- Comprovar la connectivitat entre equips amb `ping`.
- Verificar l'accés a Internet.
- Comprovar les regles del firewall.
- Verificar l'accés als recursos compartits del NAS.
- Comprovar la comunicació amb els servidors virtualitzats.
