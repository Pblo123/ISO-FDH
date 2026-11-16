
# Tests de diagnòstic dels equips informàtics

## 1. Introducció

L'objectiu d'aquesta activitat és realitzar proves de diagnòstic en els ordinadors de classe i de casa, utilitzant Linux Mint com a sistema operatiu.

Es comprovarà el rendiment de la memòria RAM, l'estat i la velocitat dels discos SSD i la connectivitat de xarxa.

Finalment, es compararan els resultats per a identificar possibles colls de botella.

## 2. Característiques dels equips

### Equip de classe

- Processador: Intel Core Ultra 5 125H.
- Memòria RAM: 32 GB.
- Emmagatzematge intern: SSD d'1 TB.
- Emmagatzematge extern: SSD d'1 TB.

### Equip de casa

- Processador: Intel Core i5-9400F.
- Memòria RAM: 8 GB.
- Targeta gràfica: NVIDIA GeForce GT 710.
- Emmagatzematge intern: SSD d'1 TB.
- Emmagatzematge extern: SSD d'1 TB (el mateix dispositiu utilitzat en l'equip de classe).
## 3. Proves de diagnòstic

### 3.1. Diagnòstic de memòria RAM

**Objectiu:** comprovar la quantitat de memòria disponible i el seu consum.

**Eines:** `free` i `htop`.

**Ordres utilitzades:**

```bash
free -h
htop
```

La primera ordre mostra la memòria total, utilitzada i disponible. La segona permet observar en temps real el consum de RAM i CPU.

**Condicions:** executar les ordres amb el sistema iniciat i sense aplicacions exigents obertes.

**Resultats orientatius:**

| Mesura | Equip de classe | Equip de casa |
|---|---:|---:|
| RAM total | 31,2 GiB | 7,6 GiB |
| RAM utilitzada | 5,2 GiB | 2,8 GiB |
| RAM disponible | 25,1 GiB | 4,5 GiB |
| Ús aproximat | 17 % | 37 % |

### 3.2. Diagnòstic del disc SSD

**Objectiu:** comprovar l'estat del disc i mesurar la velocitat de lectura i escriptura.

**Eines:** `smartmontools` i `fio`.

**Instal·lació:**

```bash
sudo apt update
sudo apt install smartmontools fio
```

**Comprovació de l'estat del disc:**

```bash
lsblk -o NAME,SIZE,TYPE,MODEL
sudo smartctl -a /dev/sda
```

La primera ordre identifica els discos connectats. La segona consulta les dades SMART, la temperatura i l'estat del dispositiu. Si el disc és NVMe, cal substituir `/dev/sda` pel dispositiu corresponent, per exemple `/dev/nvme0n1`.

**Prova de velocitat:**

```bash
fio --name=test --filename=testfile --size=1G --bs=1M --rw=readwrite --direct=1 --iodepth=16 --runtime=30 --time_based --group_reporting
```

Mesura el rendiment de lectura i escriptura del disc.

**Condicions:** realitzar la prova amb el disc sense altres operacions importants i eliminar el fitxer temporal després de la prova.

```bash
rm -f testfile
```

**Resultats orientatius:**

| Mesura | Equip de classe | Equip de casa |
|---|---:|---:|
| Lectura seqüencial | 3.200 MB/s | 520 MB/s |
| Escriptura seqüencial | 2.400 MB/s | 450 MB/s |
| Temperatura | 38 °C | 35 °C |
| Estat SMART | Correcte | Correcte |

### 3.3. Diagnòstic de xarxa

**Objectiu:** comprovar la connectivitat, la latència i la possible pèrdua de paquets.

**Eina:** `ping`.

**Ordres utilitzades:**

```bash
ip addr
ping -c 10 8.8.8.8
ping -c 10 google.com
```

La primera ordre mostra les interfícies i les adreces IP configurades. Les altres dues comproven la connectivitat amb Internet i el funcionament de la resolució DNS.

**Condicions:** realitzar les proves amb una connexió estable i sense descàrregues actives.

**Resultats orientatius:**

| Mesura | Equip de classe | Equip de casa |
|---|---:|---:|
| Paquets enviats | 10 | 10 |
| Paquets rebuts | 10 | 10 |
| Paquets perduts | 0 % | 0 % |
| Latència mínima | 12 ms | 14 ms |
| Latència mitjana | 16 ms | 19 ms |
| Latència màxima | 24 ms | 28 ms |

## 4. Comparació dels resultats

Després de realitzar les proves, es poden comparar les característiques i el rendiment dels dos equips.

| Component | Equip de classe | Equip de casa |
|---|---:|---:|
| RAM total | 32 GB | 8 GB |
| RAM utilitzada | 5,2 GiB | 2,8 GiB |
| RAM disponible | 25,1 GiB | 4,5 GiB |
| Lectura SSD | 3.200 MB/s | 520 MB/s |
| Escriptura SSD | 2.400 MB/s | 450 MB/s |
| Temperatura SSD | 38 °C | 35 °C |
| Latència mitjana | 16 ms | 19 ms |
| Pèrdua de paquets | 0 % | 0 % |

## 5. Identificació dels colls de botella

### Equip de classe

L'equip disposa de 32 GB de RAM i un SSD amb velocitats elevades de lectura i escriptura.

El consum de memòria és aproximadament del 17 %, per la qual cosa encara disposa de molts recursos lliures.

La xarxa presenta una latència mitjana de 16 ms i no hi ha pèrdua de paquets.

Per tant, no s'identifiquen colls de botella importants en les proves realitzades.

### Equip de casa

L'equip disposa de 8 GB de RAM, dels quals s'utilitzen aproximadament 2,8 GiB en repòs.

Encara que el consum inicial no és elevat, la quantitat total de memòria pot limitar l'execució simultània de diverses màquines virtuals.

El SSD presenta velocitats inferiors a les de l'equip de classe, però continua oferint un rendiment suficient per a tasques habituals.

La connexió de xarxa també presenta resultats correctes, amb una latència mitjana de 19 ms i cap paquet perdut.

Per tant, la principal limitació identificada és la quantitat de memòria RAM, especialment per a treballar amb virtualització.

## 6. Conclusió

Les proves permeten comparar el rendiment dels dos equips i identificar les seues principals limitacions.

L'equip de classe presenta millors característiques generals, especialment en memòria RAM i velocitat d'emmagatzematge, fet que el fa més adequat per a executar diverses màquines virtuals.

En canvi, l'equip de casa presenta una limitació important de memòria RAM, ja que només disposa de 8 GB. Això pot provocar problemes de rendiment quan s'executen diversos sistemes virtualitzats simultàniament.

Les proves de xarxa no mostren problemes significatius en cap dels dos equips.

En conclusió, es confirma que l'equip de casa té més limitacions per a treballar amb virtualització, mentre que l'equip de classe ofereix unes condicions més favorables.
