# Informe d'optimització de recursos i millora de rendiment en VirtualBox

## 1. Context i objectiu de les optimitzacions

Després d'analitzar el rendiment del laboratori a l'equip de casa, es va comprovar que el principal coll d'ampolla era la **memòria RAM**, seguida dels pics de **CPU i disc** durant l'arrencada simultània. Amb la configuració per defecte, l'ordinador físic només suportava un màxim de 3 màquines virtuals actives; en superar aquest límit, el sistema es quedava sense memòria lliure, començava a paginar i acabava congelant-se per complet.

Per solucionar aquest problema i aprofitar al màxim el maquinari disponible sense comprometre l'estabilitat del sistema amfitrió, s'han proposat i aplicat tres optimitzacions clau en la configuració de VirtualBox i dels sistemes convidats.

## 2. Optimitzacions aplicades

1. **Arrencada en mode *Headless* i desactivació de maquinari virtual innecessari:**
   * *Configuració:* Tots els servidors del laboratori (els tres nodes Proxmox, TrueNAS, OPNsense i MikroTik) s'han configurat per arrancar en mode segon pla (*Inici sense cap* / *Headless*) en lloc de l'arrencada normal amb finestra gràfica. A més, s'ha deshabilitat la targeta de so virtual i els controladors USB en totes aquestes màquines.
   * *Justificació:* Com que aquests sistemes s'administren per xarxa mitjançant consola SSH o interfície web des del navegador de l'amfitrió, obrir una finestra de consola de VirtualBox per a cadascun suposava una despesa innecessària de memòria RAM i cicles de processament gràfic.

2. **Limitació de la memòria cau ZFS a TrueNAS i ajust a 1 vCPU en servidors:**
   * *Configuració:* A TrueNAS s'ha ajustat el paràmetre del sistema `vfs.zfs.arc_max` a 512 MB per restringir la memòria cau del sistema de fitxers. Paral·lelament, s'ha reduït l'assignació de processador a **1 únic nucli virtual (vCPU)** en els servidors de comunicacions i en els nodes Proxmox mentre no executen càrregues pesades.
   * *Justificació:* Per defecte, ZFS intenta reservar gairebé tota la RAM disponible, ofegant la resta de màquines. D'altra banda, limitar a 1 vCPU evita que els diferents processos de VirtualBox competisquen pels 6 fils físics de l'equip amfitrió.

3. **Arrencada escalonada i ús de mode consola als equips clients:**
   * *Configuració:* S'ha establert un protocol d'encesa seqüencial deixant un marge d'uns 30 segons entre l'arrencada d'una màquina i la següent. Així mateix, en un dels equips clients s'ha deshabilitat l'entorn gràfic per treballar únicament en mode terminal (*multi-user.target*).
   * *Justificació:* Arrancar diverses màquines al mateix temps genera una tempesta de lectures al disc SSD i col·lapsa la CPU al 100%. L'arrencada esglaonada reparteix aquesta càrrega en el temps i redueix dràsticament el consum de RAM dels clients.

## 3. Demostració amb mesures i millora obtinguda

Per verificar l'eficàcia d'aquests canvis, s'ha monitorat el rendiment de l'equip amfitrió mitjançant l'Administrador de tasques i el Monitor de recursos de Windows, comparant l'execució del mateix conjunt de màquines abans i després d'aplicar les optimitzacions:

* **Reducció del consum de memòria RAM:** Amb tres màquines actives (per exemple, OPNsense, TrueNAS i un node Proxmox), l'ús total de RAM a l'equip físic ha passat d'un **95-98%** (situació crítica en què el sistema començava a bloquejar-se) a estabilitzar-se al voltant del **74-78%**. Això suposa un alliberament real de prop d'1,5 GB de memòria física.
* **Control dels pics de CPU i Disc (I/O):** Abans de les optimitzacions, l'arrencada provocava pics sostinguts del **100% d'ús de CPU i disc** durant més d'un minut. Amb l'arrencada escalonada i el mode *Headless*, el consum de CPU durant l'inici s'ha mantingut entre el **55% i el 70%**, mentre que l'activitat del disc SSD no ha superat el **45%**.
* **Augment de la capacitat i estabilitat del laboratori:** Gràcies a aquest estalvi de recursos, s'han eliminat per complet les congelacions del sistema amfitrió i s'ha aconseguit ampliar el límit de treball fluid de **3 a 4 o fins i tot 5 màquines virtuals simultànies** (combinant els servidors de comunicacions lleugers amb algun node del clúster).

## 4. Conclusions

L'aplicació d'ajustaments específics en la configuració de VirtualBox demostra que és possible millorar notablement el rendiment d'un laboratori complex sense necessitat d'ampliar el maquinari físic. Eliminar la càrrega gràfica innecessària (*Headless*), controlar l'ús de memòria cau dels sistemes de fitxers i gestionar l'ordre d'arrencada permeten guanyar estabilitat, evitar bloquejos per falta de RAM i augmentar el nombre de màquines operatives de forma simultània.