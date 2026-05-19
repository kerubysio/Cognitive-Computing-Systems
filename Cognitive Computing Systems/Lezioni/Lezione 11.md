MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 28/04/2026
FONTE: capitolo 15.01 e 15.02

---

!!! LEZIONE DA RIVEDERE (mancano i commenti ad alcune slides) 

![[Pasted image 20260428144951.png]]

In questo paradigma, il nostro obiettivo è addestrare un modello affinché sia in grado di assegnare un campione di input a una categoria discreta ben definita. Mentre nella classificazione binaria ci limitiamo a due sole opzioni, in questo corso utilizzeremo il celebre **Digits dataset** di scikit-learn per affrontare un problema di **multi-classification**. Lavoreremo su quasi 1800 immagini di cifre scritte a mano, dove ogni input è una matrice $8 \times 8$ di pixel e l'output deve ricadere in una delle 10 classi possibili (i numeri da 0 a 9). Per fare ciò, partiremo dall'algoritmo **k-Nearest Neighbors (k-NN)**: un approccio non parametrico e intuitivo che basa la predizione sulla "vicinanza" metrica tra i dati nel feature space. È il punto di partenza perfetto per capire come i dati etichettati permettano a una macchina di "imparare" a riconoscere pattern visivi.

![[Pasted image 20260428145329.png]]

L'algoritmo k-Nearest Neighbors (k-NN) è un metodo di classificazione supervisionata che predice la classe di un campione analizzando i $k$ campioni di addestramento più vicini in termini di **distanza metrica**, ovvero una funzione matematica (come la distanza Euclidea o di Manhattan) utilizzata per quantificare la similarità spaziale tra i vettori di caratteristiche dei dati. Il principio di funzionamento si basa su un sistema di votazione: la classe che riceve il maggior numero di preferenze tra i vicini selezionati determina l'appartenenza del nuovo dato. Per garantire una decisione univoca ed evitare situazioni di parità (ties), è prassi comune utilizzare un valore di $k$ dispari, assicurando così che ci sia sempre una classe dominante nel conteggio dei voti.
Per X ed Y è facile prevedere la classe di appartenenza, ma per Z no. In questi casi vince la classe con il maggior numero di voti vicini all'elemento da classificare, in questo caso vincono i rossi 2v1.

![[Pasted image 20260428150327.png]]

In scikit-learn, il caricamento del dataset avviene tramite la funzione `load_digits()`, che restituisce un oggetto di tipo **Bunch**. Un oggetto Bunch è essenzialmente una struttura dati simile a un dizionario Python, ma estesa per includere metadati e attributi specifici del dataset (come le immagini grezze, i target e le descrizioni). Questa astrazione facilita l'accesso organizzato sia ai campioni delle cifre numeriche sia alle informazioni contestuali necessarie per la fase di addestramento del modello di machine learning.

![[Pasted image 20260428150633.png]]
![[Pasted image 20260515190815.png]]
Il Digits dataset utilizzato è un subset del dataset UCI ML hand-written digits e comprende i 1797 campioni originariamente destinati al test. Accedendo all'attributo **DESCR** dell'oggetto Bunch, è possibile visualizzare i metadati che descrivono la struttura dei dati: ogni campione è composto da **64 feature**, corrispondenti a un'immagine $8 \times 8$ con valori dei pixel compresi tra 0 e 16, e non presenta valori mancanti. Sebbene 64 feature possano apparire numerose, questa dimensione è contenuta rispetto ai dataset del mondo reale che possono raggiungerne milioni, richiedendo capacità di calcolo estremamente elevate per l'elaborazione.

![[Pasted image 20260515191219.png]]

All’interno dell'oggetto Bunch, gli attributi **data** e **target** sono memorizzati come array NumPy, garantendo un'elaborazione efficiente. L'array `data` contiene i 1797 campioni, dove ciascuna immagine è rappresentata da un vettore di **64 feature** che indicano l'intensità dei pixel, con valori che spaziano da **0 (bianco)** a **16 (nero)**. Parallelamente, l'array `target` contiene le etichette numeriche (da 0 a 9) associate a ogni immagine, ovvero la "verità di base" (ground truth) necessaria per l'addestramento e la valutazione del modello; come mostrato nello snippet di codice, è possibile accedere a questi valori tramite slicing (es. `digits.target[::100]` che ci permette di accedere ad ogni 100esimo valore) per esaminare la distribuzione delle classi nel dataset.

![[Pasted image 20260515191402.png]]

(AGGIUNGERE COMMENTO DI GEMINI/CHATGPT SE NECESSARIO)

![[Pasted image 20260428151915.png]]
Oltre all'attributo unidimensionale dei dati, l'oggetto Bunch del Digits dataset espone l'attributo **images**, che preserva la natura bidimensionale originale dei campioni. Ogni elemento in questo attributo è un **array 8-by-8** di tipo NumPy **float64**, dove ogni valore rappresenta l'intensità del pixel nella sua posizione spaziale (larghezza e altezza). Come osservabile nell'esempio relativo all'indice 13, questa rappresentazione matriciale è fondamentale per visualizzare correttamente la cifra o per applicare algoritmi di computer vision che sfruttano la topologia 2D dell'immagine.

![[Pasted image 20260428151959.png|315]]

![[Pasted image 20260428152114.png]]

