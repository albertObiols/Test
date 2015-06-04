#P2P

######node = peer

##1. Introducció
- Arquitectura de **Sistemes i Aplicacions Distribuïts**.
- Cada **peer** contribueix amb dades i recursos.
- Ús més freqüent per la compartició d'arxius.

##2. Característiques
- Tots els **peers** tenen el mateix rol (funcions i responsabilitats).
- Cada peer pot comunicar-se amb qualsevol altre peer sense necessitat d'un servidor. (Purament P2P solen ser rares, normalment solen usar algun element basat en client-server)
- Cada peer ha de poder localitzar els altres:
	- **DNS** Domain Name System, directori on buscar els demés peers.
	- **DHT** Taula Hash Distribuïda, on cada peer manté una part de la hash actualitzada amb els seus peers veïns.

##3. Taula Hash Distribuïda (DHT)
Els nodes s'han de coordinar amb uns pocs altres del sistema de l'ordre de **log(n)**, així que pocs nodes són afectats pels canvis en el sistema.

###3.1 Característiques
- Autonomía i Descentralització (peers formen el sistema col·lectivament)
- Tolerància a fallades (fiable encara que els peers entrin i surtin contínuament)
- Escalabilitat (eficient fins i tot amb milers de nodes)

###3.2 Estructura
- **Esquema de partició d'espai de claus:** s'otorga a cada node un rang de claus contigu.
	- **Funció Hash Consistent:** té la propietat de que al afegir o esborrar un node tan sols canvia el conjunt de claus dels nodes veïns, deixant la resta sense alterar.
	- Es defineix una funció de distància entre claus (res a veure amb distància geogràfica o latència).

- **Xarxa superposada (overlay network): ** és com els nodes es connecten entre ells formant una xarxa que els permet trobar el propietari d'una clau concreta en l'espai de claus.
	- Cada node manté un conjunt d'enllaços a nodes veïns o taules d'enrutament.
	- El veinatge dels nodes està definit segons la topologia de la xarxa
	- L'algoritme per trobar nodes veins **Nearest Neighbour**
	- Algoritmes que exploten l'estructura de la xarxa per fer broadcast. Usats per aplicacions per fer multicast

##4. Seguretat P2P
Hem d'assegurar que el peer és realment qui diu ser:

-	En registrar-se al servidor, aquest li proporciona una clau i el peer l'ha de presentar a cada iteració.

##5. Middleware i Frameworks
- Chord (DHT)
- Tapestry (DHT)
- Pastry (DHT)
- JXTA (Framework)

##6. Exemple P2P: BitTorrent
###6.1 Característiques
- Sistema de distribució de contingut multimèdia
	- Fitxers grans.
- Centrat en la descàrrega no en la cerca
	1. Un sol peer publica el fitxer.
	2. Múltiples peers el poden descarregar.
	3. Els peers comparteixen peces que ja han descarregat.
- Es fa servir l'estrategia *quid pro quo*
	- Compartiré amb tu si tu comparteixes amb mi.
	- De tant en tant es deixa a peers egoistes descarregar.
	- Necessari per iniciar el procés de descàrrega.

###6.2 Funcionament
1. Localitzar el **.torrent** (fitxer de METADATA).
2. Aquest fitxer conté:
	1. Nom
	2. Valor de HASH
	3. URL del tracker(servidor)
	4. Longitud 
	5. ...
3. Es fa la petició al **Tracker** per afegir-nos al torrent.
4. El tracker selecciona una llista aleatòria de peers per a fer la descàrrega:
	- **Seeds:** peers que ja tenen el fitxer sencer.
	- **Leechers:** peers que encara els hi falten peces per descarregar.
5. Contactar amb els peers de la llista per demanar peces.
6. Formar part de la xarxa P2P i participar tambè.

###6.3 Peces
- Cada fitxer es descomposa en peces:
	- Normalment de mida 256 Kbytes.
	- Es descarreguen en paral·lel.
	- Avisar de peces ja descarregades a la llista de peers pròpia.
	- S'envien peces a peers mentres es segueix descarregant altres.

- Selecció de peces a **descarregar:**
	- Es seleccionen aleatòriament al inici.
	- Es prioritzen les més poc freqüents a la xarxa, així hi hauran més pels demés.

