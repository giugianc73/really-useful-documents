
# LabDocs su GitHub
Questo progetto si occupa di trovare una soluzione per stabilire la documentazione del gruppo SmeUp su GitHub.

## Introduzione
Il progetto si occupa di tre aspetti:
- Migrazione.
- Creazione.
- Mantenimento.  

La **Migrazione** si preoccupa dei documenti già esistenti su LabDocs. Questi devono venire correttamente **trasferiti** sulla nuova piattaforma, **senza avere una perdita di informazioni** nel processo.  
La **Creazione** si preoccupa della creazione di nuovi documenti in un modo più smart e semplice rispetto al passato.  
Il **Mantenimento** deve preoccuparsi di come gestire e mantenere, **in modo ordinato**, una repository su GitHub che ha più di 1500 documenti al suo interno.


## Migrazione

### Alcuni cenni storici
LabDocs non è un nuovo, contiene più di 1500 file, tra documentazione, tutorial e documenti vari. Bisogna trovare un metodo per trasferire tutto questo senza perdere nulla di quello scritto fino ad adesso.


I documenti su [LabDocs](https://labdocs.smeup.com/) sono scritti in **Markdown** (abbreviato in MD) un linguaggio di markup nato nel 2004 per cui, nel bene o nel male, **non è mai stato definito uno standard internazionale**. LabDocs, ad esempio, utilizza **inserire implementazione md**.

Nel 2014 è nato [**CommonMark**](https://commonmark.org) che cercava  di unire tutte le implementazioni del linguaggio sotto un'unica bandiera, in modo da non avere più ambiguità tra i vari documenti.  
GitHub utilizza una estensione di CommonMark, detta [GitHub Flavored Markdown](https://github.github.com/gfm/), questo crea problemi di compatibilità tra i due sistemi: **non tutti i documenti LabDocs vengono visualizzati correttamente su GitHub.**  
Dobbiamo trovare e riparare tutti gli elementi in discrepanza senza dover riscrivere a mano ogni documento, *cosa oggettivamente poco fattibile data la mole di dati*.

### Convertire i documenti esistenti
Per convertire i vari documenti abbiamo diverse strade:
- Utilizzare un software convertitore di documenti, come [**Pandoc**](https://pandoc.org/).


### Elementi Discrepanti
#### Header
L'implementazione di CommonMark è molto certosina per quanto riguarda spazi e 

#### Table of Content
La Table of Content non viene correttamente renderizzata in quanto deprecata l'espressione `[[_TOC_]]`, utilizzata spessissimo nei documenti su LabDocs. GitHub mette a disposizione una sua versione da interfaccia grafica, quindi il problema si pone nel:
- Rimuovere la dicitura del tutto.
- Mantenerla in tutti i documenti in caso si voglia fare un *rollback*.
- Mantenerla nei documenti *pre-GitHub* e rimuoverla nei nuovi documenti.

![Table Of Content su GitHub](https://i.imgur.com/DedStfS.png)

#### Immagini
Le immagini contenute nei documenti LabDocs sono anche esse file locali, questo ha come vantaggio il fatto che so dove trovare una determinata immagine senza passare da un URL ignoto, ma sono costretto a caricarla manualmente. Questo toglie anche la modularità del documento, in quanto è fruibile solo con l'ausilio delle proprie immagini. Proprio per questo, **le immagini non vengono visualizzare su GitHub** perchè, appunto, dobbiamo **aggiornare il loro indirizzo** (nel nostro caso assoluto).

Tutto il resto sembra renderizzato come previsto, se dovessero esserci altri problemi fatelo notare.





## Contenuto che bisogna verificare

- link pubblici che vengono indirizzati in un sito internet esterno (Google Drive, pagine internet, youtube, ecc...)
- Immagini .png, .bpm, .img, ecc...
- Navigazione da una scheda all'altro sempre rimanendo su GitHub
- Visualizzazione delle tabelle 
- Caricamento di tutto il contenuto del LabDocs
- TOC (Tablle Of Contenent)

## Problematiche riscontrate

#### Immagini
Nelle immagini bisogna apportare delle modifiche relative al path dove si trova l'immagine stessa all'interno delle cartelle presente nel repository.

Fatto questo passaggio bisogna entrare nel documento ed elimianare l'endopoint "https://labdocs.smeup.com/"
e mettere solamente il path di dove si trova l'immagine.

#### TOC - Table Of Contenent
Durante la fase di migrazione la cosa ce non viene proprio visualizzata è il TOC. PEr far sicchè venga mostrato senza problemi, abbiamo diversi modi:
* crearlo a mano
* affidarsi ad un sito esterno ["GitHub Wiki TOC generator"](https://ecotrust-canada.github.io/markdown-toc/) che genera automaticamente in base al testo come è stato scritto

#### Caricamento e visualizzazione di tutto il contenuto del LabDocs
Purtroppo Git Hub ha un limite di visualizzazione, lato front-end, di solamente 1000 file. Questo cosa comporta? che se noi abbiamo da visualizzare 1500 file, i restanti 500 che non sono visualizzabili, non possono essere visualizzati al livello di interfaccia web. Però, va precisato che nel repository questi 500 file sono presenti, infatti il manuale ufficiale di Git Hub comunicare che se uno vuole vedere i file non visualizzabili, bisogna clonare il repository sul proprio pc.

#### Editor
Attualmente non è possibile creare un nuovo documento. Per risolvere questa lacuna, va fatto una ricerca di un'applicazione o qualcosa che mette in comunicazione con il repository tale da poter generare o editare un documento presente su Git.

Dopo aver fatto uno studio sulle possibili scelte, siamo giunti alla conclusione che due strade fanno al nostro caso:
* editor di github.com
* editor di github.dev

Sonio due cose distine e diverse. Facciamo un po' di chiarezza.

##### EDITOR DI GITHUB.COM

come si può evincere dalle immagini ho sempre la possibilità di creare un nuovo file all'interno del repository di GitHub e scrivere quello che si vuole. Ovviamente quando si ha finito, lo si può salvare e commitare.
![New File](https://labdocs.smeup.com/H3LS04-NW21000456/Editor_NewFile_githubcom.PNG)
![Create File](https://labdocs.smeup.com/H3LS04-NW21000456/Editor_CreateFile_githubcom.PNG)


_**Però questa possibilità di editare su github è molto limitata come funzionalità.**_


##### EDITOR DI GITHUB.DEV
C'è un'altra strada sempre rilasciata da GitHub. Per accedere a questo editor, dobbiamo cambiare l'endpoint da **github.com** a **github.dev**
In pratica a primo impatto è come Visual Studio Code. Ci sono tante e moltissime funzionalità rispetto all'editor precedente.

Si possono creare directory, file, modificare tutto rimanendo sempre connessi con il nostro repository. 

![Editor GitHubDev](https://labdocs.smeup.com/H3LS04-NW21000456/Editor_githubdev.PNG)