Per utilizzare correttamente gli stimatori di scikit-learn, è necessario che i dati siano organizzati in una struttura bidimensionale (array 2D, lista di liste o DataFrame pandas), dove ogni riga corrisponde a un singolo campione e ogni colonna rappresenta una specifica feature. Nel caso di dati nativamente multidimensionali, come le immagini $8 \times 8$ viste in precedenza, i campioni devono essere sottoposti a un processo di flattening per essere trasformati in array unidimensionali (vettori di feature). Inoltre, eventuali feature categoriche espresse tramite stringhe devono essere pre-elaborate in valori numerici, ad esempio attraverso tecniche come il one-hot encoding (che vedremo meglio più avanti), per permettere al modello di eseguire i calcoli necessari.

![[Pasted image 20260515191552.png]]

(AGGIUNGERE COMMENTO DI GEMINI/CHATGPT SE NECESSARIO)

![[Pasted image 20260515191818.png]]

(AGGIUNGERE COMMENTO DI GEMINI/CHATGPT SE NECESSARIO)

![[Pasted image 20260515191844.png]]

Nota come uno stesso carattere spesso cambia molto, il che spiega perchè sia difficile riconoscerli per una AI.

![[Pasted image 20260515191949.png]]

Il passo successivo a tutto questo, è quello di creare il diagramma. Per farlo, guardiamo questo codice Python:

![[Pasted image 20260428155048.png]]

Per visualizzare i dati e verificare la correttezza delle etichette, si utilizza la libreria **Matplotlib**, molto utile per creare una griglia di sottografici (subplots). Attraverso un ciclo che itera simultaneamente sugli assi, sulle immagini 2D contenute in `digits.images` e sui relativi target, ogni cifra viene renderizzata con `imshow` utilizzando una mappa di colori in scala di grigi invertita (`gray_r`). Per migliorare la leggibilità del grafico ed eliminare rumore visivo superfluo, vengono rimossi i segni di graduazione (ticks) dagli assi X e Y, mentre il valore del `target` viene impostato come titolo di ogni singolo subplot, permettendo un confronto immediato tra l'immagine del pattern scritto a mano e la sua classificazione reale.

---

![[Pasted image 20260428155257.png]]

In questa fase cruciale del workflow di machine learning, utilizziamo la funzione `train_test_split` per suddividere il dataset originale in due sottoinsiemi distinti: uno per l'addestramento e uno per il testing. Questa operazione inizia con uno shuffling (mescolamento) dei dati per garantire che entrambi i set abbiano caratteristiche statistiche simili e non siano influenzati dall'ordine originale dei campioni. La funzione restituisce una tupla di quattro elementi: i primi due contengono i campioni (feature) divisi in training e testing set, mentre gli ultimi due contengono i corrispondenti valori target (etichette) anch'essi suddivisi. Isolare una porzione di dati mai visti dal modello durante il training è fondamentale per valutare in modo imparziale le sue prestazioni su nuovi input.

![[Pasted image 20260515192243.png]]
![[Pasted image 20260515192326.png]]

(AGGIUNGERE COMMENTO DI GEMINI/CHATGPT SE NECESSARIO)

![[Pasted image 20260428155721.png]]

Di default, la funzione `train_test_split` riserva il **75% dei dati per l'addestramento** (training) e il **25% per il testing**. Come si evince dalle dimensioni degli array, su un totale di 1797 campioni, il training set (`X_train`) ne riceve **1347**, mentre il test set (`X_test`) ne riceve **450**, mantenendo per entrambi la struttura a **64 feature**. Verificare la proprietà `.shape` di questi set è un passaggio fondamentale per assicurarsi che la suddivisione sia avvenuta correttamente e che le proporzioni rispettino i requisiti necessari per una solida fase di validazione del modello.

![[Pasted image 20260428160026.png]]

Nella libreria scikit-learn, i modelli di machine learning vengono definiti **estimatori**. Per implementare l'algoritmo dei k-vicini più prossimi, si utilizza l'estimatore **KNeighborsClassifier**, che viene importato dal modulo `sklearn.neighbors`. La creazione del modello avviene istanziando un oggetto di questa classe, come mostrato nell'istruzione `knn = KNeighborsClassifier()`, che configura il classificatore pronto per essere addestrato sui dati.

![[Pasted image 20260428160207.png]]

L'addestramento dell'estimatore avviene tramite il metodo **fit**, che carica nel modello sia il set di campioni (`X_train`) sia le relative etichette target (`y_train`). Durante questa fase, il classificatore memorizza i dati per poter calcolare le vicinanze durante la predizione; in particolare, il parametro **n_neighbors** all'interno di `KNeighborsClassifier` corrisponde al valore $k$ dell'algoritmo, definendo quanti vicini debbano essere considerati per la votazione. L'esecuzione di `knn.fit` restituisce l'oggetto classificatore stesso, confermando l'avvenuto caricamento dei dati e l'applicazione delle impostazioni di default (o personalizzate) necessarie per il funzionamento del modello.

![[Pasted image 20260515192512.png]]

(AGGIUNGERE COMMENTO DI GEMINI/CHATGPT SE NECESSARIO)

![[Pasted image 20260515192554.png]]

(AGGIUNGERE COMMENTO DI GEMINI/CHATGPT SE NECESSARIO)

![[Pasted image 20260515192641.png]]
![[Pasted image 20260515192717.png]]
Nota come la previsione è sempre corretta tranne che nel penultimo elemento (prevede 5 ma la realtà è 3)

(AGGIUNGERE COMMENTO DI GEMINI/CHATGPT SE NECESSARIO)

![[Pasted image 20260515192819.png]]

(AGGIUNGERE COMMENTO DI GEMINI/CHATGPT SE NECESSARIO)



