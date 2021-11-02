# Accessi nativi in SPL

Implementare nell'interprete SPL delle istruzioni che vadano ad eseguire i metodi del prototipo accessi nativi.
Qualcosa tipo, dato questo codice:
<pre>
fun t02_findTwoAfterErbuscWithSetll4AndReadE3() {  
        val dbFile = dbManager!!.openFile(libName!!, MUNICIPALITY_TABLE_NAME)  
        assertTrue(dbFile!!.setll(buildMunicipalityKey("IT", "LOM", "BS", "ERBUSC")))  
        assertEquals("ERBUSCO", getMunicipalityName(dbFile.readEqual(buildMunicipalityKey("IT", "LOM", "BS")).record))  
        assertEquals("ESINE", getMunicipalityName(dbFile.readEqual(buildMunicipalityKey("IT", "LOM", "BS")).record))  
        assertEquals("FIESSE", getMunicipalityName(dbFile.readEqual(buildMunicipalityKey("IT", "LOM", "BS")).record))  
        dbManager!!.closeFile(libName!!, MUNICIPALITY_TABLE_NAME)  
    }  
</pre>

qualcosa tipo questo (ma è da capire come gestire i valori di ritorno):  

<pre>
OPEN MUNICIPALITY
SETLL IT LOM ERBUSC 
READE IT LOM BS  
READE IT LOM BS 
READE IT LOM BS 
CLOSE MUNICIPALITY
</pre>

Gli scopi del progetto sono molteplici, di ampio spettro e coinvolgono più fronti/attori in quanto vertono su: 

##### Visibilità
1. percezione costante dello stato d'avanzamento del progetto Jariko (Misurabilità)
2. implementazioni e codici operativi già presenti (Funzionalità)
3. modularità e separazione dei compiti (Disaccoppiamento)

##### Potenzialità
1. Implementazioni future (Scalabilità)
2. Dialogo con altre tecnologie (Integrazione)
3. Estendibile ad un nuovo linguaggio/sintassi da intepretare (Estendibilità) 

##### Testabilità
1. Facililità scrittura test e sviluppo di nuove funzioni
2. Estensione ai "non addetti ai lavori" della testabilità delle funzioni presenti (per chi non conosce linguaggi di alto livello)

Infine, oltre a quanto sopra, ovviamente l'iprescindibile valore intrinseco della ricerca.


## Riferimenti:
### Prototipo accessi nativi
https://github.com/smeup/DBNativeAccess  
https://mauer.smeup.com/TACPG-PD000139/RapportoFinale  

### Interprete SPL  
https://github.com/smeup/interpreter-runtime-proto  

## Proposta (Franco Parodi)   
### Sintassi SPL

Una possibile sintassi per poter creare test per il prototipo di linguaggio interpretato:
```sh
OPEN <TableName> //Apertura tabella
RECORD <FieldName1> <FieldName2> <FieldNameN> //Campi di ritorno del record (non specificato=tutti i campi)
KEY1 <Value1> <Value2> <ValueN> //Definizione chiave e nome (valori o campi)
CHAIN <KeyName> //Accesso per chiave
ASSERTEQ RECORD @Empty //Condizione (prefisso @ per valori costanti riservati, tipo @Empty, @True...)
KEY2 <Value1> <Value2> //Definizione chiave (valori o campi)
CHAIN <KeyName> //Accesso per chiave
ASSERTEQ RECORD.<FieldName> <ValueToCompareWith> //Condizione
CLOSE <TableName> //Chiusura tabella
```
**N.B:** _KEYn_ per semplicità è sia codice operativo (definisce che si tratta di definizione di chiave/i), sia il nome stesso della chiave da utilizzare nell'operazione di lettura successiva.

Come valore di ritorno di un'operazione (OPEN, SETLL, CHAIN, READ...) si può avere una variabile Boolean, String, o Lista e va specificato con il segno di ">" (maggiore).
Diversamente si può non specificare ed assumere di avere sempre un valore di ritorno predefinito contestualmente ai codici operativi (OPEN -> boolean, SETLL -> boolean, CHAIN -> lista campi, READ -> lista campi...) 
```sh
OPEN <TableName> > <BooleanVar>
SETLL <KeyName> > <BooleanVar>
ASSERTEQ SETLL @True
CHAIN <KeyName> > <Record>
ASSERTEQ RECORD @Empty
READ <KeyName> > <Record>
ASSERTEQ RECORD @Empty
...
```

#### Sintassi SPL-TEST applicata a casi reali (test del progetto DBNativeAccess)

```sh
@Test
fun findRecordsIfChainWithExistingKey() {
    val dbFile = dbManager!!.openFile(libName!!, TSTTAB_TABLE_NAME)
    val chainResult = dbFile!!.chain("XXX")
    assertEquals("XXX", chainResult.record["TSTFLDCHR"])
    assertEquals("123.45", chainResult.record["TSTFLDNBR"])
    dbManager!!.closeFile(libName!!, TSTTAB_TABLE_NAME)
}
OPEN TSTTAB_TABLE_NAME 
KEY1 "XXX"
RECORD TSTFLDCHR TSTFLDNBR 
CHAIN KEY1  
ASSERTEQ RECORD.TSTFLDCHR "XXX"
ASSERTEQ RECORD.TSTFLDNBR "123.45"
CLOSE TSTTAB_TABLE_NAME
```
```sh
@Test
fun doesNotFindRecordsIfChainWithNotExistingKey() {
    val dbFile = dbManager!!.openFile(libName!!, TSTTAB_TABLE_NAME)
    assertTrue(dbFile!!.chain("XYZ").record.isEmpty())
    dbManager!!.closeFile(libName!!, TSTTAB_TABLE_NAME)
}
OPEN TSTTAB_TABLE_NAME
KEY1 "XYZ"
CHAIN KEY1  
ASSERTEQ RECORD @Empty
CLOSE TSTTAB_TABLE_NAME
```

