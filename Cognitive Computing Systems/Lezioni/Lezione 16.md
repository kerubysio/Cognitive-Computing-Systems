MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 19/05/2026
FONTE: 
RIASSUNTO: [[Lezione 16 - Riassunto]]

--- 

![[Pasted image 20260519144418.png]]
La gestione dei dati all'interno di un'applicazione cognitiva si sviluppa attraverso due fasi sequenziali e interconnesse: l'**Acquisizione delle fonti di dati** e la **Creazione e raffinamento del corpora**. La prima fase richiede un bilanciamento strategico tra sorgenti strutturate interne, l'analisi di dati archiviati ma storicamente ignorati definiti "dati nascosti", e l'integrazione di fonti esterne organizzate tramite tassonomie e ontologie di dominio. Questo flusso alimenta direttamente il nucleo della gestione dei dati, dove le informazioni subiscono un processo continuo di preparazione e _ingestion_ in tempo reale per garantirne la leggibilità, la ricercabilità e la comprensibilità. Il patrimonio informativo viene poi ciclicamente espanso e raffinato per massimizzare l'accuratezza del sistema, operando sotto una rigorosa _governance_ per il rispetto dei requisiti di privacy e sicurezza.

![[Pasted image 20260519144705.png]]
Dove $tf\_env$ sarebbe l'environment di Anaconda in cui hai installato tensorflow.

La funzione $load\_data$ restituisce una tupla di tuple contenenti campioni ed etichette di training e testing.

![[Pasted image 20260519144841.png]]
Questa verifica garantisce la corretta consistenza dimensionale tra le caratteristiche estratte e i target attesi, ponendo le basi per la successiva configurazione dei tensori nella rete neurale.

![[Pasted image 20260519145040.png]]
![[Pasted image 20260519145333.png]]
![[Pasted image 20260519145643.png|395]]
Lo script implementa la logica per estrarre e visualizzare graficamente un campione casuale di 24 immagini dal dataset MNIST, evidenziando empiricamente la variabilità e la complessità intrinseca nel riconoscimento delle cifre scritte a mano.

Per ogni iterazione, la singola immagine viene renderizzata in scala di grigi invertita (`cmap=plt.cm.gray_r`) mediante la funzione `imshow`, e la cifra numerica corretta viene impostata come titolo del riquadro (`set_title(target)`). Infine, l'istruzione `plt.tight_layout()` ottimizza automaticamente la spaziatura tra i sotto-grafici per evitare sovrapposizioni del testo, mostrando a schermo la forte eterogeneità di stile, spessore e inclinazione con cui diversi utenti umani scrivono lo stesso numero.

![[Pasted image 20260519150044.png]]
Durante la **Data Preparation**, la sezione dedicata al **Reshaping the Image Data** descrive i requisiti geometrici fondamentali per convertire le matrici bidimensionali grezze in strutture compatibili con i modelli di deep learning di Keras.

Le reti neurali convoluzionali (_convnets_) sviluppate in questo framework richiedono tassativamente input strutturati come array NumPy, in cui ogni singolo campione deve presentare una forma tridimensionale definita dalla triade `(width, height, channels)`, dove per channel si intende il valore del pixel in scala di grigi, quindi da 0 fino a 255.
Man mano che la rete neurale esegue il processo di apprendimento sui dati somministrati, gli strati convoluzionali generano progressivamente un numero molto più elevato di canali intermedi; questi canali interni non rappresentano più semplici mappe di intensità luminosa, bensì feature via via più complesse e astratte, come bordi, curve e linee, la cui combinazione logica consente alla rete di riconoscere e classificare correttamente le cifre scritte a mano.

