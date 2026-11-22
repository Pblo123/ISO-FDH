## Documentación del laboratorio

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/aa97f8a0-7bc2-41c9-ba6b-f8d13206ce38" />

# Documentació d'instal·lació de la infraestructura virtualitzada

## 1. Introducció

La infraestructura es desenvoluparà mitjançant Oracle VM VirtualBox, utilitzant màquines virtuals per a simular una xarxa informàtica completa.

L'objectiu és instal·lar i configurar diferents sistemes operatius i servidors de comunicació, permetent realitzar proves de connectivitat, seguretat i administració de xarxes.

## 2. Configuració de la xarxa en VirtualBox

Es crearan dues xarxes principals:

- **Xarxa exterior:** utilitzarà un adaptador NAT per a proporcionar accés a Internet.
- **Xarxa interna:** es crearà una xarxa interna anomenada `LAN-INTERNA`, que simularà el commutador (switch) de comunicacions.

Els servidors de comunicació tindran dos adaptadors de xarxa: un connectat a la xarxa exterior i un altre a la xarxa interna.

La resta de màquines virtuals estaran connectades a `LAN-INTERNA`.

## 3. Instal·lació dels servidors de comunicació

Es crearan tres màquines virtuals independents:

### 3.1. MikroTik RouterOS (CHR)

1. Descarregar la imatge de MikroTik CHR.
2. Crear una màquina virtual en VirtualBox.
3. Assignar CPU, memòria RAM i dos adaptadors de xarxa.
4. Configurar el disc virtual i iniciar el sistema.
5. Configurar les interfícies, l'adreçament IP i les regles del tallafoc.

### 3.2. OPNsense

1. Descarregar la imatge ISO d'OPNsense.
2. Crear una màquina virtual de tipus BSD.
3. Assignar recursos i dos adaptadors de xarxa.
4. Instal·lar el sistema en el disc virtual.
5. Configurar les interfícies WAN i LAN i accedir a la seua interfície web.

### 3.3. Debian amb NFTABLES

1. Descarregar la imatge ISO de Debian.
2. Crear una màquina virtual amb dos adaptadors de xarxa.
3. Instal·lar el sistema operatiu.
4. Configurar les adreces IP.
5. Instal·lar i configurar NFTABLES per a gestionar el trànsit i les regles del tallafoc.

## 4. Instal·lació dels clients

Es crearan dues màquines virtuals:

- **Windows:** instal·lar Windows mitjançant la seua imatge ISO i connectar el seu adaptador a `LAN-INTERNA`.
- **Linux:** instal·lar Ubuntu mitjançant la seua imatge ISO i connectar-lo a la mateixa xarxa interna.

Els dos equips serviran per a comprovar la connectivitat i l'accés als serveis.

## 5. Instal·lació del NAS

S'utilitzarà TrueNAS SCALE com a sistema d'emmagatzematge.

1. Descarregar la seua imatge ISO.
2. Crear una màquina virtual i assignar memòria RAM i CPU.
3. Afegir un disc per al sistema i un altre per a les dades.
4. Configurar l'adaptador connectat a `LAN-INTERNA`.
5. Instal·lar TrueNAS i configurar l'emmagatzematge compartit mitjançant SMB o NFS.

## 6. Instal·lació del clúster de virtualització

Es crearan tres màquines virtuals connectades a `LAN-INTERNA`:

- **Windows Server:** instal·lació del sistema i configuració de serveis d'administració, com ara Active Directory.

## 7. Configuració i proves

S'assignaran adreces IP als diferents dispositius i es comprovarà la comunicació entre ells.

Es realitzaran les proves següents:

- Connectivitat mitjançant `ping`.
- Accés a Internet a través dels servidors de comunicació.
- Comprovació de les regles del tallafoc.
- Accés a l'emmagatzematge compartit del NAS.
- Funcionament dels serveis de xarxa.
- Connexió amb el servidor de bases de dades.

Els tres servidors de comunicació es provaran individualment per a comparar-ne les funcionalitats i evitar conflictes d'encaminament.

## 8. Conclusió

La infraestructura virtualitzada mitjançant VirtualBox permet simular una xarxa completa utilitzant diferents sistemes operatius i solucions de comunicació.
