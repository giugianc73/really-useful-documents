
# LabDocs su GitHub
Questo progetto si occupa di trovare una soluzione per stabilire la documentazione del gruppo SmeUp su GitHub.

[TOC]

## Introduzione
Il progetto si occupa di tre aspetti:
- Migrazione.
- Creazione.
- Mantenimento.  

La **Migrazione** si preoccupa dei documenti già esistenti su LabDocs. Questi devono venire correttamente **trasferiti** sulla nuova piattaforma, **senza avere una perdita di informazioni** nel processo.  
La **Creazione** si preoccupa della creazione di nuovi documenti in un modo più smart e semplice rispetto al passato.  
Il **Mantenimento** deve preoccuparsi di come gestire e mantenere, **in modo ordinato**, una repository su GitHub che ha più di 1500 documenti al suo interno.


## Migrazione

LabDocs non è un nuovo database, contiene più di 1500 file, tra documentazione, tutorial e documenti vari. Bisogna trovare un metodo per trasferire tutto questo senza perdere nulla di quello scritto fino ad adesso.


I documenti su [LabDocs](https://labdocs.smeup.com/) sono scritti in **Markdown** (abbreviato in MD) un linguaggio di markup nato nel 2004 per cui, nel bene o nel male, **non è mai stato definito uno standard internazionale**.

Nel 2014 è nato [**CommonMark**](https://commonmark.org) che cercava  di unire tutte le implementazioni del linguaggio sotto un'unica bandiera, in modo da non avere più ambiguità tra i vari documenti.  
GitHub utilizza una estensione di CommonMark, detta [GitHub Flavored Markdown](https://github.github.com/gfm/), questo crea problemi di compatibilità tra i due sistemi: **non tutti i documenti LabDocs vengono visualizzati correttamente su GitHub.**  
Dobbiamo trovare e riparare tutti gli elementi in discrepanza senza dover riscrivere a mano ogni documento, *cosa oggettivamente poco fattibile data la mole di dati*.


### Elementi Discrepanti
#### Header
L'implementazione CommonMark è molto certosina per quanto riguarda spazi e *a capi*, in LabDocs non ci sono problemi se un header è scritto come `#Header` o `# Header`, in GitHub solo la versione **con lo spazio** viene renderizzata correttamente. Questi problemi di identazione e spaziatura sono risolvibili con un convertitore di documenti come [**Pandoc**](https://pandoc.org/).

#### Table of Content
La Table of Content non viene correttamente renderizzata in quanto l'espressione `[TOC]` o `[[_TOC_]]`, utilizzata molto spesso nei documenti su LabDocs, non è riconosciuta. GitHub obbliga quindi gli utenti a creasi una loro TOC nel documento, cosa molto poco conveniente, ma mette a disposizione **una sua versione da interfaccia grafica**, quindi il problema si pone nel:
- Rimuovere la dicitura del tutto.
- Mantenerla in tutti i documenti in caso si voglia fare un *rollback*.
- Mantenerla nei documenti *pre-GitHub* e rimuoverla nei nuovi documenti.

![Table Of Content su GitHub](https://i.imgur.com/DedStfS.png)

Un'altra soluzione è utilizzare un programma terzo per auto-generare la TOC e aggiungerla al documento (anche manualmente *sigh*), alcuni esempi sono:
- [Versione Locale](https://github.com/ekalinin/github-markdown-toc) funziona anche con file da remoto e riesce a gestire più file in contemporanea (*need further testing*).
- [Versione Web](https://ecotrust-canada.github.io/markdown-toc/)

#### Immagini
Le immagini contenute nei documenti LabDocs sono anche esse file locali, questo ha come vantaggio il fatto che so dove trovare una determinata immagine senza passare da un URL ignoto, ma sono costretto a caricarla manualmente. Questo toglie anche la modularità del documento, in quanto è fruibile solo con l'ausilio delle proprie immagini. Proprio per questo, **le immagini non vengono visualizzate su GitHub** perchè, appunto, dobbiamo **aggiornare il loro indirizzo** (nel nostro caso assoluto).

Tutto il resto sembra renderizzato come previsto, se dovessero esserci altri problemi fatelo notare.

## Creazione
Una volta spostato l'intero database su GitHub sarebbe ideale poter aggiungervi nuovi documenti direttamente ed essendo nel 21esimo secolo sarebbe bello farlo in modo facile ed efficace. Vogliamo utilizzare un **Editor di Testo**.

### La scelta dell'Editor
Per capire che editor di testo scegliere dobbiamo prima capire cosa vogliamo noi scrittori da esso:
- **Drag And Drop**: L'upload e la gestione delle immagini deve essere gestito dal software, l'unica cosa che l'utente deve fare è trascinare l'immagine dentro al testo.
- **Push** **diretto**: Il file salvato deve poter essere pushato direttamente nella repository.
- **Accessibile Ovunque**: Posso visualizzare ed editare i miei documenti da qualsiasi PC o Smartphone, una ***Applicazione** **Web*** è preferibile per questo.
- **Produttività**: L'inclusione di Snippet, di autocompletamento del codice e di un Render-Preview in tempo reale. Tutte risorse che rendano la stesura di un documento facile ed immediata.
- **Multi-Utente**: In LabDocs non è supportato l'editing collaborativo, questo significa che se due utenti modificano lo stesso documento l'ultimo a salvare avrà la meglio. Vogliamo un applicativo che sia in grado di gestire più utenti in contemporanea (editor o viewer) sullo stesso documento.
- **User-Friendly:** Una interfaccia grafica semplice e intuitiva è sempre ben gradita.

### Alcuni Editor di Nota
Vediamo alcuni applicativi che risultano (più o meno) idonei a quello che cerchiamo.

#### [CodiMD](https://github.com/hackmdio/codimd/) e [HackMD](https://hackmd.io/)
Rispettivamente versione Open-Source e non, sono degli editor web, desktop ed estensioni VSCode molto versatili, con una interfaccia semplice e che permettono Drag&Drop di immagini (*anche CopyPaste*), Editing Collaborativo, e un collegamento con le repository su GitHub. Le immagini caricate vengono uploadate su [imgur](https://imgur.com) per gli utenti free, su un server Amazon S3 privato per gli utenti premium e con delle soluzioni ad-hoc per le aziende. Per i dettagli sui prezzi e le differenze tra i vari piani rifarsi a questo [link](https://hackmd.io/pricing).

![Esempio di file markdown](https://i.imgur.com/CMhCB7b.png)

#### [NotesHub](https://www.noteshub.app/)
NotesHub è un applicativo Web e Desktop gratuito che risulta molto semplice nella UI, permette di editare **direttamente collegandosi alla Repository** evitando di dover pushare ogni volta. Pultroppo **non permette Drag and Drop**, ma permette di caricare le immagini direttamente nella repository (*Need further testing per capire se con la versione Desktop è possibile scegliere una sottocartella per questi attachment, dato che vengono caricati tutti nella stessa cartella*)

![](https://i.imgur.com/SK6aIR7.png)

#### [Dillinger](https://dillinger.io/)
Dillinger risulta un editor Web semplice ed immediato, sfortunatamente nel provarlo dava diversi errori, sia nel D&D sia nel connessione a GitHub (*che stiano aggiornando il tutto?*)

#### [Obsidian](https://obsidian.md/)
Obsidian è un moderno software Desktop per la creazione e gestione dei file Markdown, è anche provvisto di un plugin installer. 

#### [StackEdit](https://stackedit.io/)
StackEdit risulta un applicativo Web molto immediato e semplice da usare che però non supporta nessun tipo di Upload dell'immagine.

#### [Typora](https://typora.io/)
Typora è un editor Desktop con uno stile minimal e con varie capacità di sincronizzazione.

#### [Zettrl](https://www.zettlr.com/)
Editor Desktop gratuito

#### [ZNote](https://znote.io/)
Editor Desktop gratuito


## Mantenimento

### Caricamento e visualizzazione di tutto il contenuto del LabDocs
Purtroppo Git Hub ha un limite di visualizzazione, lato front-end, di solamente 1000 file. Questo cosa comporta? che se noi abbiamo da visualizzare 1500 file, i restanti 500 che non sono visualizzabili, non possono essere visualizzati al livello di interfaccia web. Però, va precisato che nel repository questi 500 file sono presenti, infatti il manuale ufficiale di Git Hub comunicare che se uno vuole vedere i file non visualizzabili, bisogna clonare il repository sul proprio pc.

### Altre Soluzioni alternative per creare una wiki
- [Outline](https://www.getoutline.com)
- [Read The Docs](https://readthedocs.org/)
- [MKDocs](https://www.mkdocs.org/)
- [Wiki.js](https://js.wiki/)

### Fonti
Apparte vari forum la source principale per questo articolo è stata [Markdown Guide](https://www.markdownguide.org/)