![[Pasted image 20260519150325.png]]
Il codice mostra l'applicazione pratica del metodo `reshape` degli array NumPy per riconfigurare geometricamente le dimensioni dei dataset di addestramento e di test secondo i requisiti di Keras. Il metodo riceve come argomento una tupla che definisce la nuova forma tridimensionale estesa per includere il canale singolo della scala di grigi.
Operativamente, l'istruzione `X_train.reshape((60000, 28, 28, 1))` rimodella la struttura originaria delle immagini di addestramento, e la successiva invocazione di `X_train.shape` ne conferma la corretta trasformazione in un tensore a quattro dimensioni composto da 60.000 campioni, ciascuno avente larghezza e altezza di 28 pixel e 1 singolo canale di colore. La medesima procedura viene applicata al dataset di valutazione tramite il comando `X_test.reshape((10000, 28, 28, 1))`, il cui output dimensionale verificato da `X_test.shape` attesta la conversione finale nei 10.000 campioni tridimensionali richiesti per la successiva fase di feeding della rete neurale convoluzionale.

![[Pasted image 20260519150428.png]]
![[Pasted image 20260519150624.png]]
È di fondamentale importanza standardizzare i valori numerici delle feature prima di sottoporli a un modello di deep learning, poiché i valori grezzi possono variare su intervalli molto ampi. Le reti neurali profonde garantiscono prestazioni e velocità di convergenza nettamente superiori quando lavorano su dati preventivamente normalizzati all'interno dell'intervallo standard compreso tra 0.0 e 1.0, oppure scalati in un range in cui la media statistica della distribuzione sia pari a 0.0 e la deviazione standard sia uguale a 1.0.
Operativamente, nel contesto del trattamento di immagini digitali a 8 bit come quelle del dataset MNIST, la tecnica più immediata per effettuare questa riscalatura consiste nel dividere matematicamente il valore di intensità di ogni singolo pixel per il valore massimo consentito, ovvero 255, convertendo così l'intervallo originario di interi [0, 255] nel range continuo in virgola mobile [0.0, 1.0] richiesto per l'ottimizzazione dell'addestramento.

![[Pasted image 20260519150648.png]]
Il **One-Hot Encoding** descrive il processo di trasformazione dei target numerici necessario per consentire a Keras di calcolare correttamente l'accuratezza del modello durante la classificazione multiclasse. Poiché la rete neurale convoluzionale restituisce per ogni cifra un array di 10 probabilità distinte (una per ciascuna classe da 0 a 9), sorge l'esigenza di uniformare la struttura geometrica delle etichette del dataset originale, che inizialmente sono memorizzate come singoli numeri interi compresi tra 0 e 9.
Per rendere confrontabili gli output predetti con i target reali, le etichette intere devono essere trasformate in array categorici speculari tramite la tecnica del _one-hot encoding_, la quale converte ogni numero intero in un vettore a 10 elementi composto interamente da valori pari a 0.0, a eccezione dell'indice corrispondente alla cifra rappresentata, che assume il valore di 1.0 (come mostrato nell'esempio in cui il numero 7 viene codificato inserendo un'unità esclusivamente in ottava posizione).
All'interno del framework, questa operazione di preelaborazione categorica viene eseguita in modo diretto richiamando la funzione `to_categorical` contenuta nel modulo `tensorflow.keras.utils`.

---

![[Pasted image 20260519151052.png]]
Per creare una cnn si ricorre al modello Sequential di keras, perchè ci permette di impilare, ossia mettere in uno stack, diversi layer da eseguire sequenzialmente. In questa impostazione classica da rete _feed-forward_, l'output generato da un livello diventa direttamente l'input per lo strato successivo, sebbene venga anticipato che non tutte le tipologie di livelli avanzati trasferiscono necessariamente l'output al livello immediatamente adiacente. 

![[Pasted image 20260519151207.png]]
Una struttura _convnet_ standard si compone di tre macro-livelli fondamentali: un _input layer_, il cui compito è ricevere i campioni del dataset di addestramento; una serie di _hidden layers_ (strati nascosti), che estraggono le caratteristiche e apprendono attivamente dai dati somministrati; e infine un _output layer_, deputato a generare le predizioni finali del modello. Dobbiamo quindi aggiungere questi layer alla rete neurale.