```sh
@Test
fun findRecordsIfChainWithExistingKey() {
    val dbFile = dbManager!!.openFile(libName!!, TST2TAB_TABLE_NAME)
    val key2 = listOf(
         RecordField("TSTFLDCHR", "ABC"),
         RecordField("TSTFLDNBR", "12.00")
    )
    val chainResult = dbFile!!.chain(key2)
    assertEquals("ABC", chainResult.record["TSTFLDCHR"])
    assertEquals("12.00", chainResult.record["TSTFLDNBR"])
    assertEquals("ABC12", chainResult.record["DESTST"])
    dbManager!!.closeFile(libName!!, TST2TAB_TABLE_NAME)
}
OPEN TST2TAB_TABLE_NAME 
KEY2 "ABC" "12.00"
RECORD TSTFLDCHR TSTFLDNBR DESTST
CHAIN KEY2
ASSERTEQ RECORD.TSTFLDCHR "ABC"
ASSERTEQ RECORD.TSTFLDNBR "12.00"
ASSERTEQ RECORD.DESTST "ABC12"
CLOSE TSTTAB_TABLE_NAME
```

```sh
@Test
fun doesNotFindRecordsIfChainWithNotExistingKey() {
    val dbFile = dbManager!!.openFile(libName!!, TST2TAB_TABLE_NAME)
    val key2 = listOf(
        RecordField("TSTFLDCHR", "ZZZ"),
        RecordField("TSTFLDNBR", "12")
    )
    assertTrue(dbFile!!.chain(key2).record.isEmpty())
    dbManager!!.closeFile(libName!!, TST2TAB_TABLE_NAME)
}
OPEN TST2TAB_TABLE_NAME
KEY2 "ZZZ" "12"
CHAIN KEY2  
ASSERTEQ RECORD @Empty
CLOSE TSTTAB_TABLE_NAME
```

```sh
@Test
fun t01_notFindErbascoButFindErbuscoWithChain() {
    val dbFile = dbManager!!.openFile(libName!!, MUNICIPALITY_TABLE_NAME)
    val chainResult1 = dbFile!!.chain(buildMunicipalityKey("IT", "LOM", "BS", "ERBASCO"))
    assertEquals(0, chainResult1.record.size)
    val chainResult2 = dbFile.chain(buildMunicipalityKey("IT", "LOM", "BS", "ERBUSCO"))
    assertEquals("ERBUSCO", getMunicipalityName(chainResult2.record))
    dbManager!!.closeFile(libName!!, MUNICIPALITY_TABLE_NAME)
}
OPEN MUNICIPALITY_TABLE_NAME 
RECORD COMUNE
KEY1 "IT" "LOM" "BS" "ERBASCO"
CHAIN KEY1
ASSERTEQ RECORD @Empty
KEY1 "IT" "LOM" "BS" "ERBUSCO"
CHAIN KEY1
ASSERTEQ RECORD.COMUNE "ERBUSCO"
CLOSE MUNICIPALITY_TABLE_NAME
```

```sh
@Test
fun t02_findTwoAfterErbuscWithSetll4AndReadE3() {
    val dbFile = dbManager!!.openFile(libName!!, MUNICIPALITY_TABLE_NAME)
    assertTrue(dbFile!!.setll(buildMunicipalityKey("IT", "LOM", "BS", "ERBUSC")))
    assertEquals("ERBUSCO", getMunicipalityName(dbFile.readEqual(buildMunicipalityKey("IT", "LOM", "BS")).record))
    assertEquals("ESINE", getMunicipalityName(dbFile.readEqual(buildMunicipalityKey("IT", "LOM", "BS")).record))
    assertEquals("FIESSE", getMunicipalityName(dbFile.readEqual(buildMunicipalityKey("IT", "LOM", "BS")).record))
    dbManager!!.closeFile(libName!!, MUNICIPALITY_TABLE_NAME)
}
OPEN MUNICIPALITY_TABLE_NAME 
KEY1 "IT" "LOM" "BS" "ERBUSC"
KEY2 "IT" "LOM" "BS"
RECORD COMUNE
SETLL KEY1
ASSERTEQ SETLL @True
READE KEY2  
ASSERTEQ RECORD.COMUNE "ERBUSCO"
READE KEY2  
ASSERTEQ RECORD.COMUNE "ESINE"
READE KEY2  
ASSERTEQ RECORD.COMUNE "FIESSE"
CLOSE MUNICIPALITY_TABLE_NAME
```

```sh
@Test
fun t02B_findTwoAfterErbuscoWithSetll4AndRead() {
    val dbFile = dbManager!!.openFile(libName!!, MUNICIPALITY_TABLE_NAME)
    assertTrue(dbFile!!.setll(buildMunicipalityKey("IT", "LOM", "BS", "ERBUSCO")))
    assertEquals("ERBUSCO", getMunicipalityName(dbFile.read().record))
    assertEquals("ESINE", getMunicipalityName(dbFile.read().record))
    assertEquals("FIESSE", getMunicipalityName(dbFile.read().record))
    dbManager!!.closeFile(libName!!, MUNICIPALITY_TABLE_NAME)
}
OPEN MUNICIPALITY_TABLE_NAME 
KEY1 "IT" "LOM" "BS" "ERBUSCO"
RECORD COMUNE
SETLL KEY1
ASSERTEQ SETLL @True
READ  
ASSERTEQ RECORD.COMUNE == "ERBUSCO"
READ  
ASSERT RECORD.COMUNE == "ESINE"
READ  
ASSERT RECORD.COMUNE == "FIESSE"
CLOSE MUNICIPALITY_TABLE_NAME
```

