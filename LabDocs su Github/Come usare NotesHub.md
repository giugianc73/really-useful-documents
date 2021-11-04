# NotesHub
[NotesHub](https://about.noteshub.app/) è un editor di file Markdown molto semplice e leggero. Utilizzabile praticamente ovunque, sia su **PC** che su **Telefono**, è ottimo per gestire la documentazione di LabDocs. Vediamo come utilizzarlo in breve.

## Collegare NotesHub a GitHub
Giovanni (come dice il nome) è completamente sincronizzabile con GitHub, tutti i file che andremo a creare saranno salvati direttamente li.
1 - Colleghiamoci a [NotesHub](https://www.noteshub.app/notebooks) e, clicchiamo sul simbolo **+** in basso a destra.
2 - Selezionamo GitHub come **Provider Name** e, cliccando su **connect**, eseguiamo il login a GitHub.
3 - Nella sezione **Notebook name** selezionamo la Repository su cui vogliamo creare i nostri documenti.

L'applicazione ora sincronizzerà l'intera repository in un suo Workspace dedicato (*per librerie molto grandi ci mette un po' non preoccupatevi*), da qui possiamo creare i nostri nuovi file.md.
![image](/.attachments/faaa0868fafb8dc54fe42dcb7979a506bf66d958.png)

## Creare un nuovo documento
NotesHub funziona a cartelle, ovvero non ci lascierà mai creare un file nella root della repo, per questo **se non vedete alcuni file, questo è perchè sono nella root e non in una cartella**. Questo è un male nel bene, nel senso che ci obbliga a tenere il nostro Workspace in ordine, e ci obbliga a sistemare quegli vecchi.

![image](/.attachments/c35528ed09cb4e41a5fdb70513cc938b2225cfe7.png)

Creiamo o navigiamo nella nostra cartella e creiamo un nuovo file (non serve l'estensione .md). Una volta finito basta salvare con la spunta in alto a destra e abbiamo finito, il nostro file viene pushato su GitHub automaticamente. Nelle impostazioni è anche possibile attivare la Sync automatica del documento.

## Il caricamento delle immagini
NotesHub **non supporta Drag And Drop**, supporta un caricamento statico (tramite l'icona dell'immagine nella UI) o tramite **Copy Pase** dalla Clipboard. Le immagini vengono automaticamente caricate nella cartella *.attachments* **che si trova nella root della repository**, utilizzando un suo modo (*poco human-friendly*) per rendere il nome delle immagini univoche. Questo comporta che **tutte le immagini di tutti i documenti di una repo finiscano in una sola cartella**. Sta a voi scegliere se lasciare che il caos domini oppure andare a sistemare manualmente il problema.

![image](/.attachments/338eef7e32a289383681c4a201b4a1a7e207047f.png)

## Merging e Collaboratività
NotesHub funziona anche offline, quindi se Interet non funziona e devo editare assoutamente un file posso farlo, il file viene sincronizzato una volta tornati online. E se qualcun'altro ha editato il vostro stesso file mentre eravate Offline? Non dobbiamo preoccuparti, in quanto NotesHub gestisce automaticamente questo problema facendo un Merge che risolve il conflitto! Il risultato sarà come il seguente:
![](https://about.noteshub.app/images/features/desktop/merge-conflicts-auto-resolution.png).

Notare che però NotesHub **non supporta ancora la collaboratività in Real-Time**.

## Il problema con LabDocs
Elenchiamo ora i problemi nell'utilizzare questo applicativo Web con la Repository di LabDocs:
- Non è organizzata, la maggiorparte dei file si trova nella Root e quindi **non sono visibili da NotesHub**.
- **Tutte le immagini vengono caricate nella stessa cartella** con nomi indicibili, quindi dobbiamo a mano sistemare ogni volta che carichiamo una immagine.
- Manca una barra di ricerca in NotesHub, devo perforza usare Ctrl+F per cercare quello che voglio, inoltre non posso cercare per parole chiave, ma solo per nome della directory.