![[Pasted image 20260519151353.png]]
Iniziamo con il livello Convoluzionale.
Il processo di elaborazione di una rete neurale convoluzionale (CNN) parte da questo livello, il quale sfrutta la vicinanza spaziale tra i pixel per riconoscere e apprendere caratteristiche visive specifiche (feature o pattern) all'interno di piccole aree delimitate dell'immagine.

Per illustrare il meccanismo matematico in modo più chiaro, possiamo analizzare un'operazione di convoluzione applicata a un'immagine di esempio formata da una griglia di 6x6 pixel. Su questa griglia interviene il cosiddetto "kernel" (o filtro di convoluzione), rappresentato nell'esempio da un'area quadrata di 3x3 pixel. L'operazione di convoluzione consiste nel sovrapporre questo filtro all'immagine: l'algoritmo analizza le informazioni contenute nei 9 pixel racchiusi in quel momento all'interno dell'area del kernel e, tramite un calcolo matematico, le condensa restituendo in output una singola nuova caratteristica. Scorrendo sull'intera immagine, questo processo genera una serie di nuovi valori condensati che andranno a comporre una nuova matrice, la "feature map" finale.

![[Pasted image 20260519151518.png]]
Muovendosi passo dopo passo da sinistra verso destra, il kernel analizza l'intera immagine originale. Questo processo di scansione bidimensionale si ripete riga per riga fino a coprire l'intera area d'ingresso, trasformando così la matrice iniziale 6x6 in una feature map finale da 4x4 pixel, composta complessivamente da 16 sotto-aree di feature estratte.

![[Pasted image 20260519152120.png]]
Il passaggio completo del kernel sull'intera immagine, muovendosi da sinistra a destra e dall'alto verso il basso, definisce l'azione di un singolo filtro. Dal punto di vista matematico, quando si utilizza un kernel con dimensioni 3x3, la matrice di output risultante presenterà una larghezza e un'altezza ridotte di due unità rispetto alle dimensioni originali dell'input; nel caso specifico delle immagini del dataset MNIST da 28x28 pixel, l'estrazione genererà quindi una mappa di feature da 26x26 pixel.
All'interno di un livello convoluzionale standard progettato per elaborare immagini di piccole dimensioni, il numero complessivo di filtri applicati contemporaneamente varia solitamente tra 32 e 64, dove ciascun filtro apprende e produce risultati differenti estratti dagli stessi dati d'ingresso. All'aumentare della risoluzione dei campioni grafici cresce proporzionalmente la quantità di dettagli e feature complesse presenti, richiedendo di conseguenza un numero più elevato di filtri integrati.

![[Pasted image 20260519152239.png]]
L'insieme delle risposte prodotte dai singoli filtri all'interno di un livello convoluzionale prende il nome di _feature map_. Gli strati convoluzionali successivi hanno il compito di combinare progressivamente le caratteristiche estratte dalle mappe precedenti, permettendo al modello di riconoscere pattern e feature via via più grandi e complesse; ad esempio, in un sistema di riconoscimento facciale, i primi livelli identificano elementi geometrici semplici come linee, bordi e curve, mentre i livelli successivi aggregano questi dati per riconoscere strutture complesse quali occhi, sopracciglia, nasi, orecchie e bocche. Una volta che la rete ha appreso una determinata caratteristica, diventa in grado di riconoscerla in qualsiasi punto dell'immagine, indipendentemente dalla sua posizione spaziale; questa proprietà di invarianza alla traslazione costituisce uno dei motivi principali per cui le reti neurali convoluzionali (_convnets_) sono ampiamente utilizzate e popolari nel campo dell'object recognition.

Qualche slide prima, abbiamo visto questo snippet di codice utile per dare vari livelli ad una rete convoluzionale:
![[Pasted image 20260519155709.png]]
Il blocco di codice mostra l'importazione delle classi fondamentali necessarie alla costruzione di un'architettura di rete neurale convoluzionale (_convnet_) mediante il modulo `layers` del framework Keras. L'istruzione `from tensorflow.keras.layers import Conv2D, Dense, Flatten, MaxPooling2D` carica nello script i quattro blocchi costruttivi essenziali per i dati d'ingresso.