```sh
@Test
fun t03_findTwoBeforeErbuscoWithSetll4AndReadPE3() {
    val dbFile = dbManager!!.openFile(libName!!, MUNICIPALITY_TABLE_NAME)
    assertTrue(dbFile!!.setll(buildMunicipalityKey("IT", "LOM", "BS", "ERBUSCO")))
    assertEquals(
        "EDOLO",
         getMunicipalityName(dbFile.readPreviousEqual(buildMunicipalityKey("IT", "LOM", "BS")).record)
    )
    assertEquals(
        "DESENZANO DEL GARDA",
        getMunicipalityName(dbFile.readPreviousEqual(buildMunicipalityKey("IT", "LOM", "BS")).record)
    )
    dbManager!!.closeFile(libName!!, MUNICIPALITY_TABLE_NAME)
}
OPEN MUNICIPALITY_TABLE_NAME 
KEY1 "IT" "LOM" "BS" "ERBUSCO"
RECORD COMUNE
SETLL KEY1
ASSERTEQ SETLL @True
READPE KEY1 
ASSERTEQ RECORD.COMUNE "EDOLO"
READPE KEY1
ASSERTEQ RECORD.COMUNE "DESENZANO DEL GARDA"
CLOSE MUNICIPALITY_TABLE_NAME
```

```sh
@Test
fun t03B_findTwoBeforeErbuscoWithSetll4AndReadP() {
    val dbFile = dbManager!!.openFile(libName!!, MUNICIPALITY_TABLE_NAME)
    assertTrue(dbFile!!.setll(buildMunicipalityKey("IT", "LOM", "BS", "ERBUSCO")))
    assertEquals("EDOLO", getMunicipalityName(dbFile.readPrevious().record))
    assertEquals("DESENZANO DEL GARDA", getMunicipalityName(dbFile.readPrevious().record))
    dbManager!!.closeFile(libName!!, MUNICIPALITY_TABLE_NAME)
}
OPEN MUNICIPALITY_TABLE_NAME 
KEY1 "IT" "LOM" "BS" "ERBUSCO"
RECORD COMUNE
SETLL KEY1
ASSERTEQ SETLL @True
READP 
ASSERTEQ RECORD.COMUNE "EDOLO"
READP
ASSERTEQ RECORD.COMUNE "DESENZANO DEL GARDA"
CLOSE MUNICIPALITY_TABLE_NAME
```

```sh
@Test
fun t04_findLastOfBergamoWithSetll4AndReadPE2() {
    val dbFile = dbManager!!.openFile(libName!!, MUNICIPALITY_TABLE_NAME)
    assertTrue(dbFile!!.setll(buildMunicipalityKey("IT", "LOM", "BS", "ACQUAFREDDA")))
    assertEquals(0, dbFile.readPreviousEqual(buildMunicipalityKey("IT", "LOM", "BS")).record.size)
    assertTrue(dbFile.setll(buildMunicipalityKey("IT", "LOM", "BS", "ACQUAFREDDA")))
    assertEquals("ZOGNO", getMunicipalityName(dbFile.readPreviousEqual(buildMunicipalityKey("IT", "LOM")).record))
    dbManager!!.closeFile(libName!!, MUNICIPALITY_TABLE_NAME)
}
OPEN MUNICIPALITY_TABLE_NAME 
KEY1 "IT" "LOM" "BS" "ACQUAFREDDA"
KEY2 "IT" "LOM" "BS"
KEY3 "IT" "LOM"
RECORD COMUNE
SETLL KEY1
ASSERTEQ SETLL @True
READPE KEY2
ASSERTEQ RECORD @Empty
SETLL KEY1
ASSERTEQ SETLL @True
READPE KEY3
ASSERTEQ RECORD.COMUNE "ZOGNO"
CLOSE MUNICIPALITY_TABLE_NAME
```

## SPL Syntax - Implementazione (10.04.2020)
Nel corso dello sviluppo, alcune considerazioni fatte nell'analisi precedente possono essere venute meno o comunque differire dallo stato dell'arte dell'implementazione, in virtù di facilitazioni o ostacoli emersi in fase di scrittura del codice. 

