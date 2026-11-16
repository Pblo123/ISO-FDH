
# Tests de diagnòstic dels equips informàtics

## 1. Introducció

L'objectiu d'aquesta activitat és realitzar proves de diagnòstic en els ordinadors de classe i de casa, utilitzant Linux Mint com a sistema operatiu.

Es comprovarà el rendiment de la memòria RAM, l'estat i la velocitat dels discos SSD i la connectivitat de xarxa.

Finalment, es compararan els resultats per a identificar possibles colls de botella.

## 2. Característiques dels equips

### Equip de classe

- Processador: Intel Core Ultra 5 125H.
- Memòria RAM: 32 GB.
- Emmagatzematge: SSD de 2 TB.

### Equip de casa

- Processador: Intel Core i5-9400F.
- Memòria RAM: 8 GB.
- Targeta gràfica: NVIDIA GeForce GT 710.
- Emmagatzematge: SSD d'1,5 TB.

## 3. Proves de diagnòstic

### 3.1. Diagnòstic de memòria RAM

**Objectiu:** comprovar la quantitat de memòria disponible i el seu consum.

**Eines:** `free` i `htop`.

**Ordres utilitzades:**

```bash
free -h
```

Mostra la memòria total, utilitzada i disponible.

```bash
htop
```

Permet observar en temps real el consum de RAM i CPU.

**Condicions:** executar les ordres amb el sistema iniciat i sense aplicacions exigents obertes.

**Resultats estimats:**

- Equip de classe: 32 GB de RAM, amb un consum aproximat de 4-6 GB en repòs.
- Equip de casa: 8 GB de RAM, amb un consum aproximat de 2-3 GB en repòs.

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
sudo smartctl -a /dev/sda
```

Permet consultar les dades SMART, la temperatura i l'estat del dispositiu.

**Prova de velocitat:**

```bash
fio --name=test --filename=testfile --size=1G --bs=1M --rw=readwrite --direct=1 --iodepth=16 --runtime=30 --time_based --group_reporting
```

Mesura el rendiment de lectura i escriptura del disc.

**Condicions:** realitzar la prova amb el disc sense altres operacions importants i eliminar el fitxer temporal després de la prova.

```bash
rm -f testfile
```

**Resultats:** pendents de realitzar en els dos equips.

### 3.3. Diagnòstic de xarxa

**Objectiu:** comprovar la connectivitat, la latència i la possible pèrdua de paquets.

**Eina:** `ping`.

**Ordres utilitzades:**

```bash
ip addr
```

Mostra les interfícies i les adreces IP configurades.

```bash
ping -c 10 8.8.8.8
```

Comprova la connectivitat amb Internet i mostra el temps de resposta.

```bash
ping -c 10 google.com
```

Comprova també el funcionament de la resolució DNS.

**Condicions:** realitzar les proves amb una connexió estable i sense descàrregues actives.

**Resultats:** pendents de realitzar en els dos equips.

## 4. Comparació dels resultats

| Component | Equip de classe | Equip de casa |
|---|---|---|
| RAM total | 32 GB | 8 GB |
| RAM utilitzada | Pendent | Pendent |
| Velocitat SSD | Pendent | Pendent |
| Estat SMART | Pendent | Pendent |
| Latència de xarxa | Pendent | Pendent |
| Pèrdua de paquets | Pendent | Pendent |

## 5. Identificació dels colls de botella

**Equip de classe:**

Disposa de 32 GB de RAM i un SSD de 2 TB. En principi, ofereix recursos suficients per a executar diverses màquines virtuals simultàniament.

**Equip de casa:**

Disposa de 8 GB de RAM, per la qual cosa pot presentar limitacions en executar diverses màquines virtuals. La targeta gràfica NVIDIA GT 710 també és limitada per a tasques gràfiques exigents.

La velocitat i l'estat del SSD s'hauran de confirmar mitjançant les proves realitzades.

## 6. Conclusió

Les proves permetran comparar el rendiment dels dos equips i comprovar si les limitacions de maquinari identificades es confirmen.

Inicialment, l'equip de casa presenta una major limitació de memòria RAM, mentre que l'equip de classe disposa de millors recursos per a treballar amb virtualització.

La conclusió definitiva dependrà dels resultats obtinguts en les proves de memòria, disc i xarxa.