![[Pasted image 20260519155855.png]]
L'istruzione `cnn.add(Conv2D(filters=64, kernel_size=(3, 3), activation='relu', input_shape=(28, 28, 1)))` istanzia lo strato convoluzionale introducendo quattro argomenti fondamentali per l'estrazione delle caratteristiche:

- Il parametro `filters=64` specifica il numero di filtri indipendenti che verranno applicati;
- Il parametro `kernel_size=(3, 3)` stabilisce le dimensioni spaziali dei vari kernel;
- L'argomento `activation='relu'` imposta la funzione di attivazione _Rectified Linear Unit_ per introdurre non linearità nell'output del livello. La funzione **ReLU** è definita matematicamente come $f(x) = \max(0, x)$, il che significa che lascia invariati i valori positivi e azzera completamente tutti i valori negativi in uscita dalle convoluzioni;
- Infine, il parametro `input_shape=(28, 28, 1)` definisce larghezza, altezza e canali dei campioni di input, un requisito obbligatorio esclusivamente per il primo livello della struttura per consentire alla rete di allocare correttamente i pesi sinaptici iniziali.

![[Pasted image 20260519160029 1.png]]
Quando si configura il primo livello della struttura, è necessario specificare esplicitamente la forma dei campioni d'ingresso tramite il parametro `input_shape=(28, 28, 1)`. Dal punto di vista architetturale, questa istruzione genera implicitamente un _input layer_ fittizio incaricato esclusivamente di caricare i campioni in memoria e di trasmetterli direttamente allo strato `Conv2D`, il quale costituisce a tutti gli effetti il primo vero livello nascosto (_first hidden layer_) della rete.
Per tutti i livelli inseriti successivamente all'interno della struttura, Keras è in grado di dedurre e calcolare automaticamente il rispettivo `input_shape` ereditandolo direttamente dalla forma dell'output generata dal livello immediatamente precedente. Questa proprietà di inferenza dimensionale automatica semplifica notevolmente la progettazione del codice, rendendo estremamente immediato lo _stacking_ in sequenza di numerosi strati convoluzionali, di pooling o densi senza dover calcolare manualmente le contrazioni geometriche dei tensori intermedi.

![[Pasted image 20260519160118.png]]
I campioni d'ingresso originari presentano una struttura tridimensionale pari a 28x28x1, traducendosi in un totale iniziale di 784 feature per ogni singola immagine. Avendo configurato il livello convoluzionale con 64 filtri indipendenti e un kernel da 3x3 pixel, la dimensione della feature map è pari a 26x26x64, per un totale finale di 43.264 feature estratte.
Questo fenomeno determina un incremento significativo della dimensionalità, un volume enormemente superiore rispetto al numero di feature tipicamente elaborate nei modelli di Machine Learning classico. Poiché ogni strato nascosto aggiuntivo estrae e stratifica nuove caratteristiche, la dimensione delle mappe tende a crescere in modo rilevante lungo i canali interni; tale proliferazione dei parametri rappresenta una delle ragioni principali per cui i modelli di deep learning richiedono una potenza di calcolo e risorse computazionali estremamente elevate per completare le fasi di addestramento e ottimizzazione.

---

![[Pasted image 20260519160249.png]]
L'overfitting si verifica quando un modello presenta un'architettura eccessivamente complessa rispetto alla natura dei dati che deve mappare, portando la rete, nel caso più estremo, a memorizzare passivamente le feature specifiche del dataset di addestramento anziché apprenderne le regole di generalizzazione. Nel deep learning, questa deriva tende a manifestarsi con frequenza elevata a causa dell'esplosione della dimensionalità dei tensori interni, un fenomeno che non solo inficia le capacità predittive del sistema su dati estranei, ma incrementa in modo esponenziale i tempi di calcolo necessari. Specificamente, quando si esegue il training di modelli di deep learning sfruttando esclusivamente l'architettura hardware delle CPU, i tempi di elaborazione possono dilatarsi fino a diventare intollerabilmente lenti e insostenibili.
Per ovviare a queste limitazioni ed evitare il collasso delle prestazioni, si rende necessario introdurre diverse tecniche di regolarizzazione e mitigazione dell'overfitting: noi useremo il **pooling**.