- Selecció de peces a **enviar:**
	- Es seleccionen fins a 4 peers per a enviar la peça amb la máxima tasa de descàrrega.
	- Periòdicament es va recalculant la tasa de descàrrega de les peces que s'envien.

- Optimistic Upload
	- Cada 30 segons selecciona un peer aleatori i se li envia una peça que no tingui.
	- Es segueix buscant els peers més rápids.


__________________

#HADOOP

##1. Instroducció
Dissenyat per Google per poder donar resposta a l'alta demanda de requeriments del seu motor de cerca.

##2. Característiques
- Hadoop es basa en el treball fet per google als inicis de 2000.
- Aproximació al problema de la computació distribuïda:
	- Robust a fallades parcials del sistema
	- Recuperació de dades
	- Recuperació d'un component
	- Consistent
	- Escalable
- Idea básica: distribuir les dades tan bon punt siguin guardades al sistema.
- No es necessita transferir dades per la xarxa per començar a processar un node.

##3. Conceptes Clau
- Aplicacions escrites a alt nivell. (no ens hem de preocupar de sincronització, dependències, sincronitzacións...)
- Els nodes es comuniquen entre ells tant poc com sigui possible.
- Les dades es reparteixen a priori entre les màquines.
- La computació passa allà on estan les dades sempre que sigui possible.
- Les dades estan replicades varies vegades en el sistema per augmentar la fiablitat i la disponibilitat.

##4. Components de Hadoop
- Bàsics:
	- **HDFS** (Hadoop Distributed File System)
	- **MapReduce Software Framework**

- Clúster: és un conjunt de nodes fent funcionar el HDFS i el MapReduce.

- Node: és una máquina individual.

- Otros:
	- Pig, Hive, HBase, Flume, Oozie, Sqoop, Mahout ...

##5. Tractament de fallades
- Si un node falla, el máster ho detecta i reassigna la seva tasca a un altre node del sistema.
- Si un node caigut es torna a encendre, s'afegeix automàticament al sistema y se li assignen noves tasques.
- Si un node esta executant una tasca amb un rendiment per sota de l'esperat, el màster pot executar una altre instància d'aquesta mateixa tasca en un altra node, i els resultats del primer que acabin seran els que s'utilitzaran.
- Tornar a encendre una tasca no implica comunicació amb nodes que estiguin treballant amb altres porcions de dades.

##6. HDFS
- És el responsable d'emmagatzemar les dades en el clúster.
- Els fitxers de dades són dividits en blocs i repartits a través dels múltiples nodes en el clúster.
- Cada bloc es replicat vàries vegades:
	- Per defecte, 3 cops.
	- Les repliques es guarden en diferents nodes.
	- Això ens dona fiabilitat i disponibilitat.

###6.1 Com funciona?
- És un sistema de fitxers escrits en JAVA.
- Situat a sobre de qualsevol sistema de fitxers natiu.
- Ens dona emmagatzemament redundant per grans quantitats de dades fent servir qualsevol tipus d'ordinador.
- Està optimitzat per fitxers grans i lectures consecutives (milions de fitxers de més de 100MB).

###6.2 Com es guarden els fitxers?
- Dividits en blocs.
- Distribuïts a diverses màquines al carregar-se.
- Diferents blocs del mateix fitxer es guarden a diferents màquines.
- Els blocs es repliquen a diverses màquines, per defecte 3.
- **NameNode** és l'encarregat de mantenir la informació de quins blocs formen un fitxer i a quines maquines estan situats aquests blocs.

###6.3 Com posar i treure dades a l'HDFS
- API de Hadoop (command line):
	- Còpia de local a HDFS:
``hadoop fs -copyFromLocal local dir /hdfs dir``
	- Còpia de HDFS a local:
``hadoop fs -copyToLocal /hdfs dir local dir``

###6.4 Exemple de funcionament complet
Fitxers de dades:
>/user/eloi/foo.txt
>/user/eloi/bar.txt

Es creen els blocs per cada fitxer:
>foo - blocs: f1, f2, f4
>bar - blocs: b3, b5

Node|Blocs|
:--:|------
N1| f1, f2
N2| f1, b5
N3| b3, b5, f2
N4| f1, f4, b5, b3
N5| f4, f2
N6| b3, f4

