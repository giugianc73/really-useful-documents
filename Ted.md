# Stress Test per componente TED markdown
Test effettuati su ambiente SVI-U e GESD-U.

## Alfabeti

#### UTF-8 e UTF-16
Copiando incollando il [Sample UTF-8](https://kermitproject.org/utf8.html) in prima battuta non presenta problemi e tutto viene renderizzato come nel sito, ma salvando e/o ricaricando il componente **non visualizza correttamente molti caratteri** e invece renderizza dei *?*. Qualsiasi scrittura in markdown (o html) non viene renderizzata correttamente.
Per visualizzare correttamente altri alfabeti oltre al nostro dobbiamo **inserire il codice HTML-UTF**.

#### Unicode 3.2 e older
Copiando incollando il [Test Unicode 3.2](https://www.cogsci.ed.ac.uk/~richard/unicode-sample-3-2.html) troviamo lo stesso problema di UTF-8, in particolare salva caratteri solo provenienti da questi alfabeti:
- Basic Latin
- Latin-1 Supplement

In sostanza, tutti gli alfabeti che non siano il nostro **non vengono salvati se copiati incollati direttamente** (greco, arabo, cirillico, kanji, alcuni simboli matematici, emoji ecc...) i **codici Unicode** *U+CODICE* non vengono renderizzati.

#### Extended ASCII
I codici [ASCII](https://www.ascii-code.com/) vengono renderizzati correttamente

#### HTML Symbols
I codici [HTML](https://www.htmlsymbols.xyz/), che quindi comprendono UTF e Unicode, sono ambigui. Alcuni vengono renderizzati correttamente, altri invece vengono renderizzati con il quadratino bianco o con un punto di domanda se non vengono messi in un tag HTML (come <"/span">). I Codici CSS *\CODICE* non vengono renderizzati. **Alcuni caratteri non fanno più aprire il componente :(**


## Plain Text
Se copio incollo [Moby Dick](https://www.gutenberg.org/files/2701/2701-h/2701-h.htm) non salva *o comunque ci mette troppo*.

Pensando che il problema potesse essere la formattazione ho copia incollato [1 Million Word Text](https://www.damienelliott.com/wp-content/uploads/2020/07/Lorem-ipsum-dolor-sit-amet.txt) (prima su notepad e poi sul componente) ma il risultato è lo stesso.
Riducendo man mano il numero di caratteri sembra che il limite del componente sia intorno al **milione di caratteri** di *plain text*.

## Markdown
Se copio incollo questo documento non renderizza la formattazione, ma mantiene i colori. Ciò significa che il componente **non renderizza markdown correttamente**.

## Formattazione

### Testo con Headers
Il testo da circa un milione di caratteri viene salvato anche se tutto scritto nell'header più grande.

### Testo **Bold** ~~Strikethrough~~ e *Italics*
Se il testo rientra sempre nell'ordine del milione di caratteri e utilizzo uno solo di questi tre non ha molti problemi, se invece ne utilizzo due o tre insieme (~~***Text***~~) il tempo di salvataggio aumenta (*ma comunque salva*).

### Tabelle
Sembrano funzionare correttamente. Se voglio creare una nuova tabella molto grande risulta impossibile dato che l'interfaccia si ferma dove di ferma la textbox. *vedere la sezione immagini qui sotto per capirne il senso*

### Elenchi Puntati, Numerati e CheckBox
Gli elenchi sembrano funzionare correttamente, posso anche annidare più elenchi tramite gli **ident** che sembrano funzionare correttamente.

### Testo Code Block
Il testo da circa un milione di caratteri viene salvato (anche se con alcuni bug grafici). Il codice non viene colorato (non riconosce quindi il linguaggio di programmazione).

## Inserimenti Extratestuali

### Immagini
**Il componente supporta Drag and Drop**
Le immagini di piccole dimensioni e non troppo pesanti vengono salvate subito sia da URL che da File.
Le immagini da file con risoluzione superiore al FULL HD sono molto laboriose da salvare, non ho problemi invece per immagini da link. Non posso ridimensionarle a mio piacimento. 

Inoltre, l'inserimento risulta impossibile se la TextBox non è abbastanza grande, la UI viene tagliata.

![](https://i.ibb.co/b6QKb48/Immagine-2021-11-11-110429.png)
![](https://i.ibb.co/VjYRdn2/Immagine-2021-11-11-110429-2.png) 

### Link
Vengono inseriti correttamente, ma non sono direttamente cliccabili. Non so se il problema è del componente o che non siamo in un "vero render", ma per aprire il link devo utilizzare perforza *tasto destro -> apri link in un'altra scheda*.