![[Pasted image 20260519160556.png]]
Per mitigare il rischio di overfitting e contrarre drasticamente i tempi di calcolo, uno strato convoluzionale viene tipicamente seguito da uno o più livelli deputati a ridurre la dimensionalità spaziale delle feature map uscenti da esso. L'operazione di _pooling_, effettuata da un Pooling Layer, comprime il volume informativo scartando deliberatamente le caratteristiche meno rilevanti. È  un processo di astrazione e sintesi che costringe il modello a focalizzarsi solo sulle informazioni salienti, migliorando l'abilità di generalizzazione della rete su dati non ancora visti.
La tecnica di pooling più diffusa nel deep learning prende il nome di **max pooling**: questa procedura esamina la feature map suddividendola in sotto-aree quadrate da 2x2 pixel, analizza i valori numerici racchiusi in ciascuna di esse e conserva all'interno della matrice di output esclusivamente il valore massimo rilevato, eliminando l'attivazione dei pixel circostanti.

![[Pasted image 20260519160933.png|773]]
Questo processo di scansione a blocchi disgiunti si ripete simmetricamente per ogni riga, dimezzando le coordinate spaziali del tensore: la matrice iniziale da 6x6 pixel viene compressa in una nuova matrice da 3x3 pixel che conserva unicamente i picchi di attivazione delle feature estratte.

![[Pasted image 20260519161147.png]]
L'algoritmo estrae la caratteristica con il valore massimo da ciascuna finestra isolata (_pool_) garantendo che le aree di scansione (pools) non si sovrappongano tra di loro; per ottenere questo comportamento disgiunto con una finestra tridimensionale da 2x2 elementi, il passo di scorrimento orizzontale e verticale (stride) viene impostato automaticamente a 2.
Poiché ogni gruppo logico di quattro pixel attigui viene sintetizzato e ridotto a un singolo valore numerico, l'applicazione del 2x2 max pooling comprime il numero delle feature del 75%. Questo processo dimezza sia la larghezza che l'altezza del tensore d'ingresso, riducendo le dimensioni della mappa generata dal livello precedente da 26x26x64 a 13x13x64 elementi, mantenendo del tutto invariata la terza dimensione relativa alla profondità dei canali.

![[Pasted image 20260519161456.png]]
Le architetture convoluzionali complesse prevedono tipicamente l'inserimento in cascata di molteplici strati di convoluzione e pooling; in particolare, i modelli sviluppati dal team di Keras tendono a **raddoppiare** il numero dei filtri nei livelli convoluzionali successivi (passando in questo caso da 64 a `filters=128`) per incrementare la capacità della rete di apprendere relazioni e correlazioni più astratte tra le feature map estratte. Operativamente, l'input che alimenta questo secondo blocco è rappresentato dal tensore tridimensionale da 13x13x64 uscente dal primo livello di max pooling.
L'applicazione dell'istruzione `cnn.add(Conv2D(filters=128, kernel_size=(3, 3), activation='relu'))` riduce la risoluzione spaziale di due unità a causa del kernel 3x3 privo di padding, generando un output intermedio pari a 11x11x128. Successivamente, l'integrazione del secondo livello di pooling tramite il comando `cnn.add(MaxPooling2D(pool_size=(2, 2)))` deve gestire una matrice a dimensionalità dispari (11x11): i livelli di pooling di Keras risolvono questa asimmetria applicando di default un arrotondamento per difetto (_round down_), escludendo l'ultima riga e l'ultima colonna della griglia per ricondursi a un'area parziale da 10x10 pixel. Di conseguenza, il dimezzamento spaziale operato dalla finestra 2x2 produce un tensore compresso finale pari a 5x5x128 elementi, riducendo significativamente il numero di parametri planari e preservando l'intera profondità dei 128 canali informativi.

