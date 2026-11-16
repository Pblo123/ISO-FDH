
# Tests de diagnòstic dels equips informàtics

## 1. Introducció

L'objectiu d'aquesta activitat és realitzar proves de diagnòstic en els ordinadors de classe i de casa, utilitzant Linux Mint com a sistema operatiu.

Es comprovarà el rendiment de la memòria RAM, l'estat i la velocitat dels discos SSD i la connectivitat de xarxa.

Finalment, es compararan els resultats per a identificar possibles colls de botella i determinar si les limitacions detectades inicialment es confirmen.

## 2. Característiques dels equips

### Equip de classe

- Processador: Intel Core Ultra 5 125H.
- Memòria RAM: 32 GB.
- Emmagatzematge intern: SSD NVMe d'1 TB.
- Emmagatzematge extern: SSD d'1 TB amb Linux Mint instal·lat.

### Equip de casa

- Processador: Intel Core i5-9400F.
- Memòria RAM: 8 GB.
- Targeta gràfica: NVIDIA GeForce GT 710.
- Emmagatzematge intern: SSD d'1 TB.
- Emmagatzematge extern: el mateix SSD d'1 TB utilitzat en l'equip de classe, amb Linux Mint instal·lat.

**Important:** Linux Mint s'executa des del SSD extern en tots dos equips. Per tant, el sistema operatiu i el dispositiu d'emmagatzematge extern són els mateixos, fet que permet comparar el comportament dels dos ordinadors en condicions semblants.

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

### 3.2. Diagnòstic dels discos

**Objectiu:** comprovar l'estat dels discos i mesurar les velocitats de lectura i escriptura.

**Eines:** `smartmontools` i `fio`.

**Instal·lació:**

```bash
sudo apt update
sudo apt install smartmontools fio
```

**Identificació dels discos:**

```bash
lsblk -o NAME,SIZE,MODEL,TRAN,ROTA
```

Aquesta ordre permet identificar els discos interns i externs, la capacitat i el tipus de connexió.

**Comprovació de l'estat SMART:**

```bash
sudo smartctl -a /dev/sdX
```

Cal substituir `/dev/sdX` pel dispositiu corresponent. En el cas d'un disc NVMe, es pot utilitzar `/dev/nvme0n1`.

**Prova de velocitat:**

```bash
fio --name=test \
--filename=testfile \
--size=1G \
--bs=1M \
--rw=readwrite \
--direct=1 \
--iodepth=16 \
--runtime=30 \
--time_based \
--group_reporting
```

Mesura el rendiment de lectura i escriptura del disc seleccionat.

**Condicions:** realitzar les proves per separat en cada disc, sense altres operacions importants d'emmagatzematge.

En el cas de l'SSD extern, s'utilitzarà el mateix dispositiu en tots dos ordinadors per a comparar el rendiment de la connexió USB.

**Resultats orientatius:**

| Mesura | Equip de classe | Equip de casa |
|---|---:|---:|
| SSD intern | NVMe | SSD |
| Capacitat interna | 1 TB | 1 TB |
| SSD extern | 1 TB | 1 TB |
| Lectura SSD extern | 450 MB/s | 350 MB/s |
| Escriptura SSD extern | 400 MB/s | 320 MB/s |
| Estat SMART | Correcte | Correcte |

*Les velocitats de l'SSD extern són exemples orientatius, no mesures reals. Encara que siga el mateix dispositiu, la connexió USB i els controladors poden influir en el resultat.*

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

Les proves permeten comparar els dos equips tenint en compte que utilitzen el mateix SSD extern i el mateix sistema operatiu.

| Component | Equip de classe | Equip de casa |
|---|---|---|
| Processador | Intel Core Ultra 5 125H | Intel Core i5-9400F |
| RAM | 32 GB | 8 GB |
| SSD intern | 1 TB NVMe | 1 TB SSD |
| SSD extern | 1 TB | 1 TB (mateix dispositiu) |
| Sistema operatiu | Linux Mint | Linux Mint |
| Lectura SSD extern | 450 MB/s | 350 MB/s |
| Escriptura SSD extern | 400 MB/s | 320 MB/s |
| Latència mitjana | 16 ms | 19 ms |

## 5. Identificació dels colls de botella

### Equip de classe

Disposa de 32 GB de RAM i un SSD intern NVMe d'1 TB. Aquestes característiques permeten treballar amb diverses aplicacions i màquines virtuals simultàniament.

El sistema operatiu s'executa des de l'SSD extern, per la qual cosa el rendiment també depén de la connexió USB.

### Equip de casa

Disposa de 8 GB de RAM i un SSD intern d'1 TB. La memòria RAM pot representar una limitació quan s'executen diverses màquines virtuals simultàniament.

No obstant això, el rendiment del processador i del SSD s'ha de valorar a partir de les proves, sense considerar que l'equip siga inferior en tots els aspectes.

### Comparació de l'emmagatzematge extern

Com que s'utilitza el mateix SSD extern amb Linux Mint en els dos ordinadors, les diferències de velocitat poden estar relacionades amb els ports USB, els controladors o altres característiques de cada equip.

Per tant, és important comparar les velocitats mesurades abans d'identificar un possible coll de botella.

## 6. Conclusió

Les proves permeten analitzar el rendiment dels dos equips en condicions semblants, utilitzant el mateix sistema operatiu i el mateix dispositiu d'emmagatzematge extern.

La principal diferència de maquinari és la memòria RAM, amb 32 GB en l'equip de classe i 8 GB en el de casa. Això pot influir especialment en l'execució simultània de màquines virtuals.

També s'ha de tindre en compte que l'equip de classe disposa d'un SSD intern NVMe, mentre que el de casa utilitza un SSD intern diferent.

En conclusió, no es pot determinar que un equip siga millor en tots els aspectes sense analitzar els resultats de cada prova per separat.
