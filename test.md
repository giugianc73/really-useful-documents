# Archivi smeup su DB non relazionali + k48 

[TOC]

###Comitati da coinvolgere:
* Strumenti Server
* Tecnologie Server
* Integrazione
* Multienvironment

##Introduzione
Lo scopo del progetto è trovare eventuali limiti o nuove idee per la persistenza di oggetti su database non relazionali, in ottica multi piattaforma. 

###Database analizzati
* MongoDB (object database)
* Redis (key-value database)
* Neo4Js (Document-Graph database)
* Cassandra (database a colonne)
* MariaDB (database relazionale forkato da MySQL)

I seguenti database sono stati documentati nella cartella [`DB-non-relazionali`](https://labdocs.smeup.com/pages/DB-non-relazionali/):
* [MongoDB](https://labdocs.smeup.com/DB-non-relazionali/MongoDB)
* [Redis](https://labdocs.smeup.com/DB-non-relazionali/Redis)
* [OrientDB](https://labdocs.smeup.com/DB-non-relazionali/OrientDB)
* [Cassandra](https://labdocs.smeup.com/DB-non-relazionali/Cassandra)
* [Neo4Js](https://labdocs.smeup.com/DB-non-relazionali/Neo4j)

###File AS400 trasferiti
I file (tabelle SQL) esportati da AS400 dalle librerie (collection) **SMEUP_DAT** e **SMETAB** sono:



| Nome    | Dimensione | Libreria  | Descrizione 
|---------|------------|-----------|-------------
|BRARTI0F |760         |SMEUP_DAT  |Esempio di file (tabella) di anagrafica (contenente gli articoli) "semplice".
|BRENTI0F |63150       |SMEUP_DAT  |Esempio di file (tabella) di anagrafica (contenente gli enti) che può essere visto come "insieme di gruppi". Contiene infatti i clienti, i fornitori, le banche, ecc.
|JALOGT0F |7251188     |SMEUP_DAT  |Tipico file (tabella) che contiene log quindi potenzialmente con molti record. Inoltre contiene un campo molto lungo (30000 byte).
|C£ESO00F |2027        |SMEUP_DAT  |File con più chiavi.
|V5TDOC0F |753         |SMEUP_DAT  |Contiene testate di documenti. 
|V5RDOC0F |983         |SMEUP_DAT  |Contiene righe di documenti. 
|TABEL00F |81183       |SMETAB     |esempio di file (tabella) in cui sono memorizzate *tabelle Sme.UP*. Formate da un campo di 100 byte in cui vengono immagazzinate informazioni eterogenee che possono essere diverse record per record e da una sorta di tipo record che identifica il modo in cui le informazioni sono scritte nel campo "libero".

**V5TDOC0F e V5RDOC0F** Sono due file logicamente collegati. Il primo contiene testate di documenti, il secondo le righe. Quindi, ad esempio, per formare un ordina di vendita si ha un record su V5TDOC0F (la testata appunto) e n record su V5RDOC0F (le righe appunto). Ci sono due campi del V5RDOC0F che contengono le chiavi di riferimento del V5TDOC0F.

###Tool utilizzati
* **Docker** per creare delle applicazioni virtuali di database indipendenti. Sono i prototipi di database su cui vengono trasferiti i dati da AS400.
* **Pentaho Data Integration (PDI) - Kettle** software per il trasferimento dati.
* **MongoDB Compass** come client GUI ufficiale di MongoDB [link](https://www.mongodb.com/download-center/compass)
* **Mongo Shell** è possibile interrograre il database da riga di comando [link](https://docs.mongodb.com/manual/mongo/)
* **redis-cli** 
* **Redis plugin per Pentaho** per la scrittura in Redis usando Pentaho. Si scarica a questo [link](https://github.com/DanielYWoo/pentaho-di-redis-plugin)

##DB AS400
<pre>
Driver:     JTOpen(AS/400)
Url:        jdbc:as400://srvlab01.smeup.com  
Libreria:   SMEUP_DAT 
</pre>

##DB di test su Docker
Dario ha installato i seguenti database su Docker in una macchina virtuale:
```
Name: ITERBVRT013
S.O.: Ubuntu Linux 18.4 LTE versione Server
IP:   172.16.2.123
User: smeup
Pwd:  (te la dice a voce lol)
```
Sono presenti delle configurazioni di esempio lanciabili con *Docker compose*.

Database | Porta | Versione
---------|-------|----------
MongoDB  |27017  |4.0.10
Cassandra|9042   |3.11.2
Redis    |6379   |5.0.5
Neo4Js   |       |
MariaDB  |3307   |10.4.7

MariaDB è di default impostato sulla porta 3306, io l'ho rimappata sulla porta 3307.

###Docker Compose
Serve per lanciare più applicazioni Docker allo stesso tempo sullo stesso host. Si utilizza un file YAML per configurare i servizi da lanciare. In questo modo è possibile lanciare tutte le applicazioni con un unico comando. Entrati nella cartella del database che si vuole lanciare:
```
docker-compose up -d
```
Per disattivare il database:
```
docker-compose down
```

##Pentaho Data Integration - Kettle
Pentaho è una suite completa per la manipolazione, visualizzazione, gestione, analisi, trasferimento di dati da ogni fonte a ogni destinazione. Disponibile sia in Windows che Linux.

![ ](https://bluesoftware.com/wp-content/uploads/2017/01/pentaho-logo.png)  
[documentazione](https://help.pentaho.com/Documentation/8.3/Products)

La suite è strutturata in diversi tool specifici per le varie necessità. **Data integration** già noto come *Kattle* da accesso ad un motore di trasformazione, trasformazione e caricamento dati (ETL) tra due database diversi.  
Facilita il processo di migrazione, pulitura e salvataggio dati tra database con architetture differenti, rendendo il dato in un formato fruibile a terze parti.  
Dopo il download del pacchetto .zip, l'applicazione desktop si apre eseguendo il file 
```
./data-integration/spoon.sh
``` 
Prerequisito per il corretto funzionamento dell'applicazione è **Java 8** (Java 11 da problemi).  
[download](https://community.hitachivantara.com/docs/DOC-1009855-data-integration-kettle)  
[documentazione](https://help.pentaho.com/Documentation/8.3/Products/Pentaho_Data_Integration)

###Configurazione
####AS400 input
* Creare una nuova *Trasformazione* e creare una nuova connessione 
* Configurare il database in ingresso con i seguenti dati:
```
Host name:  srvlab01.smeup.com
Db name:    SMEUP_DAT
Username:   account AS400
Password:   Passoword AS400
```
Il nome del database è la libreria di AS400. 
* Se provando la connessione, il sistema va in crash, serve sostituire il driver JDBC `./lib/jt400.jar` con una versione più recente.
* Oggetti di base -> Input -> Tabella 'input: Scegliere la connessione impostata prima e immettere la query SQL desiderata. In anteprima si può vedere il risultato.

##MongoDB

###Interrogazione
Il database si può interrogare:
* attraverso la [shell](https://docs.mongodb.com/manual/mongo/) nativa di MongoDB.  
Ci si connette noto l'IP:
```
mongo "mongodb://172.16.2.123:27017"
```
Il database di default è *test*. È stato creato un database denominato *smeup* in cui sono stati trasferiti tutti i file di AS400
```
use smeup
```
Tutte le interrogazioni saranno così strutturate:
```
db.NOME_COLLECTION.comando()
```
Dove `NOME_COLLECTION` è stato fatto corrispondere al nome del file AS400 trasferito, all'atto della migrazione in Pentaho.
* attraverso la GUI nativa di MongoDB [Mongo-compass](https://docs.mongodb.com/compass/current/), che permette la una visualizzazione agevole.

###Migrazione
L'esportazione è stata effettuata seguendo i seguenti step **(trasformazioni)** in Pentaho:
* **Table input** è stata impostata la query SQL 
```sql
SELECT * FROM BRARTI0F
```
* **Select values** in questo step sono stati rinominati tutti nomi del campi rimuovendo il carattere speciale `§`
* **String operations**. I campi contenenti stringhe in un database relazionale hanno lunghezza predefinita. Questo step effettua un trim da destra di tutti gli spazi. 
* **MongoDB Output** Questo step converte la tabella relazionale in documenti JSON. Ogni record sarà un oggetto contenete tante coppie chiave/valore quanti sono i campi non vuoti del record. La preventiva rimozione degli spazi inutili nel precedente passaggio, fa si che i campi vuoti del record non vengano convertiti in coppie chiave/valore. Le impostazioni sono:
```
Host Name:           172.16.2.123 (IP macchina virtuale)
Port:                27017
Database:            smeup
Collection:          BRARTI0F (Nome file AS400 da trasferire)
Truncate collection: yes
```
È inoltre possibile impostare una struttura ad albero nel tab *Mongo documents fields*. Nella colonna *Mongo document path* è possibile definire un oggetto-padre in cui incapsulare gli altri oggetti. Lo stesso campo della colonna *Mongo document path* permette di attribuire alla chiave un nome diverso dal campo AS400 corrispondente impostando come `N` la colonna *Use field name* e inserendo il nuovo nome preceduto da un punto. Si noti come in questo modo è possibile saltare lo step 2.


Si osservi l'esempio, estratto di un record del file BRARTI0F:

|A§ARTI	|A§DEAR	|A§DEA2	|A§TIAR	|A§TPAR	|A§UNMS	|A§PESO	|A§VOLU	|A§CALT	|A§ARRI	|A§DISE	|A§BARC|
|---|	---|	---|	---|	---|	---|	---|	---|	---|	---|	---|	---|
|A01            	|ciao 17/02/2019 11:25:35 ff        	|AGGIUNTIVAaat                      	|ART  	|1	|KG	|8	|100	|               	|      |         	                    	               
    	               
```javascript
_id:"5d4957f222900c0f69c79805",
descrizione: {DEAR: "ciao 17/02/2019 11:25:35 ff",DEA2:"AGGIUNTIVAaat"},
tipo:{TIAR:"ART",TPAR:"1"},
UNMS:"KG",
PESO:8,
VOLU:100,
ARRI:null
```
* Il nome di ciascun campo è stato troncato eliminando il prefisso `A§`.
* I nomi dei campi così modificati sono diventati le chiavi del nuovo oggetto in MongoDB.
* Le stringhe sono state troncate eliminando gli spazi vuoti.
* È stata data una struttura ad albero all'oggetto in MongoDB inserendo certi campi in altri oggetti.

####Problemi con il trasferimento date:
Nei file AS400 presi in esame, le date sono salvate come intero da 8 cifre nel formato `yyyyMMdd`, tuttavia òe date non definite sono indicate con 0. Pentaho riconosce tale codifica come un intero, e come tale lo migra in MongoDB. La conversione a tipo-data nativo di MongoDB può essere effettuata in 3 passaggi:
* I campi contenenti date in formato numerico sono convertiti in stringa usando lo step *data select* tab *meta dati*
* I campi contenenti valori uguali a 0 sono posti uguali a `null` nello step *null-if*, in modo da non provocare errori durante la conversione.
* A questo punto tutti i campi di date sono o nulli o delle stringhe di 8 caratteri nel formato `yyyyMMdd`. Riusando lo step *data select* nel tab *meta-dati* è possibile imporre la conversione in data dei campi, noto il formato. È pure possibile definire il fuso è il formato.

L'output sarà un oggetto-data JavaScript definito come ```ISODate("2000-12-31T23:00:00.000+00:00")```

![](/TACPG-PD000050/img1.png)

####Annidamento
È possibile configurare Pentaho per effettuare l'annidamento di documenti provenienti da database logicamente correlati. Nell'esempio, è stata trasferita in MongoDB la JOIN di due tabelle:
* testate della fattura
* righe della fattura

Ogni testata è correlata ad un numero variabile di righe. Quindi si è fatto in modo che per ogni documento rappresentate un record della tabella delle testate, venisse annidato un array con i documenti di tutte le righe ad essa correlate:
```javascript
_id:"5d513339a2147e1d768630a5",
T§DEDO:"Previsione Cliente Italia     ",
T§DTDO:"20060209",
righe:[
   {R§NRIG:"5", R§CDOG:"A04            ",R§DSOG:"QUARTO ARTICOLO                    "},
   {R§NRIG:"10",R§CDOG:"DBL199F        ",R§DSOG:"BOLLETTINO TECNICO                 "},
   {R§NRIG:"15",R§CDOG:"A01            ",R§DSOG:"PRIMO ARTICOLO                     "}
]
```
In Pentaho:
* Lettura da tabella *testate* e tabella *righe*
* Riordino delle righe di ciascuna tabella secondo il campo da utilizzare per fare la JOIN
* Scrittura in MongoDB della tabella risultante dalla JOIN
  * In *Output Options* marcare:
    * *Truncate collection*
    * *Update*
    * *Upsert*
    * *Modifier Update* (in modo da abilitare i metodi `$set` e `$push`)
  * In *Mongo document fields* i campi delle righe sono inseriti due volte:
     * Prima si configura la struttura del documento con `$set`:
```
Mongo document path: nome_array[0]
Modifier operation:  $set
Modifier policy:     Insert
```
     * Poi si inseriscono i campi annidati nella struttura precedentemente definita con `$push`:
```
Mongo document path: nome_array[]
Modifier operation:  $push
Modifier policy:     Update
```

![Esempio annidamento](/TACPG-PD000050/joinMongo.png)

##MariaDB

###Interrogazione
Attraverso SQuirrelSQL è possibile interrogare il database, previa installazione del file JDBC di MariaDB.  
Parametri di connessione:
```
Connection type:  MariaDB
Host Name:        172.16.2.123
Database Name:    smeup
Port Number:      3307
Username:         user
Password:         password
```

###Migrazione
Il trasferimento è stato effettuato tra due database relazionali, dunque senza particolari trasformazioni.

È necessario incollare nella cartella `./data-integration/lib` il file JDBC di MariaDB prima di connettersi.

Ogni file AS400 è stato replicato:
* Così com'era con lo stesso nome.
* In aggiunta, è stato creata una copia in cui sono state convertite le date in formato `DATE` rispetto all'intero da 8 cifre contenuto in AS400. La trasformazione ha generato campi nulli laddove la data fosse stata 0. La nuova tabella è stata rinominata con il seguente formato `NOMEORIGINALE_date`.

![](/TACPG-PD000050/mariadb_pentaho.png)

I passaggi sono stati:
* **Table input** è stata impostata la query SQL 
```sql
SELECT * FROM BRARTI0F
```
* **MariaDB Output** immediata scrittura del dato così com'era in AS400.
* **Conversione campi data** in tre passaggi (i primi due propedeutici al terzo):
  * Conversione da intero a stringa (la conversione in data avviene solo da stringa).
  * Cancellazione di tutti i campi dati contenenti "0" indicante data assente (le stringhe possono solo essere del formato prestabilito `yyyyMMdd`, altriemnti il campo deve essere `<null>`).
  * Conversione in data partendo dal pattern `yyyyMMdd`.

Prima del trasferimento, è necessario creare la tabella in MariaDB. L'SQL necessario è generato automaticizzante da Pentaho cliccando apposito bottone (terzo da sinistra in alto). 

####Indici e campi univoci

Sono stati impostati anche i seguenti indici per ogni tabella, basandosi sulle viste di AS400.

| Nome    | Indice     | Campi univoci          | Problemi di encoding?|
|---------|------------|------------------------|--------------------- |
|BRARTI0F |BRARTI0L    |A§ARTI                  | Duplicati per case insensitive
|BRENTI0F |BRENTI8L    |E§IDOJ                  | Caratteri esadecimali non supportati
|JALOGT0F |JALOGT4L    |J1IDOG                  |
|C£ESO00F |C£ESO06L    |P£TPRC, P£TP01, P£CD01, P£TP02, P£CD02, P£TP03, P£CD03, P£NUMP, P£DTIN, P£SEQU |
|C£ESO00F |C£ESO09L    |P£TPRC, P£CD01, P£CD02, P£NUMP, P£DTIN, P£SEQU |
|V5TDOC0F |V5TDOC0L    |T§TDOC, T§NDOC          |
|V5RDOC0F |V5RDOC0L    |R§TDOC, R§NDOC, R§NRIG  |
|TABEL00F |TABEL01L    |TTSETT, TTELEM          | Caratteri esadecimali non supportati

####Encoding
Molti errori sono stati causati dall'encoding di default impostato su MariaDB, "atin1_swedish_ci" che risulta *case insensitive*. Per questo il corretto trasferimento di alcuni caratteri veniva impedito e  si creavano conflitti all'atto di creazione degli indici. Per risolvere il problema si è impostato il server di default su 
```sql
SET collation_server = 'utf8_bin'; 
```
* [Encoding MariaDB](https://mariadb.com/kb/en/library/setting-character-sets-and-collations/)

####Date

La generazione delle tabelle `NOME_date` è stata fatta manualmente in SQuirrelSQL perché Pentaho considera solamente il formato `DATETIME`. Dunque prima di lanciare l'SQL necessario alla generazione della tabella, esso viene copiato, tutti i formati `DATETIME` vengono manualmente cambiati `DATE` e il comando è infine lanciato in SQuirrelSQL.

Durante il trasferimento, il sistema non da errori. Si suppone che avvenga un casting automatico all'interno di MariaDB. 

Il risultato è che ogni campo connettente un intero da 8 cifre nel formato `yyyyMMdd` è stato convertito in formato SQL `DATE` del tipo `yyyy-MM-dd`.

##Redis
La scrittura in Redis è stata effettuata convertendo ogni record in una stringa JSON.
[plugin Pentaho](https://github.com/DanielYWoo/pentaho-di-redis-plugin)

##Cassandra 
###Interrogazione
Il modo migliore trovato per interagire con il database è attraverso la shell nativa `cqlsh` versione 5.0.1. Si scarica solo installando Cassandra in toto (si veda [link](https://www.vultr.com/docs/how-to-install-apache-cassandra-3-11-x-on-ubuntu-16-04-lts) per Ubuntu).  
```
cqlsh $CQLSH_HOST 172.16.2.123
```
Si è poi provveduto a creare il database detto *KEYSPACE* in Cassandra denominato `smeup`:
```sql
CREATE KEYSPACE smeup
  WITH REPLICATION = { 
   'class' : 'SimpleStrategy', 
   'replication_factor' : 1 
  };
```

###Migrazione
Ogni file AS400 è stato replicato:
* Così com'era con lo stesso nome.
* In aggiunta, è stato creata una copia in cui sono state convertite le date in formato `TIMESTAMP` rispetto all'intero da 8 cifre contenuto in AS400. La trasformazione ha generato campi nulli laddove la data fosse stata 0. La nuova tabella è stata rinominata con il seguente formato `NOMEORIGINALE_date`.

![](/TACPG-PD000050/cassandra_pentaho.png)

L'esportazione è stata effettuata seguendo i seguenti step **(trasformazioni)** in Pentaho:
* **Table input** è stata impostata la query SQL 
```sql
SELECT * FROM BRARTI0F
```
* **Select values** in questo step sono stati rinominati tutti nomi del campi rimuovendo il carattere speciale `§`.
* **Cassandra Output** immediata scrittura del dato così com'era in AS400.
* **Conversione campi data** in tre passaggi (i primi due propedeutici al terzo):
  * Conversione da intero a stringa (la conversione in data avviene solo da stringa).
  * Cancellazione di tutti i campi dati contenenti "0" indicante data assente (le stringhe possono solo essere del formato prestabilito `yyyyMMdd`, altriemnti il campo deve essere `<null>`).
  * Conversione in data partendo dal pattern `yyyyMMdd`.
* **Cassandra Output** scrittura del dato con date convertite in `TIMESTAMP`.

####Date

Cassandra supporta, analogamente ai database relazionali, i tipi di dato:
* DATE
* DATETIME
* TIMESTAMP

Pentaho purtroppo non fa questa distinzione. Se inesistente, la creazione della *column family* (analogo tabella in Cassandra) viene fatta da Pentaho prima di iniziare la migrazione. I campi data convertiti, vengono inizializzati come `TIMESTAMP`.

Sono stati fatti tentativi per inizializzare la *COLUMN FAMILY* manualmente, ponendo le colonne rappresentati delle date in formato `DATE`. Tuttavia, Cassandra non supporta il casting implicito tra `TIMESTAMP` e `DATE`, e all'atto di trasferimento segnala un errore di incompatibilità nel tipo di campo.

Cassandra supporta di verse funzioni di manipolazione date. Usando `toDate()` è possibile manipolare campi `TIMESTAMP` come fosse `DATE`:
```sql
SELECT toDate(DT01) as DT01  from smeup.BRARTI0F_date;
```

* [Articolo funzioni Time](https://docs.datastax.com/en/archived/cql/3.3/cql/cql_reference/timeuuid_functions_r.html)

#### GitHub
https://github.com/smeup/smeup_migrations
