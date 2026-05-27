MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 28/04/2026
FONTE: capitolo 15.01 e 15.02

---

![[Pasted image 20260428144951.png]]
In questo paradigma, il nostro obiettivo è addestrare un modello affinché sia in grado di assegnare un campione  ad una categoria discreta ben definita. In questo corso utilizzeremo il celebre **Digits dataset** di scikit-learn per affrontare un problema di **multi-classification**. Lavoreremo su quasi 1800 immagini di cifre scritte a mano, dove ogni input, ossia ogni elemento del dataset, è una matrice $8 \times 8$ di pixel e l'output deve ricadere in una delle 10 classi possibili (i numeri da 0 a 9).
Per fare ciò, partiremo dall'algoritmo **k-Nearest Neighbors (k-NN)**. È il punto di partenza perfetto per capire come i dati etichettati permettano a una macchina di "imparare" a riconoscere pattern visivi.

![[Pasted image 20260428145329.png]]

L'algoritmo k-Nearest Neighbors (k-NN) è un metodo di classificazione supervisionata che predice la classe di un campione analizzando i $k$ campioni di addestramento più vicini in termini di **distanza metrica**, ovvero una funzione matematica (come la distanza Euclidea o di Manhattan) utilizzata per quantificare la similarità spaziale tra i vettori di caratteristiche dei dati.
Il principio di funzionamento si basa su un sistema di votazione: la classe che riceve il maggior numero di preferenze tra i vicini selezionati determina l'appartenenza del nuovo dato. Per garantire una decisione univoca ed evitare situazioni di parità, è prassi comune utilizzare un valore di $k$ dispari, assicurando così che ci sia sempre una classe dominante nel conteggio dei voti.
Per X ed Y è facile prevedere la classe di appartenenza, ma per Z no. In questi casi vince la classe con il maggior numero di voti vicini all'elemento da classificare, in questo caso vincono i rossi 2v1.

![[Pasted image 20260428150327.png]]
In scikit-learn, il caricamento del dataset avviene tramite la funzione `load_digits()`, che restituisce un oggetto di tipo **Bunch**. Un oggetto Bunch è essenzialmente una struttura dati simile a un dizionario Python, ma estesa per includere metadati e attributi specifici del dataset (come le immagini grezze, i target e le descrizioni). Questa astrazione facilita l'accesso organizzato sia ai campioni delle cifre numeriche sia alle informazioni contestuali necessarie per la fase di addestramento del modello di machine learning.

![[Pasted image 20260428150633.png]]
![[Pasted image 20260515190815.png]]
Il Digits dataset utilizzato è un subset del dataset UCI ML hand-written digits e comprende i 1797 campioni originariamente destinati al test. Accedendo all'attributo **DESCR** dell'oggetto Bunch, è possibile visualizzare i metadati che descrivono la struttura dei dati: ogni campione è composto da **64 feature**, corrispondenti a un'immagine $8 \times 8$ con valori dei pixel compresi tra 0 e 16, e non presenta valori mancanti. Sebbene 64 feature possano apparire numerose, questa dimensione è contenuta rispetto ai dataset del mondo reale che possono raggiungerne milioni, richiedendo capacità di calcolo estremamente elevate per l'elaborazione.

![[Pasted image 20260515191219.png]]
All’interno dell'oggetto Bunch, gli attributi **data** e **target** sono array di float:

- L'array `data` contiene i 1797 campioni, ossia le immagini delle cifre del dataset.  Ciascuna immagine è rappresentata da un vettore 1x64, in cui ogni elemento indica l'intensità di un pixel con valori che spaziano da **0 (bianco)** a **16 (nero)**.
  
- L'array `target` contiene le etichette numeriche (da 0 a 9) associate a ogni immagine, ovvero la "verità di base" (ground truth) necessaria per l'addestramento e la valutazione del modello.

Come mostrato nello snippet di codice, è possibile accedere a questi valori tramite slicing (es. `digits.target[::100]` che ci permette di accedere ad ogni 100esimo valore) per esaminare la distribuzione delle classi nel dataset.

![[Pasted image 20260515191402.png]]
Python

```
digits.data.shape
```

