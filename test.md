\[\[*TOC*\]\]

# MIGRAZIONE E TEST DA LABDOCS A GITHUB

## Obiettivo

L'obiettivo di questo progetto è analizzare ed indagare se ci sono delle
anomalie in fase di migrazione. La migrazione avviene tra il LabDocs del
Gruppo SmeUP su GitHub. Potrebbe succedere che alcuni contenuti non
siano visibili. Per questo motivo serve un'analisi puntuale per scoprire
se è errore dicitura ppure dobbiamo modificare la logica.

## Svolgimento

In primi abbiamo fato con il Git GUI Bash il clone della sorgente che ci
ha comunicato il nostro responsabile. abbiamo creato un cartella nel
nostro pc e abbiamo copiato tutto il contenuto che abbiamo tirato giù da
GitHub. Facciamo questi comandi git clone (nel repository che vogliamo
storicizzare i dati) git pull (aggiorniamo la nostra cartella locale)
git add \* (aggiungiamo tutti i file che abbiamo nel locale direttamente
nel repository di GitHub) git commit -m '...' (committiamo con
l'aggiunta di un messaggio) git push (storicizziamo tutti i file su git
definitivamente)

## Contenuto che bisogna verificare

-   link pubblici che vengono indirizzati in un sito internet esterno
    (Google Drive, pagine internet, youtube, ecc...)
-   Immagini .png, .bpm, .img, ecc...
-   Navigazione da una scheda all'altro sempre rimanendo su GitHub
-   Visualizzazione delle tabelle
-   Caricamento di tutto il contenuto del LabDocs
-   TOC (Tablle Of Contenent)

## Problematiche riscontrate

#### Immagini

Nelle immagini bisogna apportare delle modifiche relative al path dove
si trova l'immagine stessa all'interno delle cartelle presente nel
repository.

Fatto questo passaggio bisogna entrare nel documento ed elimianare
l'endopoint "https://labdocs.smeup.com/" e mettere solamente il path di
dove si trova l'immagine.

#### TOC - Table Of Contenent

Durante la fase di migrazione la cosa ce non viene proprio visualizzata
è il TOC. PEr far sicchè venga mostrato senza problemi, abbiamo diversi
modi: \* crearlo a mano \* affidarsi ad un sito esterno ["GitHub Wiki
TOC generator"](https://ecotrust-canada.github.io/markdown-toc/) che
genera automaticamente in base al testo come è stato scritto

#### Caricamento e visualizzazione di tutto il contenuto del LabDocs

Purtroppo Git Hub ha un limite di visualizzazione, lato front-end, di
solamente 1000 file. Questo cosa comporta? che se noi abbiamo da
visualizzare 1500 file, i restanti 500 che non sono visualizzabili, non
possono essere visualizzati al livello di interfaccia web. Però, va
precisato che nel repository questi 500 file sono presenti, infatti il
manuale ufficiale di Git Hub comunicare che se uno vuole vedere i file
non visualizzabili, bisogna clonare il repository sul proprio pc.

#### Editor

Attualmente non è possibile creare un nuovo documento. Per risolvere
questa lacuna, va fatto una ricerca di un'applicazione o qualcosa che
mette in comunicazione con il repository tale da poter generare o
editare un documento presente su Git.

Dopo aver fatto uno studio sulle possibili scelte, siamo giunti alla
conclusione che