Relativamente al progetto [DBNAtiveAccess](https://github.com/smeup/DBNativeAccess) sono state implementate ulteriori
istruzioni intepretate che utilizzano funzioni kotlin di accesso al DB:

* VAR
* OPEN
* CLOSE
* KEY
* CHAIN
* SETLL
* SETGT
* READ
* READP
* READE
* READPE
* ASSERTxx (Equals, Contains...)

 
#### VAR (dichiarazione e valorizzazione variabile)
Si dichiara con la specifica VAR, seguita dal nome della variabile e dal suo valore (variabile o costante):

```sh
VAR MYVAR "Foo"
```
oppure se variabile

```sh
VAR MYVAR1 "Bar"
VAR MYVAR2 MYVAR1
```


#### OPEN (apertura tabella DB)
Si puo specificare il nome libreria.tabella sia come costante che come variabile:

```sh
OPEN "TSTLIB.TSTTAB"
```
oppure se variabile

```sh
VAR MYLIB "TSTLIB.TSTTAB"
OPEN MYLIB
```

#### CLOSE (chiusura tabella DB)
Si puo specificare il nome libreria.tabella sia come costante che come variabile:

```sh
CLOSE "TSTLIB.TSTTAB"
```

oppure se variabile

```sh
VAR MYLIB "TSTLIB.TSTTAB"
CLOSE MYLIB
```


#### KEY (dichiarazione chiave/lista chiavi d'accesso alla tabella DB)
Si dichiara con la specifica KEY, seguita dal nome che si vuol dare alla chiave/lista chiavi e seguita dal/i campo/i della tabella DB:

```sh
VAR TSTFLDCHR "XXX"
VAR TSTFLDNBR "123.45"
KEY KEY001 TSTFLDCHR TSTFLDNBR
```

**N.B.** non è supportata l'assegnazione di una KEY ad una VAR, per poi utilizzare VAR come chiave di un'operazione di accesso al DB, cioè:

```sh
VAR TSTFLDCHR "XXX"
KEY KEY001 TSTFLDCHR
VAR MY_VAR KEY001 // non ammesso
CHAIN MYLIB MY_VAR // non ammesso

```

#### CHAIN (accesso al file e lettura del record per chiave)
Si effettua con la specifica CHAIN, seguita dal nome tabella (variabile o costante) seguita dalla chiave/lista chiavi (istruzione KEY) precedentemente definita:

**N.B.** ovviamente è necessario che l'operazioni di apertura (OPEN) e le dichiarazioni/valorizzazioni di variabili/chiavi siano state preventivamente effettuate. 


```sh
VAR MYLIB "TSTLIB.TSTTAB"
VAR TSTFLDCHR "XXX"
KEY KEY001 TSTFLDCHR
CHAIN MYLIB KEY001
ASSERTEQ "TSTLIB.TSTTAB" @FOUND
ASSERTEQ "TSTLIB.TSTTAB" "TSTFLDCHR" "XXX"
```
**N.B.** mediante la keyword @FOUND si può testare che l'operazione abbia trovato il record

#### SETLL (posizionamento del cursore al "di sotto" del limite inferiore della chiave fornita.)
Si effettua con la specifica SETLL, seguita dal nome tabella (variabile o costante) seguita dalla chiave/lista chiavi (istruzione KEY) precedentemente definita:

```sh
OPEN "TSTLIB.MUNICIPALITY"
VAR NAZ "IT"
VAR REG "LOM"
VAR PROV "BS"
VAR CITTA "ERBUSCO"
KEY KEY001 NAZ REG PROV CITTA
SETLL "TSTLIB.MUNICIPALITY" KEY001
ASSERTEQ "TSTLIB.MUNICIPALITY" @FOUND
```
**N.B.** mediante la keyword @FOUND si può testare che l'operazione abbia trovato il record


#### SETGT (posizionamento del cursore al "di sopra" del limite superiore della chiave fornita.)
Si effettua con la specifica SETGT, seguita dal nome tabella (variabile o costante) seguita dalla chiave/lista chiavi (istruzione KEY) precedentemente definita:

```sh
OPEN "TSTLIB.MUNICIPALITY"
VAR NAZ "IT"
VAR REG "LOM"
VAR PROV "BS"
VAR CITTA "ERBUSCO"
KEY KEY001 NAZ REG PROV CITTA
SETGT "TSTLIB.MUNICIPALITY" KEY001
ASSERTEQ "TSTLIB.MUNICIPALITY" @FOUND
```
**N.B.** mediante la keyword @FOUND si può testare che l'operazione abbia trovato il record


#### READ (lettura del record con spostamento del cursore "next", senza utilizzo di chiave)
Si effettua con la specifica READ, seguita dal nome della tabella (variabile o costante), senza però specificare la chiave/lista chiavi.
```sh
OPEN "TSTLIB.MUNICIPALITY"
VAR NAZ "IT"
VAR REG "LOM"
VAR PROV "BS"
VAR CITTA "ERBUSCO"
KEY KEY001 NAZ REG PROV CITTA
SETLL "TSTLIB.MUNICIPALITY" KEY001
ASSERTEQ "TSTLIB.MUNICIPALITY" @FOUND
READ "TSTLIB.MUNICIPALITY"
ASSERTEQ "TSTLIB.MUNICIPALITY" "CITTA" "ERBUSCO"
READ "TSTLIB.MUNICIPALITY"
ASSERTEQ "TSTLIB.MUNICIPALITY" "CITTA" "ESINE"
```


#### READE (lettura del record con spostamento del cursore "next" con l'utilizzo di chiave)
Si effettua con la specifica READE, seguita dal nome della tabella (variabile o costante), seguita dalla chiave/lista chiavi (istruzione KEY) precedentemente definita:
```sh
OPEN "TSTLIB.MUNICIPALITY"
VAR NAZ "IT"
VAR REG "LOM"
VAR PROV "BS"
VAR CITTA "ERBUSC"
KEY KEY001 NAZ REG PROV CITTA
SETLL "TSTLIB.MUNICIPALITY" KEY001
ASSERTEQ "TSTLIB.MUNICIPALITY" @FOUND
KEY KEY002 NAZ REG PROV
READE "TSTLIB.MUNICIPALITY" KEY002
ASSERTEQ "TSTLIB.MUNICIPALITY" "CITTA" "ERBUSCO"
READE "TSTLIB.MUNICIPALITY" KEY002
ASSERTEQ "TSTLIB.MUNICIPALITY" "CITTA" "ESINE"
```

#### READP (lettura del record con spostamento del cursore "previous", senza utilizzo di chiave)
Si effettua con la specifica READP, seguita dal nome della tabella (variabile o costante), senza però specificare la chiave/lista chiavi.
```sh
OPEN "TSTLIB.MUNICIPALITY"
VAR NAZ "IT"
VAR REG "LOM"
VAR PROV "BS"
VAR CITTA "ERBUSCO"
KEY KEY001 NAZ REG PROV CITTA
SETLL "TSTLIB.MUNICIPALITY" KEY001
ASSERTEQ "TSTLIB.MUNICIPALITY" @FOUND
READP "TSTLIB.MUNICIPALITY"
ASSERTEQ "TSTLIB.MUNICIPALITY" "CITTA" "EDOLO"
READP "TSTLIB.MUNICIPALITY"
ASSERTEQ "TSTLIB.MUNICIPALITY" "CITTA" "DESENZANO DEL GARDA"
```

#### READPE (lettura del record con spostamento del cursore "previous" con l'utilizzo di chiave)
Si effettua con la specifica READPE, seguita dal nome della tabella (variabile o costante), seguita dalla chiave/lista chiavi (istruzione KEY) precedentemente definita:
```sh
OPEN "TSTLIB.MUNICIPALITY"
VAR NAZ "IT"
VAR REG "LOM"
VAR PROV "BS"
VAR CITTA "ERBUSC"
KEY KEY001 NAZ REG PROV CITTA
SETLL "TSTLIB.MUNICIPALITY" KEY001
ASSERTEQ "TSTLIB.MUNICIPALITY" @FOUND
KEY KEY002 NAZ REG PROV
READPE "TSTLIB.MUNICIPALITY" KEY002
ASSERTEQ "TSTLIB.MUNICIPALITY" "CITTA" "EDOLO"
READPE "TSTLIB.MUNICIPALITY" KEY002
ASSERTEQ "TSTLIB.MUNICIPALITY" "CITTA" "DESENZANO DEL GARDA"
```

#### ASSERTxx (Asserzioni EQ=Equal, NE=NotEqual, CN=Contains, NC=NotContains)
Si effettua con la relativa istruzione ASSERTxx, seguita dal nome della tabella (variabile o costante) seguita dal nome del campo DB.
**N.B.** In questo caso il nome del campo (del record del DB) non deve essere variabile (quindi va scritto tra doppi apici), verrebbe altrimenti risolto prendendo il suo valore come definito precedentemente in VAR (nella dichiarazione della keylist KEY...)

```sh
OPEN "TSTLIB.TSTTAB"
VAR TSTFLDCHR "XXX"
VAR TSTFLDNBR "123.45"
VAR VAR001 "3.4"
KEY KEY001 TSTFLDCHR
KEY KEY002 TSTFLDNBR
KEY KEY003 TSTFLDCHR TSTFLDNBR
CHAIN "TSTLIB.TSTTAB" KEY001
ASSERTEQ "TSTLIB.TSTTAB" "TSTFLDCHR" "XXX"
ASSERTEQ "TSTLIB.TSTTAB" "TSTFLDNBR" "123.45"
ASSERTEQ "TSTLIB.TSTTAB" "TSTFLDNBR" "999.99"
ASSERTNE "TSTLIB.TSTTAB" "TSTFLDNBR" "543.21"
ASSERTNE "TSTLIB.TSTTAB" "TSTFLDCHR" @EMPTY
ASSERTCN "TSTLIB.TSTTAB" "TSTFLDNBR" VAR001
ASSERTNC "TSTLIB.TSTTAB" "TSTFLDNBR" "4.5"
ASSERTNC "TSTLIB.TSTTAB" "TSTFLDNBR" ".45"
CLOSE "TSTLIB.TSTTAB"
```

**N.B.** presenza della keyword riservata @EMPTY

nel caso di SETxx e READxx:
```sh
OPEN "TSTLIB.MUNICIPALITY"
VAR NAZ "IT"
VAR REG "LOM"
VAR PROV "BS"
VAR CITTA "ERBUSCO"
KEY KEY001 NAZ REG PROV CITTA
SETLL "TSTLIB.MUNICIPALITY" KEY001
ASSERTEQ "TSTLIB.MUNICIPALITY" @FOUND
READ "TSTLIB.MUNICIPALITY"
ASSERTEQ "TSTLIB.MUNICIPALITY" "CITTA" "ERBUSCO"
READ "TSTLIB.MUNICIPALITY"
ASSERTEQ "TSTLIB.MUNICIPALITY" "CITTA" "ESINE"
READ "TSTLIB.MUNICIPALITY"
ASSERTEQ "TSTLIB.MUNICIPALITY" "CITTA" "FIESSE"
CLOSE "TSTLIB.MUNICIPALITY"
```


Lo stato (memoria) del programma è conservata nell'oggetto ExecutionContext:

```sh
public class ExecutionContextImpl implements ExecutionContext {
	private SQLDBMManager dbManager;
	private LinkedHashMap<String, DBFile> dbFiles;
	private LinkedHashMap<String, Result> results;
	private LinkedHashMap<String, String> variables;
	private LinkedHashMap<String, List<RecordField>> keys;
	private LinkedHashMap<String, Boolean> positioningResults;
	private List<ProgramInstructionsResults> programInstructionsResults;
	private String testLibrary;
        ...
        ...
}
```

**N.B.** la presenza della variabile __testLibrary__ è da considerarsi temporanea, in quanto palliativo per gestire la presenza di un DB in memoria (HSQLDB) a runtime.
Questo perchè il DB viene creato all'avvio del test e quindi qualsiasi dichiarazione di libreria fatta a posteriori (nel codice da intepretare) non sarebbe coerente con quanto creato in fase di startup (libreria TSTLIB_xxxxxxxxxxxxx_y.TSTTAB temporanea, non può soddisfare quella specificata nella OPEN).
In sostanza quindi il valore di __testLibrary__, se presente, sovrascriverà qualsiasi dichiarazione di libreria fatta nel codice SPL da intepretare.

Tutti i valori di ritorno dell'esecuzione di ogni singola istruzione vengono memorizzati nell'oggetto ProgramInstructionsResults:

```sh
public class ProgramInstructionsResults {
	private Instruction instructionType;
	private Instant timeBegin;
	private Instant timeEnd;
	private long timeElapsed;
	private Map<String, String> results;
	private List<String> logs;
	
	public ProgramInstructionsResults(Instruction instructionType) {
		this.instructionType = instructionType;
		this.timeBegin = Instant.now();
		this.results = new LinkedHashMap<String, String>();
		this.logs = new ArrayList<String>();
	}
        ...
        ...
```

dove:

* instruction: tipo istruzione (VAR, OPEN...)
* timeStamp: timestamp inizio esecuzione istruzione
* result: risultato istruzione (se ASSERT:True/False, se VAR:nomeVar=valore, se KEY:nomeKey=valore/i, se CHAIN: lista campi del record)
* logs: log 

**N.B.** l'esecuzione di ogni istruzione produce una nuova istanza di ProgramInstructionsResults, che viene poi memorizzata in ExecutionContext. 

Di fatto con ExecutionContext e ProgramInstructionsResults si ha tutta la "storia" dell'esecuzione delle istruzioni. 

Per intenderci, se in due momenti si assegnano due valori diversi alla medesima variabile, tale variabile avrà (ovviamente) l'ultimo valore assegnatogli e memorizzato in ExecutionContext (getVariables) ma avremo anche tutti i valori precedenti memorizzati nelle diverse istanze di ProgramInstructionsResults. 

_toString di ProgrammInstructionsResults di ogni istruzione eseguita_
```sh
Type: OPEN - Results:  - Begin: 2020-04-02T12:31:45.571Z - End: 2020-04-02T12:31:45.573Z - Elapsed: 2ms - Log: [INFO Table TSTLIB_1585830705185_6.TSTTAB correctly opened]
Type: VAR - Results: TSTFLDCHR=XXX - Begin: 2020-04-02T12:31:45.575Z - End: 2020-04-02T12:31:45.575Z - Elapsed: 0ms - Log: [INFO Var TSTFLDCHR of value XXX stored in memory]
Type: VAR - Results: TSTFLDNBR=123.45 - Begin: 2020-04-02T12:31:45.575Z - End: 2020-04-02T12:31:45.575Z - Elapsed: 0ms - Log: [INFO Var TSTFLDNBR of value 123.45 stored in memory]
Type: VAR - Results: VAR001=3.4 - Begin: 2020-04-02T12:31:45.575Z - End: 2020-04-02T12:31:45.575Z - Elapsed: 0ms - Log: [INFO Var VAR001 of value 3.4 stored in memory]
Type: KEY - Results: KEY001=TSTFLDCHR , TSTFLDCHR=XXX - Begin: 2020-04-02T12:31:45.575Z - End: 2020-04-02T12:31:45.575Z - Elapsed: 0ms - Log: [INFO Key KEY001 stored in memory]
Type: KEY - Results: KEY002=TSTFLDNBR , TSTFLDNBR=123.45 - Begin: 2020-04-02T12:31:45.575Z - End: 2020-04-02T12:31:45.575Z - Elapsed: 0ms - Log: [INFO Key KEY002 stored in memory]
Type: KEY - Results: KEY003=TSTFLDCHR TSTFLDNBR , TSTFLDCHR=XXX, TSTFLDNBR=123.45 - Begin: 2020-04-02T12:31:45.575Z - End: 2020-04-02T12:31:45.575Z - Elapsed: 0ms - Log: [INFO Key KEY003 stored in memory]
Type: CHAIN - Results: TSTFLDCHR=XXX, TSTFLDNBR=123.45 - Begin: 2020-04-02T12:31:45.575Z - End: 2020-04-02T12:31:45.579Z - Elapsed: 4ms - Log: [INFO Record found on TSTLIB_1585830705185_6.TSTTAB with key KEY001]
Type: ASSERTEQ - Results: ASSERTEQ=true - Begin: 2020-04-02T12:31:45.581Z - End: 2020-04-02T12:31:45.581Z - Elapsed: 0ms - Log: [INFO Assert TSTFLDCHR EQ XXX is true (Actual: XXX Expected: XXX)]
Type: ASSERTEQ - Results: ASSERTEQ=true - Begin: 2020-04-02T12:31:45.581Z - End: 2020-04-02T12:31:45.581Z - Elapsed: 0ms - Log: [INFO Assert TSTFLDNBR EQ 123.45 is true (Actual: 123.45 Expected: 123.45)]
Type: ASSERTEQ - Results: ASSERTEQ=false - Begin: 2020-04-02T12:31:45.581Z - End: 2020-04-02T12:31:45.581Z - Elapsed: 0ms - Log: [INFO Assert TSTFLDNBR EQ 999.99 is false (Actual: 123.45 Expected: 999.99)]
Type: ASSERTNE - Results: ASSERTNE=true - Begin: 2020-04-02T12:31:45.581Z - End: 2020-04-02T12:31:45.582Z - Elapsed: 1ms - Log: [INFO Assert TSTFLDNBR NE 543.21 is true (Actual: 123.45 Expected: 543.21)]
Type: ASSERTNE - Results: ASSERTNE=true - Begin: 2020-04-02T12:31:45.582Z - End: 2020-04-02T12:31:45.582Z - Elapsed: 0ms - Log: [INFO Assert TSTFLDCHR NE  is true (Actual: XXX Expected: )]
Type: ASSERTCN - Results: ASSERTCN=true - Begin: 2020-04-02T12:31:45.582Z - End: 2020-04-02T12:31:45.582Z - Elapsed: 0ms - Log: [INFO Assert TSTFLDNBR CN 3.4 is true (Actual: 123.45)]
Type: ASSERTNC - Results: ASSERTNC=true - Begin: 2020-04-02T12:31:45.582Z - End: 2020-04-02T12:31:45.582Z - Elapsed: 0ms - Log: [INFO Assert TSTFLDNBR NC 4.5 is true (Actual: 123.45)]
Type: ASSERTNC - Results: ASSERTNC=false - Begin: 2020-04-02T12:31:45.582Z - End: 2020-04-02T12:31:45.582Z - Elapsed: 0ms - Log: [INFO Assert TSTFLDNBR NC .45 is false (Actual: 123.45)]
Type: CLOSE - Results:  - Begin: 2020-04-02T12:31:45.582Z - End: 2020-04-02T12:31:45.582Z - Elapsed: 0ms - Log: [INFO Table TSTLIB_1585830705185_6.TSTTAB closed]
```



#### Test run
Per testare l'esecuzione di un'istruzione, è possibile scrivere classi di test da usare come entry point dell'applicazione (es. ChainTest.java):

 ```sh
public class ChainTest extends SPLAbstractTest {
	@Test
	public void findRecordsIfChainWithExistingKey_1() throws IOException {
		SQLDBTestUtilsKt.createAndPopulateTstTable(dbManager, libName);
		executionContext = new ExecutionContextCLI(dbManager, dbFiles, results, variables, keys, positioningResults, programInstructionsResults, libName);
		Program p = new Program();
		p.setProgramName("findRecordsIfChainWithExistingKey_1");
		p.execute("com.smeup.ProgramExecutorConcrete", executionContext);
		
		executionContext.getProgramInstructionsResults().forEach(item -> {
			System.out.println(item.toString());
			switch(item.getInstructionType()) {
			case ASSERTEQ:
				org.junit.Assert.assertEquals("true", item.getResults().get("ASSERTEQ"));
				break;
			case ASSERTNE:
				org.junit.Assert.assertEquals("true", item.getResults().get("ASSERTNE"));
				break;
			case ASSERTCN:
				org.junit.Assert.assertEquals("true", item.getResults().get("ASSERTCN"));
				break;
			case ASSERTNC:
				org.junit.Assert.assertEquals("true", item.getResults().get("ASSERTCN"));
				break;
			default:
				break;
			}
		});
		
	}
 
 ```

L'istanza di Program.java viene creata passando nel costruttore il nome del file (findRecordsIfChainWithExistingKey_1.properties ma senza estensione) contenente le istruzioni da interpretare:

```sh
OPEN "TSTLIB.TSTTAB"
VAR TSTFLDCHR "XXX" 
VAR TSTFLDNBR "123.45"
VAR VAR001 "3.4"
KEY KEY001 TSTFLDCHR
KEY KEY002 TSTFLDNBR
KEY KEY003 TSTFLDCHR TSTFLDNBR
CHAIN "TSTLIB.TSTTAB" KEY001
ASSERTEQ "TSTLIB.TSTTAB" "TSTFLDCHR" "XXX" (asserisce che il campo TSTFLDCHR del DB TSTLIB.TSTTAB è uguale a "XXX")
ASSERTEQ "TSTLIB.TSTTAB" "TSTFLDNBR" "123.45"
ASSERTEQ "TSTLIB.TSTTAB" "TSTFLDNBR" "999.99"
ASSERTNE "TSTLIB.TSTTAB" "TSTFLDNBR" "543.21" (asserisce che il campo TSTFLDNBR del DB TSTLIB.TSTTAB non è uguale a "543.21")
ASSERTNE "TSTLIB.TSTTAB" "TSTFLDCHR" @EMPTY (asserisce che il campo TSTFLDCHR del DB TSTLIB.TSTTAB non è vuoto)
ASSERTCN "TSTLIB.TSTTAB" "TSTFLDNBR" VAR001 (asserisce che il campo TSTFLDNBR del DB TSTLIB.TSTTAB contiene il valore di VAR001)
ASSERTNC "TSTLIB.TSTTAB" "TSTFLDNBR" "4.5" (asserisce che il campo TSTFLDNBR del DB TSTLIB.TSTTAB non contiene il valore di VAR001)
ASSERTNC "TSTLIB.TSTTAB" "TSTFLDNBR" ".45" 
CLOSE "TSTLIB.TSTTAB"

```

Il metodo execute() di Program.java si occupa poi di leggere il contenuto del file da intepretare e per ogni istruzione riconosciuta (interfaccia PrpgramExecutor.java), eseguire il relativo metodo (implementazione: ProgramExecutorAdapter.java)

_Program.java_

```sh

...
    String line = iterator.next();
    while(line != null){
        String instruction = line.split(" ")[0];
        String[] args = line.split(" ");
        onInstructionStart.accept(line);
        switch(Instruction.valueOf(instruction)){
            case KEY: // DEFINE KEYLIST TO ACCESS DB
            	  // 1-keyName 
            	  // 2-exec context
            	  // 3-keyValues
            	  String[] keyValues = Arrays.copyOfRange(args, 2, args.length);
                programExecutor.key(args[1], executionContext, keyValues);
                break;
            case VAR: // DEFINE VAR (EXECUTION CONTEXT SCOPE)
            	  // 1-varName 
            	  // 2-varValue
                programExecutor.var(args[1], Utils.evaluateVariable(args[2], executionContext), executionContext);
                break;
            case CHAIN: // EXECUTE CHAIN WITH KEYLIST
            	  // 1-library and tableName as "libraryName.tableName"
            	  // 2-keyName
                programExecutor.chain(tableNameSolver(args[1], executionContext), args[2], executionContext);
                break;
...

```

**N.B.** l'utilizzo di Utils.evaluateVariable(nomeVariabile, ExcecutionContext) serve a reperire il valore della variabile dalla memoria (ExecutionContext), qualora questa venga passata come variabile appunto e non come costante (tra doppie virgolette).


_ProgramExecutor.java_

```sh
public interface ProgramExecutor {   
    public void call(String instruction, ExecutionContext executionContext);
    public void write(String fileName, ExecutionContext executionContext);
    public void readfile(String fileName, ExecutionContext executionContext);
    public void get(String url, ExecutionContext executionContext);
    public void wait(int timeSleep, ExecutionContext executionContext);
    public void submit(String instruction, ExecutionContext executionContext);
    public boolean open(String tableName, ExecutionContext executionContext);
    public boolean close(String tableName, ExecutionContext executionContext);
    public void key(String keyName, ExecutionContext executionContext, String... keyValues);
    public void var(String varName, String varValue, ExecutionContext executionContext);
    public boolean chain(String tableName, String keyName, ExecutionContext executionContext);
    public boolean setll(String tableName, String keyName, ExecutionContext executionContext);
    public boolean setgt(String tableName, String keyName, ExecutionContext executionContext);
    public boolean read(Instruction instruction, String tableName, String keyName, ExecutionContext executionContext);
    public boolean assertion(Assertions assertion, String[] parms, ExecutionContext executionContext);   
}
```

L'esecuzione dell'istruzione CHAIN (accesso ad un record di tabella di DB) necessita che siano state preventivamente eseguite
istruzioni di apertura tabella (OPEN), dichiarazione di variabile/i (VAR) da usare come campi chiave, dichiarazione della chiave (KEY).

Analizzando attualmente l'implementazione di CHAIN in ProgramExecutorAdapter.java:

_ProgramExecutorAdapter.java_

```sh
    @Override
    public boolean chain(String tableName, String keyName, final ExecutionContext executionContext) {
    	boolean errorOccurred = true;
    	ProgramInstructionsResults pir = new ProgramInstructionsResults(Instruction.CHAIN);
    	String log = "";
    	final String keyFile = tableName;
    	DBFile dBFile = executionContext.getDbFiles().get(keyFile);
    	if (null == dBFile) {
    		log = Level.SEVERE.toString() + "Error on table " + keyFile + " Maybe not open?";
    	} else {
        	Result result = dBFile.chain(executionContext.getKeys().get(keyName));
    		pir.timeStampExecutionEnded();
        	executionContext.getResult().put(keyFile, result);
        	if (null == result ) {
        		log = Level.WARNING.toString() + " No record found on " + keyFile + " with key " + keyName;
        	} else {
        		log = Level.INFO.toString() + " Record found on " + keyFile + " with key " + keyName;
        		result.getRecord().forEach( (fldName,fldValue) -> pir.getResults().put(fldName, fldValue) ); 
            	errorOccurred = false;
        	}
    	}
		pir.getLogs().add(log);
		executionContext.getProgramInstructionsResults().add(pir);
		return errorOccurred;
    }   
```

Firma:

* nome della tabella (nella forma libreria.tabella) 
* nome della chiave
* executionContext

Corpo:

* Crea ProgramInstructionsResult (memoria della singola istruzione)
* Ottiene tabella (DBFile) da memoria (ExecutionContext) se precedentemente aperta (OPEN) con successo
* Esegue istruzione chain (funzionalità fornita dal progetto Kotlin DBNativeAccess, package com.smeup.dbnative.file.DBFile)
* Chain (che abbia successo o meno) ottiene un Result che viene memorizzato in ExecutionContext. Result contiene oggetti Record, costituiti a loro volta da una lista di campi RecordField.
* Il risultato della chain (oltre ai log) viene memorizzato anche in ProgramInstrucionsResult dove rimarrà per tutta la vita del programma. 

## TODO e Migliorie a DBNativeAccess
* Istruzione DELETE
* DBNativeAccess, nella fattispecie l'oggetto DBFile dovrà fornire informazioni relative a metadata, per permettere di gestire le operazioni di WRITE ed UPDATE di cui sopra.
In sostanza dovrebbe esporre i metadati della tabella, sia in termini di campi del file, che in termini di campi chiave. Questo permetterebbe di istanziare il tracciato record del file da parte dell'interprete all'apertura
* Valutare la possibilità di gestire un campo autoincrement per avere una chiave univoca implicita sul file (in modo simile a quanto avviene con RRN su as400)

## TODO e Migliorie a interpreter-runtime-proto
* Istruzione DELETE 
* Istruzione WRITE 
* Istruzione UPDATE 
* Istruzione OPEN: deve poter utilizzare l'istanza di DBFile fornita da DBNativeAccess, in modo che la non necessiti del passaggio di una stringa di connessione (sia quindi trasparente al linguaggio interpretato), attualmente invece utilizza l'accesso al database creato a runtime e passato come stringa di connessione:
```sh
OPEN "TSTLIB.TST2TAB" "jdbc:hsqldb:mem:Tst2Tab;,sa,sa,/home/tron/Foo/Test/Csv/Tst2Tab.csv"
```
viene specificata una stringa di connessione con i riferimenti alla base dati, che peraltro fornisce attualmente i metadata (nella prima riga del file csv)
__Tst2Tab.csv__
```sh
Name="TSTFLDCHR";Type=VARCHAR(50);PrimaryKey=True;Unique=True;NotNull=True,Name="TSTFLDNBR";Type=DECIMAL(10:2);PrimaryKey=False;Unique=False;NotNull=True
"XXX",123.45
```
* Testabilità del progetto "core" tramite web application (attualmente è testabile solo tramite cli con il comando: 
```sh
java -jar interpreter-runtime-core.jar "Foo/Test/Spl/spl_sample.txt"
```