- Posso capire il numero di campioni del dataset e il numero delle relative caratteristiche (features) tramite l'attributo `shape` dell'array `data`.
  L'output così ottenuto, ossia `(1797, 64)`, indica che il dataset è composto da **1797 campioni complessivi** (le righe) e che ciascun campione possiede **64 feature** (le colonne), corrispondenti ai singoli pixel di un'immagine $8 \times 8$ **linearizzati in un vettore monodimensionale 1x64.**

Python
```
digits.target.shape
```

- Per verificare che il numero di etichette reali corrisponda esattamente al numero di campioni analizzabili, si interroga l'attributo `shape` applicato all'array `target`. L'output `(1797,)` restituisce una tupla a singola dimensione che conferma la presenza di 1797 valori target, garantendo la perfetta consistenza geometrica tra gli input e le relative risposte attese prima di alimentare l'algoritmo.

![[Pasted image 20260428151915.png]]
L'oggetto Bunch del Digits dataset possiede anche l'attributo **images**, che consiste in un array contenenti le immagini dei campioni (in questo caso le cifre). Ogni elemento in questo attributo è quindi un **array 8-by-8** di float, dove ogni valore rappresenta l'intensità del pixel nella sua posizione spaziale (larghezza e altezza). Come osservabile nell'esempio relativo all'indice 13, questa rappresentazione matriciale è fondamentale per visualizzare correttamente la cifra o per applicare algoritmi di computer vision che sfruttano la topologia 2D dell'immagine.

![[Pasted image 20260428151959.png|315]]

![[Pasted image 20260428152114.png]]
Per utilizzare correttamente gli stimatori di scikit-learn, è necessario che i dati siano organizzati in una struttura bidimensionale, quindi matriciale, dove ogni riga corrisponde a un singolo campione e ogni colonna rappresenta una specifica feature.
Nel caso di dati multidimensionali, come le immagini $8 \times 8$ viste in precedenza, i campioni devono essere sottoposti a un processo di flattening per essere trasformati in array unidimensionali.
Inoltre, eventuali feature categoriche espresse tramite stringhe devono essere pre-elaborate in valori numerici, ad esempio attraverso tecniche come il one-hot encoding (che vedremo meglio più avanti), per permettere al modello di eseguire i calcoli necessari.

![[Pasted image 20260515191552.png]]
Python
```
digits.data[13]
```

- La funzione `load_digits` restituisce un dataset pre-elaborato, pronto per essere passato direttamente agli algoritmi di machine learning.
    
- L'immagine bidimensionale originale (accessibile tramite `digits.images[13]`) viene linearizzata (_flattening_) in un array monodimensionale di forma $1 \times 64$, i cui elementi numerici rappresentano l'intensità dei pixel in scala di grigi. Per vedere l'array linearizzato corrispondente basta scrivere `digits.data[13]`.

![[Pasted image 20260515191818.png]]
- È fondamentale acquisire familiarità con i propri dati prima di applicare qualsiasi modello, una fase metodologica definita esplorazione dei dati. In questo passaggio si procede alla visualizzazione grafica delle prime 24 immagini del dataset per analizzarne visivamente il contenuto strutturale.

- Per comprendere l'effettiva difficoltà del problema del riconoscimento delle cifre scritte a mano, basta osservare le marcate variazioni tra le diverse immagini del numero 3 e del numero 2 presenti nella seguente immagine.

![[Pasted image 20260515191844.png]]

Nota come uno stesso carattere spesso cambia molto, il che spiega perchè sia difficile riconoscerli per una AI.

![[Pasted image 20260515191949.png]]

Il passo successivo a tutto questo, è quello di creare il diagramma. Per farlo, guardiamo questo codice Python:

![[Pasted image 20260428155048.png]]

Per visualizzare i dati e verificare la correttezza delle etichette, si utilizza la libreria **Matplotlib**, molto utile per creare una griglia di sottografici (subplots). Attraverso un ciclo che itera simultaneamente sugli assi, sulle immagini 2D contenute in `digits.images` e sui relativi target, ogni cifra viene renderizzata con `imshow` utilizzando una mappa di colori in scala di grigi invertita (`gray_r`). Per migliorare la leggibilità del grafico ed eliminare rumore visivo superfluo, vengono rimossi i segni di graduazione (ticks) dagli assi X e Y, mentre il valore del `target` viene impostato come titolo di ogni singolo subplot, permettendo un confronto immediato tra l'immagine del pattern scritto a mano e la sua classificazione reale.

---