![[Pasted image 20260519161842.png]]
Poiché l'output definitivo del modello deve essere un array monodimensionale composto da 10 probabilità distinte per classificare le cifre da 0 a 9, si rende indispensabile "appiattire" (_flatten_) il tensore tridimensionale uscente dall'ultimo livello di pooling prima di poter calcolare le predizioni. 
Di conseguenza, l'output generato dall'ultimo Pooling Layer viene dato in input ad un Flattening Layer che lo trasforma in una matrice monodimensionale da 1x3200 feature totali.

![[Pasted image 20260519162116.png]]
Vediamo adesso che cosa fa invece il Dense Layer.
Mentre i livelli posizionati a monte dello strato di _Flatten_ avevano il compito esclusivo di scansionare le immagini per apprenderne le feature visive locali, la rete deve adesso imparare le relazioni globali intercorrenti tra queste caratteristiche per poter determinare quale cifra numerica rappresenti effettivamente ogni immagine d'ingresso.
Questo obiettivo viene conseguito mediante l'inserimento di livelli nascosti di tipo **Dense** completamente connessi, in cui ogni singolo nodo riceve l'input da tutti i nodi dello strato precedente.
Operativamente, l'istruzione `cnn.add(Dense(units=128, activation='relu'))` alloca uno strato composto da 128 neuroni indipendenti (definiti anche _units_), i quali elaborano in parallelo i 3200 output linearizzati provenienti dal livello di srotolamento. Questo passaggio intermedio permette di condensare in modo significativo lo spazio delle feature (da 3200 a 128 dimensioni) e introduce un ulteriore livello di astrazione non lineare grazie all'applicazione della funzione di attivazione ReLU, preparando il flusso informativo per la decisione probabilistica finale.

![[Pasted image 20260519162330.png]]
La presenza di **almeno un livello di tipo Dense** inserito verso la fine della struttura costituisce uno standard per la stragrande maggioranza delle _convnets_, in quanto necessario per mappare i pattern visivi estratti in una decisione finale. Quando le reti neurali vengono progettate per elaborare dataset di immagini decisamente più complessi e a risoluzione molto più elevata rispetto a MNIST, come ad esempio **ImageNet**, che vanta un archivio di oltre 14 milioni di immagini categorizzate, l'architettura richiede una capacità computazionale proporzionalmente superiore. In questi scenari avanzati, i modelli integrano spesso **diversi livelli Dense in cascata**, i quali arrivano a impiegare strutture massicce composte comunemente da **4096 neuroni** per ciascuno strato intermedio.

![[Pasted image 20260519162357.png]]
L'ultimo livello della struttura è un livello di tipo **Dense** completamente connesso, configurato con un numero di unità esattamente speculare al totale delle classi target presenti nel dataset. Nel contesto del riconoscimento di cifre scritte a mano (MNIST), vengono istanziati **10 neuroni**, dove ciascun nodo rappresenta in modo univoco una classe numerica compresa nell'intervallo tra 0 e 9.
Operativamente, lo strato viene aggiunto tramite l'istruzione `cnn.add(Dense(units=10, activation='softmax'))`. Per produrre il verdetto finale, questo livello utilizza la funzione di attivazione **softmax**. Il compito matematico della softmax è prendere i valori grezzi uscenti dai 10 neuroni e schiacciarli all'interno di una distribuzione di probabilità che rispetta due vincoli fondamentali:

1. Il valore di output di ciascun neurone è compreso rigorosamente tra 0.0 e 1.0.
    
2. La somma algebrica delle probabilità restituite da tutti e 10 i neuroni è sempre pari a 1.0 (ovvero al 100%).

Grazie a questa normalizzazione esponenziale, il neurone che esprime il **valore di probabilità più elevato** determina la predizione ufficiale del modello per l'immagine analizzata. Ad esempio, se l'ottavo neurone (corrispondente all'indice della cifra 7) restituisce una probabilità del 0.94, la rete indicherà il numero 7 come classificazione finale del campione con un livello di confidenza del 94%.