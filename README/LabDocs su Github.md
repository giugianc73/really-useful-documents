
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

LabDocs non è un nuovo database, contiene più di 1500 file, tra documentazione, tutorial e documenti vari. Bisogna trovare un metodo per trasferire tutto questo senza perdere nulla di quello scritto fino ad adesso.


I documenti su [LabDocs](https://labdocs.smeup.com/) sono scritti in **Markdown** (abbreviato in MD) un linguaggio di markup nato nel 2004 per cui, nel bene o nel male, **non è mai stato definito uno standard internazionale**.

Nel 2014 è nato [**CommonMark**](https://commonmark.org) che cercava  di unire tutte le implementazioni del linguaggio sotto un'unica bandiera, in modo da non avere più ambiguità tra i vari documenti.  
GitHub utilizza una estensione di CommonMark, detta [GitHub Flavored Markdown](https://github.github.com/gfm/), questo crea problemi di compatibilità tra i due sistemi: **non tutti i documenti LabDocs vengono visualizzati correttamente su GitHub.**  
Dobbiamo trovare e riparare tutti gli elementi in discrepanza senza dover riscrivere a mano ogni documento, *cosa oggettivamente poco fattibile data la mole di dati*.


### Elementi Discrepanti
#### Header
L'implementazione CommonMark è molto certosina per quanto riguarda spazi e *a capi*, in LabDocs non ci sono problemi se un header è scritto come `#Header` o `# Header`, in GitHub solo la versione **con `space`** viene renderizzata correttamente. Questi problemi di identazione e spaziatura sono risolvibili con un convertitore di documenti come [**Pandoc**](https://pandoc.org/).

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
- **Cloud Sync**: Il file salvato deve poter essere pushato e/o sincronizzato direttamente nella repository di GitHub.
- **Accessibile Ovunque**: Posso visualizzare ed editare i miei documenti da qualsiasi PC o Smartphone, una ***Applicazione** **Web*** è preferibile per questo.
- **Produttività**: L'inclusione di Snippet, di autocompletamento del codice e di un Render-Preview in tempo reale. Tutte risorse che rendano la stesura di un documento facile ed immediata.
- **Multi-Utente**: In LabDocs non è supportato l'editing collaborativo, questo significa che se due utenti modificano lo stesso documento l'ultimo a salvare avrà la meglio. Vogliamo un applicativo che sia in grado di gestire più **utenti in contemporanea** (editor o viewer) sullo stesso documento.
- **User-Friendly:** Una interfaccia grafica semplice e intuitiva è sempre ben gradita.

### Alcuni Editor di Nota
Vediamo alcuni applicativi che risultano (più o meno) idonei a quello che cerchiamo (*ma anche altri editor che risultano lontani da quello che ci serv*e).

#### [CodiMD](https://github.com/hackmdio/codimd/) e [HackMD](https://hackmd.io/)
Rispettivamente versione Open-Source e non, sono degli editor web, desktop ed estensioni VSCode molto versatili, con una interfaccia semplice e che permettono Drag&Drop di immagini (*anche CopyPaste*), Editing Collaborativo, e un collegamento con le repository su GitHub. Le immagini caricate vengono uploadate su [imgur](https://imgur.com) per gli utenti free, su un server Amazon S3 privato per gli utenti premium e con delle soluzioni ad-hoc per le aziende. Per i dettagli sui prezzi e le differenze tra i vari piani rifarsi a questo [link](https://hackmd.io/pricing). Il problema principale è la non possibiltà di clonare la repository nel workspace.

![Esempio di file markdown](https://i.imgur.com/CMhCB7b.png)

#### [NotesHub](https://www.noteshub.app/)
NotesHub è un applicativo Web gratuito che risulta molto semplice nella UI, permette di editare **direttamente collegandosi alla Repository** evitando di dover pushare ogni volta. Pultroppo **non permette Drag and Drop**, e permette solo di caricare le immagini in una cartella /.attachments (quindi senza suddividere automaticamente le immagini). Permette però di clonare molto comodamente l'intera repository. **Non è pensato per la collaboratività**, ma è ancora in una fase molto embrionale (*il progetto è nato a Marzo 2021*) quindi ha ancora molto tempo per crescere se supportato a dovere.

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

*... And Many More ...*

### Soluzioni alternative che si estendono al semplice Editor di Markdown
Questi software risultano molto completi per gestire una ampia mole di dati che fanno da wiki, ma nessuno di essi risulta estremamente light e sono tutti da scaricare e installare.
- [Outline](https://www.getoutline.com)
- [Boost Note](https://boostnote.io/)
- [Read The Docs](https://readthedocs.org/)
- [MKDocs](https://www.mkdocs.org/)
- [Wiki.js](https://js.wiki/)
- [Notable](https://notable.app/)


## Mantenimento
Gestire, mantenere e anche semplicemente navigare una repository cosi enorme (e che continuerà ad aumentare) è sicuramente molto complicato, per questo dobbiamo trovare un modo (con strumenti propri o un software terzo) per **muoverci all'interno di questa** complicata rete di file.

### Il Problema
La versione Web di GitHub ci permette di vedere fino a un **massimo di mille "righe"** per directory ([Documentazione GitHub](https://developer.github.com/v3/search/#about-the-search-api)), per riuscire a visualizzarle tutte bisogna clonare la repository con Git, la cosa risulta molto laboriosa, sopratutto nelle fasi iniziali, dove la quantità di dati da scaricare risulta abbastanza impegnativa, per questo dobbiamo trovare un modo di evitare di clonare la repo in locale (e successivamente aggiornarla a ogni cambiamento) o quantomeno un modo per evitare di dover clonare la repo ogni volta nella sua interezza.

### Possibili Soluzioni

#### Riorganizzare la Repository
Una soluzione bruta nel pensiero ma non banale nell'esecuzione è riorganizzare le directory, in modo che siano visibili tutte le righe nella UI Web e quindi banalmente cercare con `Ctrl+F`.

#### Utilizzare Go to File
GitHub Web mette a disposizione una ricerca file integrata:

![](https://i.imgur.com/kexyeyV.png)


#### Scalar
Una possibile soluzione è un "upgrade" di Git, un software di casa Microsoft chiamato [Scalar](https://github.com/microsoft/scalar). 
Consiste in un insieme di strumenti ed estensioni per Git per permettere di gestire Repository molto grandi.

(*Need Further Testing di tutte le soluzioni. Non ho ancora accesso a una repo cosi grande*)

## Fonti
Apparte vari forum la source principale per questo articolo è stata [Markdown Guide](https://www.markdownguide.org/)