![[Pasted image 20260428155257.png]]
In questa fase cruciale del workflow di machine learning, utilizziamo la funzione `train_test_split` per suddividere il dataset originale in due sottoinsiemi distinti: uno per l'addestramento e uno per il testing. Questa operazione inizia con uno shuffling (mescolamento) dei dati per garantire che entrambi i set abbiano caratteristiche statistiche simili e non siano influenzati dall'ordine originale dei campioni.
La funzione restituisce una tupla di quattro elementi: i primi due contengono i campioni (feature) divisi in training e testing set, mentre gli ultimi due contengono i corrispondenti valori target (etichette) anch'essi suddivisi. Isolare una porzione di dati mai visti dal modello durante il training è fondamentale per valutare in modo imparziale le sue prestazioni su nuovi input.

![[Pasted image 20260515192243.png]]
![[Pasted image 20260515192326.png]]
Questo codice mette in pratica quello che abbiamo appena detto. I dataset presenti in scikit-learn presentano classi bilanciate. Di conseguenza i campioni sono divisi equamente tra le varie classi. Le classi non bilanciate possono portare a risultati sbagliati.

![[Pasted image 20260428155721.png]]
Di default, la funzione `train_test_split` riserva il **75% dei dati per l'addestramento** (training) e il **25% per il testing**. Come si evince dalle dimensioni degli array, ossia il training set (`X_train`) e il test set (`X_test`) . Verificare la proprietà `.shape` di questi set è un passaggio fondamentale per assicurarsi che la suddivisione sia avvenuta correttamente e che le proporzioni rispettino i requisiti necessari per una solida fase di validazione del modello.

![[Pasted image 20260428160026.png]]
Nella libreria scikit-learn, i modelli di machine learning vengono definiti **estimatori**. Per implementare l'algoritmo dei k-vicini più prossimi, si utilizza l'estimatore **KNeighborsClassifier**, che viene importato dal modulo `sklearn.neighbors`. La creazione del modello avviene istanziando un oggetto di questa classe, come mostrato nell'istruzione `knn = KNeighborsClassifier()`, che configura il classificatore pronto per essere addestrato sui dati.

![[Pasted image 20260428160207.png]]
L'addestramento dell'estimatore avviene tramite il metodo **fit**, che carica nel modello sia il set di campioni (`X_train`) sia le relative etichette target (`y_train`). Durante questa fase, il classificatore memorizza i dati per poter calcolare le vicinanze durante la predizione; in particolare, il parametro **n_neighbors** all'interno di `KNeighborsClassifier` corrisponde al valore $k$ dell'algoritmo, definendo quanti vicini debbano essere considerati per la votazione.
L'esecuzione di `knn.fit` restituisce l'oggetto classificatore stesso, confermando l'avvenuto caricamento dei dati e l'applicazione delle impostazioni necessarie per il funzionamento del modello.

![[Pasted image 20260515192554.png]]
- Il metodo `fit` , di solito, carica i dati all'interno dell'estimatore ed esegue calcoli matematici complessi per estrarre i pattern e ottimizzare i parametri interni, completando così l'effettivo processo di addestramento.
    
- Il metodo `fit` applicato a un classificatore k-NN, invece, si limita esclusivamente a caricare i dati nella memoria dell'oggetto.
    
    - Non viene avviato alcun processo di apprendimento durante questa chiamata.
        
    - L'estimatore viene definito pigro (_lazy learner_) perché l'intero carico computazionale viene svolto solo quando in cui il modello viene invocato per generare le predizioni sui nuovi dati.
    
- A differenza del k-NN, molti altri modelli (come le reti neurali profonde) richiedono fasi di addestramento intensive che possono durare minuti, ore o giorni. In questi scenari, l'utilizzo di acceleratori hardware ad alte prestazioni come GPU e TPU consente di ridurre drasticamente i tempi di elaborazione.

![[Pasted image 20260515192641.png]]
![[Pasted image 20260515192717.png]]
Nota come la previsione è sempre corretta tranne che nel penultimo elemento (prevede 5 ma la realtà è 3)

Per effettuare le previsioni è sufficiente chiamare il metodo `predict()` dell''estimatore `knn` sul dataset di test.

![[Pasted image 20260515192819.png]]

Questo codice restituisce tutte le volte in cui l'estimatore ha sbagliato previsione.



