# Inventari i comparativa dels equips informàtics

## 1. Equip informàtic de classe

- **Processador:** Intel Core Ultra 5 125H (14 nuclis).
- **Targeta gràfica:** Intel Arc Graphics integrada.
- **Memòria RAM:** 32 GB.
- **Emmagatzematge:** SSD de 2 TB.
- **Sistema operatiu:** Linux Mint.

## 2. Equip informàtic de casa

- **Processador:** Intel Core i5-9400F (6 nuclis i 6 fils).
- **Targeta gràfica:** NVIDIA GeForce GT 710.
- **Memòria RAM:** 8 GB.
- **Emmagatzematge:** SSD de 1,5 TB.
- **Sistema operatiu:** Windows 11.

## 3. Comparativa dels dos equips

L'equip de classe és considerablement més potent que el de casa. Destaca especialment per tindre més memòria RAM, un processador més modern i una millor capacitat de treball simultani.

L'equip de casa disposa d'un processador de sis nuclis que encara permet realitzar tasques habituals, però presenta més limitacions quan s'executen diverses aplicacions al mateix temps.

## 4. Anàlisi dels colls de botella per al laboratori de xarxes

L'activitat consisteix a muntar un laboratori d'una xarxa empresarial utilitzant VirtualBox, amb els elements següents:

- Un clúster amb 3 màquines virtuals.
- 3 servidors de comunicacions (MikroTik, OPNsense i Debian amb nftables).
- 2 clients (Windows i Debian).

Aquesta activitat requereix executar diverses màquines virtuals simultàniament, per la qual cosa els recursos més importants són el processador, la memòria RAM i l'emmagatzematge.

### Equip de classe

- **Processador:** Té 14 nuclis, per tant, pot gestionar diverses màquines virtuals simultàniament sense massa dificultats.
- **RAM:** Els 32 GB permeten repartir la memòria entre les diferents màquines virtuals. És suficient per al laboratori, encara que cal controlar-ne el consum.
- **Emmagatzematge:** Els 2 TB SSD permeten guardar les màquines virtuals i executar-les amb una bona velocitat.
- **Gràfica:** No és un factor especialment important per a aquest laboratori, ja que les màquines virtuals realitzaran principalment tasques de xarxa i administració.

**Possibles colls de botella:** Si s'executen totes les màquines virtuals al mateix temps, es podria arribar a consumir una part important de la memòria RAM i dels recursos del processador.

**Possibles solucions:**
- Controlar la RAM assignada a cada màquina virtual.
- No assignar més nuclis dels necessaris.
- Tancar aplicacions innecessàries mentre s'executa el laboratori.

### Equip de casa

- **Processador:** Els 6 nuclis poden executar diverses màquines virtuals, però tindran més dificultats per gestionar-les totes simultàniament.
- **RAM:** Els 8 GB són la principal limitació. Resulten insuficients per executar còmodament les 8 màquines virtuals del laboratori.
- **Emmagatzematge:** L'SSD d'1,5 TB ofereix prou capacitat i una bona velocitat per guardar i executar les màquines virtuals.
- **Gràfica:** La GT 710 no representa un problema important, perquè VirtualBox no necessita una targeta gràfica especialment potent per a aquest tipus de pràctiques.

**Possibles colls de botella:** La memòria RAM és el principal problema, seguida de la capacitat de processament si totes les màquines virtuals estan en funcionament alhora.

**Possibles solucions:**
- Ampliar la memòria RAM a 16 GB com a mínim, preferiblement 32 GB.
- Assignar només la RAM i els nuclis necessaris a cada màquina virtual.
- Executar les màquines virtuals per fases si els recursos no són suficients.
- Utilitzar versions de servidor sense entorn gràfic quan siga possible, per reduir el consum de recursos.
- Tancar aplicacions innecessàries mentre s'executa el laboratori.

## 5. Conclusió

Per a realitzar aquest laboratori, l'equip de classe no constaria cap problema, ja que disposa de 32 GB de RAM i un processador més modern, cosa que facilita l'execució simultània de les màquines virtuals.

L'equip de casa presenta una limitació important de memòria RAM, ja que els 8 GB no són suficients per executar totes les màquines virtuals del laboratori amb comoditat. La millora més necessària seria ampliar la RAM a 32 GB, sempre que el maquinari ho permeta.

En definitiva, l'equip de classe permetria desenvolupar el laboratori amb més fluïdesa, mentre que l'equip de casa necessitaria una ampliació de memòria per a oferir unes condicions de treball semblants.