- Com podem observar els blocs estan repartits entre les diferents màquines i replicats 3 cops.
- **NameNode** guarda la metadata dels fitxers desats a HDFS.
- **DataNode** guarden els blocs. Cada bloc es replica 3 cops.

Quan una aplicació vol llegir el fitxer **bar.txt** es comunica amb el NameNode per saber quins blocs forment el fitxer i en quin DataNode es troben. Llavors es comunica directament amb el node (N2, N3) per a llegir les dades.


##7. MapReduce
- És el sistema utilitzat per processar les dades en el clúster de Hadoop.
- Consisteix en dues fases: **Map** i **Reduce**.
- Cada operació de Map es du a terme en una porció del total de dades. Normalment un sol bloc.
- Un cop totes les operacions de Map son dutes a terme, el sistema de MapReduces distribueix automáticament els resultats intermitjos cap als nodes que faran l'operació de Reduce.

###7.1 Com funciona?
- Distribueix les tasques entre els diferents nodes.
- **Map: ** processarem les dades i realitzarem operacions costoses.
- **Reduce: ** ajuntarem la sortida i realitzarem operacions trivials.

###7.2 Característiques
- Paral·lelització contínua i distribució automàtica.
- Tolerant a fallades.
- Eines per monitoritzar el procés.
- Abstracció per a programadors (Ex: Java o Python).
- Només cal definir les funcions Map i Reduce, amaga tasques que passen per sota.

##8. Instal·lació d'un clúster Hadoop
- Normalment ho fa l'administrador del sistema.
- La forma més simple és usant la distribució de **Cloudera** que inclou *Apache Hadoop CDH*.

##9. Serveis funcionant en el Clúster
- **NameNode: ** manté METADATA del HDFS.
- **NameNode Secundari: ** fa funcions de manteniment del NameNode. (No backup)
- **DataNode: ** encarregat d'emmagatzemar els blocs de dades del HDFS.
- **JobTracker: ** gestiona els treballs de MapReduce, distribueix tasques individuals.
- **TaskTracker: ** responsable d'instanciar i monitoritzar tasques de Map i Reduce individuals.

![alt text][logo]
[logo]: http://cfile25.uf.tistory.com/image/1762633B4F571A051B3CE3

##10. Comptar paraules
- Comptar paraules d'un text és especialment complexe si tenim un gran volum de dades.
- És una tasca seqüencial, però altament paral·lelitzable.
- Si distribuïm les dades, podem fer-ho sense problemes.
- Hadoop es molt bona eina per fer-ho.

1. Primer cal descomposar les dades (Hadoop ho fa automàticament).
2. Enumerem totes les paraules (sense sumar-les) al Map processant línia a línia.
3. Hadoop ordenará la sortida per clau i la transmetrà al procés Reduce.
4. A la funció Reduce sumarem totes les dades amb una mateixa clau.

__________________

#SPARK
##1. Introducció
Dissenyat per donar resposta a l'anàlisi de dades en entorns BigData

##2. Motivació
- Dissenyar un model de programació que suporti una gamma més àmplia d'aplicacions MapReduce, mantenint la tolerància a fallades.
- **MapReduce** és ineficient per a programes de múltiples passades que no requereixen de massa intercanvi de dades entre operacions paral·leles.
- Aquests programes són força comuns a tasques d'anàlisi de dades massives:
	- Algorismes iteratius, d'aprenentage automàtic i algorismes de grafs.
	- Mineria de dades interactiva, on l'usuari carrega dades a memòria i consulta repetidament.
	- Aplicacions d'Streaming que mantenen un estat agregat al llarg del temps.

##3. Característiques
- MapReduce es basa en fer córrer una sèrie de tasques on cada una llegeix d'un disc i torna a escriure en ell, i fer-ho de forma replicada té un cost significant.
- **Spark** ofereix una abstracció anomenada *Resilient Distributed Datasets (RDD)*
	- RDD es guarda en memòria entre les diferents peticions sense necessitar de replicació.
	- En cas de fallada es reconstrueixen les dades perdudes fent servir traçabilitat.
	- Cada RDD *recorda* com va ser construit a partir dels altres datasets (a partir de maps, join, groupby) per tal de reconstruir-se.

