MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 05/05/2026
FONTE: capitolo 15.03 e 15.04
RIASSUNTO: [[Lezione 12 - Riassunto]]

---

![[Pasted image 20260505145816.png]]
Quando si costruisce un classificatore (o algoritmo ML) bisogna valutarne l'accuratezza. Eseguire più classificatori alla volta (come spesso faremo), comporta che dovremo comparare i risultati per poter scegliere quello che ci soddisfa di più.
Questa sezione approfondisce la valutazione delle prestazioni di un modello di classificazione basato sull'algoritmo **k-Nearest Neighbors (k-NN)**, applicato al dataset "Digits". In particolare, viene introdotto il metodo `score`, che permette di misurare l'accuratezza del modello confrontando le previsioni effettuate con i dati di test reali; nell'esempio mostrato, il classificatore raggiunge un'accuratezza del **97,78%** utilizzando i parametri predefiniti (come k=5). Infine, viene suggerito che è possibile migliorare ulteriormente questo risultato attraverso il **tuning degli iperparametri**, ovvero cercando il valore ottimale di "k" per massimizzare la precisione del sistema.

![[Pasted image 20260505150318.png]]
La **matrice di confusione** è uno strumento fondamentale per valutare in dettaglio le performance di un classificatore, poiché mostra non solo l'accuratezza generale, ma anche quali classi vengono confuse tra loro. Attraverso la funzione `confusion_matrix` della libreria Scikit-learn, viene generata una tabella che mette a confronto i valori reali con quelli previsti dal modello: i numeri sulla diagonale principale rappresentano i "successi" (previsioni corrette), mentre i valori fuori diagonale indicano gli errori specifici commessi.
Nell'esempio mostrato, si nota come il modello sia estremamente preciso per molte cifre, ma commetta sporadici errori di classificazione, identificando ad esempio erroneamente alcuni elementi come appartenenti a una classe diversa da quella corretta.
In particolare vediamo che il numero 1 è stato previsto correttamente 45 volte, stessa cosa per il numero 2, mentre il numero 3 ben 54 volte. Il numero 4 invece è stato previsto correttamente 42 volte, mentre è stato confuso una volta con il numero 6 e una volta con l'8.
In pratica ogni riga rappresenta il numero effettivo, e sulle varie colonne sono presenti i valori previsti dall'algoritmo.

![[Pasted image 20260505151156.png|550]]

![[Pasted image 20260505151543.png|638]]
![[Pasted image 20260505151841.png|479]]
Per rendere l'analisi dei risultati più intuitiva, la matrice di confusione può essere visualizzata graficamente tramite una **heat map** (mappa di calore), che utilizza i colori per rappresentare l'intensità dei valori numerici. Il processo prevede la conversione della matrice in un **DataFrame di Pandas** e l'utilizzo di librerie come **Seaborn** per generare il grafico: in questa rappresentazione, la diagonale principale e gli eventuali errori di classificazione risaltano immediatamente grazie al contrasto cromatico, permettendo di identificare a colpo d'occhio i punti di forza e le criticità del modello.

Nel codice, la matrice numerica viene prima convertita in un `DataFrame` di Pandas, definendo esplicitamente gli indici e le colonne (da 0 a 9) per facilitarne la lettura. Successivamente, viene creata una figura con dimensioni specifiche ($7 \times 6$ pollici) tramite Matplotlib e utilizzata la funzione `sns.heatmap` per generare la mappa di calore: il parametro `annot=True` assicura che i valori numerici siano visibili all'interno delle celle, mentre `cmap` definisce la scala cromatica (in questo caso "nipy_spectral_r"). Questa visualizzazione permette di distinguere immediatamente i successi sulla diagonale principale dagli errori, che appaiono come celle colorate al di fuori di essa.

---

![[Pasted image 20260505152030.png]]
La **K-Fold Cross-Validation** è una tecnica avanzata di validazione che permette di utilizzare l'intero dataset sia per l'addestramento che per il test, garantendo una valutazione più robusta e affidabile della capacità predittiva del modello. Il processo consiste nel suddividere i dati in $k$ parti uguali (chiamate "folds"): il modello viene addestrato ripetutamente utilizzando $k-1$ parti e testato sulla parte rimanente, ruotando il set di test a ogni iterazione. Ad esempio, con $k=10$, si effettuano dieci sessioni di test differenti, ognuna su un decimo diverso del dataset, permettendo di mitigare il rischio che una suddivisione troppo fortunata o sfortunata dei dati possa falsare i risultati dell'accuratezza.

![[Pasted image 20260505152408.png]]
In Scikit-learn, la classe **KFold** e la funzione `cross_val_score` sono gli strumenti principali per implementare la validazione incrociata. Configurando l'oggetto `KFold` con il parametro `n_splits=10`, indichiamo al sistema di dividere i dati in dieci parti; è fondamentale impostare `shuffle=True` per rimescolare i campioni in modo casuale prima della suddivisione. Questa operazione di "shuffling" è critica quando i dati originali sono ordinati o raggruppati per classe (come nel dataset Iris), poiché evita che un fold contenga campioni di una sola categoria, garantendo che ogni sessione di addestramento e test riceva una rappresentazione equilibrata e statisticamente significativa dell'intero dataset.

![[Pasted image 20260505152658.png]]
La funzione `cross_val_score` è lo strumento pratico che esegue l'intero processo di **K-Fold Cross-Validation** in un'unica operazione. Per utilizzarla, è necessario passare quattro parametri fondamentali: l'**estimator** (ovvero il modello da validare, come `knn`), i dati delle feature (`X`) da utilizzare per fare training e testing, le etichette target (`y`) per i vari campioni e l'oggetto di validazione (`cv=kfold`) che definisce la strategia di suddivisione.
Invece di restituire un singolo valore di accuratezza, questa funzione genera un array di punteggi, uno per ogni "fold" effettuato, permettendo così di analizzare la costanza delle prestazioni del modello su diverse porzioni del dataset e di ottenere una valutazione statistica molto più affidabile rispetto a un singolo test statico.

![[Pasted image 20260505153035.png]]
Dopo l'esecuzione della cross-validation, l'array `scores` contiene i risultati di accuratezza ottenuti per ciascuno dei 10 fold, che in questo caso variano da un minimo del **97,78%** fino a un massimo perfetto del **100%**. Calcolando la media di questi valori tramite il metodo `mean()`, si ottiene un'accuratezza media del **98,72%**, un risultato superiore rispetto al precedente test singolo basato sulla semplice suddivisione 75/25 dei dati. Questo incremento dimostra come la cross-validation offra una misura più precisa e meno distorta delle reali capacità del modello, poiché riduce l'impatto di eventuali distribuzioni sbilanciate dei dati nel set di test.

![[Pasted image 20260505155012.png]]
Nonostante il **KNeighborsClassifier** abbia mostrato ottimi risultati nel riconoscimento delle cifre, questa slide introduce la necessità di testare altri stimatori per verificare se esistano soluzioni ancora più precise.
A tal fine, viene proposto un confronto diretto tra tre diversi algoritmi: il già citato **k-Nearest Neighbors**, il **Support Vector Classification (SVC)** e il **Gaussian Naive Bayes (GaussianNB)**.
Noi abbiamo studiato solo il primo, ma non è necessario conoscere tutti i dettagli di un modello per poterlo utilizzare.

![[Pasted image 20260505155311.png]]
Per implementare il confronto tra diversi modelli, il codice importa le classi **SVC** (Support Vector Classification) e **GaussianNB** (Gaussian Naive Bayes) dalla libreria Scikit-learn. Viene quindi creato un dizionario denominato `estimators` che associa un nome descrittivo a ciascuna istanza del modello: il già configurato `knn`, un'istanza di `SVC` (utilizzando il parametro `gamma='scale'` per ottimizzare il calcolo ed evitare avvisi del sistema) e un'istanza di `GaussianNB`. Questa struttura a dizionario è particolarmente utile per automatizzare i test successivi, permettendo di iterare facilmente attraverso i diversi algoritmi e confrontare i loro risultati in modo sistematico.

![[Pasted image 20260505155441.png]]
Nell'ultima fase del confronto, il codice utilizza un ciclo `for` per eseguire la **10-fold cross-validation** su ciascuno degli stimatori definiti in precedenza. Per ogni modello, vengono calcolate e stampate l'**accuratezza media** e la **deviazione standard**, permettendo di valutare non solo la precisione, ma anche la stabilità dei risultati. Dall'output emerge che il **KNeighborsClassifier** e l'**SVC** ottengono prestazioni identiche (98,72%), superando nettamente il **GaussianNB** (84,48%); questo risultato suggerisce che, per scegliere il vincitore tra i primi due, sarebbe opportuno procedere con l'**hyperparameter tuning**, ovvero la calibrazione fine dei parametri interni di ciascun algoritmo per estrarre il massimo potenziale possibile.

![[Pasted image 20260505155730.png]]
L'**Hyperparameter Tuning** è un processo cruciale nel machine learning reale per ottimizzare le prestazioni di un modello scegliendo i valori dei parametri che non vengono appresi durante l'addestramento, ma impostati a priori. Nel caso dell'algoritmo k-NN, l'obiettivo principale è determinare il valore ottimale di **k** (il numero di vicini) testando diverse configurazioni e confrontandone i risultati. Per semplificare questo compito, la libreria Scikit-learn offre funzionalità per l'**ottimizzazione automatizzata**, che permettono di esplorare sistematicamente varie combinazioni di iperparametri per identificare quella che garantisce le previsioni più accurate.

![[Pasted image 20260505155946.png]]
Il codice mostra un'implementazione pratica del tuning dell'iperparametro **k** nel modello k-NN attraverso un ciclo che testa i valori dispari da 1 a 19. L'uso di valori dispari è una scelta strategica per evitare situazioni di parità durante la votazione dei vicini. Per ogni valore di $k$, viene eseguita una **10-fold cross-validation**, calcolando accuratezza media e deviazione standard: dai risultati emerge che i valori più bassi, come $k=1$ o $k=3$, offrono in questo caso la precisione più elevata (**98,83%**), mentre l'accuratezza tende a diminuire leggermente all'aumentare del numero di vicini considerati.

![[Pasted image 20260505160140.png]]
L'ottimizzazione del modello comporta dei compromessi in termini di **costi computazionali**, un fattore critico specialmente in contesti di Big Data o Deep Learning. Nello specifico dell'algoritmo k-NN, il tempo di calcolo aumenta rapidamente al crescere del valore di **k**, poiché il sistema deve confrontare ogni campione con un numero maggiore di vicini per produrre una previsione. Per monitorare questo aspetto, Scikit-learn mette a disposizione la funzione `cross_validate`, che a differenza di `cross_val_score` permette non solo di valutare l'accuratezza, ma anche di **misurare i tempi di esecuzione**, aiutando lo sviluppatore a trovare il giusto equilibrio tra la precisione del modello e le risorse temporali necessarie per l'addestramento e il test.

![[Pasted image 20260505160516.png]]
Caso di studio: temperature registrate a Gennaio a NYC dal 1895 fino al 2018.

![[Pasted image 20260505160930.png]]

![[Pasted image 20260505161009 1.png]]

![[Pasted image 20260505161045.png]]

(queste tre immagini vanno viste insieme)

![[Pasted image 20260505161214.png]]
In questa nuova sezione viene introdotto un caso di studio sulle **serie temporali** utilizzando la **regressione lineare semplice**. Il primo passo consiste nel caricamento dei dati storici delle temperature medie di New York (dal 1895 al 2018) all'interno di un **DataFrame di Pandas**, dove vengono effettuate operazioni di pulizia come la rinomina delle colonne e la formattazione delle date. Per procedere con l'analisi predittiva, si utilizza lo stimatore `LinearRegression` di Scikit-learn: poiché questo strumento richiede che i dati di input siano **bidimensionali**, è necessario trasformare la colonna delle date (che inizialmente è una Series monodimensionale) in una struttura a $n$ righe e una colonna, permettendo così al modello di elaborare correttamente la variabile indipendente per prevedere l'andamento delle temperature nel tempo.

![[Pasted image 20260505161256.png]]
Per preparare i dati alla regressione, viene utilizzata la funzione `reshape(-1, 1)` sull'array delle date: il valore `-1` indica a NumPy di calcolare automaticamente il numero di righe necessario (in questo caso 124) mantenendo una singola colonna, soddisfacendo così il requisito di bidimensionalità di Scikit-learn. Successivamente, i dati vengono suddivisi nei set di addestramento e di test tramite la funzione `train_test_split`. Questa operazione separa le feature (le date trasformate) dai target (le temperature), garantendo che il modello possa essere addestrato su una porzione di dati e poi verificato su una parte indipendente per valutarne l'efficacia predittiva.

![[Pasted image 20260505161357.png]]
In questa slide viene verificata la corretta esecuzione della suddivisione dei dati, confermando che il dataset è stato ripartito secondo la proporzione standard del **75% per l'addestramento** e del **25% per il test**. Attraverso l'attributo `.shape`, si osserva che il set di addestramento (`X_train`) contiene 93 campioni, mentre il set di test (`X_test`) ne contiene 31, mantenendo per entrambi la struttura bidimensionale a una singola colonna richiesta dal modello. Questa verifica numerica assicura che il processo di splitting sia avvenuto correttamente e che il modello disponga di una base di dati sufficientemente bilanciata per le fasi successive di apprendimento e validazione.

![[Pasted image 20260505161508.png]]
Il processo di addestramento del modello inizia con l'inizializzazione dell'oggetto `LinearRegression` e l'uso del metodo `fit`, che riceve come input le feature di addestramento ($X$) e i relativi target ($y$). Durante questa fase, l'algoritmo cerca di individuare la **retta di regressione** che meglio approssima i dati, regolando iterativamente i parametri di **pendenza** (slope) e **intercetta** (intercept). L'obiettivo matematico è minimizzare la **somma dei quadrati delle distanze** tra i punti reali e la retta stessa (metodo dei minimi quadrati), garantendo così che la linea ottenuta rappresenti l'andamento più accurato possibile per descrivere la relazione tra le date e le temperature medie.

![[Pasted image 20260505161551.png]]
Una volta completato l'addestramento, il modello di regressione lineare esprime la relazione tra i dati attraverso l'equazione della retta $y = mx + b$. La pendenza (slope), che rappresenta il coefficiente $m$, è memorizzata nell'attributo `coeff_` e indica quanto varia la temperatura per ogni unità di tempo; l'intercetta (intercept), corrispondente al valore $b$, è invece accessibile tramite `intercept_` e indica il punto in cui la retta incrocia l'asse delle ordinate. Questi due parametri sono fondamentali per interpretare il modello e verranno utilizzati per calcolare le previsioni future, applicando matematicamente la formula ai nuovi valori di input.

![[Pasted image 20260505161811.png]]
In questa fase, i valori di **pendenza** (`coef_`) e **intercetta** (`intercept_`) calcolati dal modello vengono utilizzati per effettuare previsioni concrete attraverso una funzione **lambda** che implementa l'equazione della retta $y = mx + b$. Questa logica permette non solo di stimare temperature future (come per l'anno 2019), ma anche di effettuare una stima retroattiva per anni passati non presenti nel dataset originario (come il 1890). Applicando matematicamente il modello, trasformiamo l'analisi statistica in uno strumento predittivo capace di fornire una temperatura stimata per qualsiasi valore temporale fornito come input.

> [!NOTE]
>FOCUS:
>
>Una funzione **lambda** in Python è semplicemente un modo rapido e sintetico per definire una funzione su una sola riga, senza dover usare la classica struttura `def nome_funzione()`.
Nel contesto della slide, la lambda viene usata per "impacchettare" la formula matematica della retta ($y = mx + b$) in una variabile chiamata `predict`. Ecco come leggerla:
>
> **`lambda x:`** definisce l'input della funzione (in questo caso l'anno che vuoi calcolare).
  >  
>- **`linear_regression.coef_ * x`**: moltiplica la pendenza ($m$) per l'anno fornito ($x$).
>    
>- **`+ linear_regression.intercept_`**: aggiunge l'intercetta ($b$).
>  
>In pratica, invece di scrivere ogni volta l'intera operazione matematica, definisci questa "scorciatoia" così da poter scrivere semplicemente `predict(2019)` e ottenere automaticamente il risultato del calcolo basato sui parametri che il modello ha imparato durante l'addestramento.

---

![[Pasted image 20260505162034.png]]

![[Pasted image 20260505162144.png]]
In questa fase finale della visualizzazione, viene utilizzata la libreria **Matplotlib** per tracciare fisicamente la retta di regressione sopra lo scatterplot precedentemente creato. Il comando `plt.plot(x, y)` unisce i due punti calcolati (il valore predetto per la data minima e quello per la data massima), generando una linea continua che rappresenta l'andamento tendenziale delle temperature nel corso degli anni. Questa rappresentazione grafica è fondamentale perché permette di verificare visivamente l'accuratezza del modello: la pendenza della retta mostra chiaramente se le temperature medie a New York stiano aumentando o diminuendo nel periodo considerato, rendendo i dati statistici immediatamente comprensibili e interpretabili.

---

![[Pasted image 20260505162358.png]]
L'**underfitting** e l'**overfitting** sono ostacoli comuni che impediscono ai modelli di generalizzare correttamente su dati mai visti prima. L'underfitting si verifica quando un modello è troppo semplice per catturare la struttura dei dati (ad esempio, usare una retta per dati che seguono una curva), risultando impreciso sia sull'addestramento che sul test. Al contrario, l'overfitting accade quando il modello è eccessivamente complesso e finisce per "memorizzare" il rumore dei dati di addestramento invece di imparare il pattern sottostante; in questo caso, il modello fornirà previsioni perfette sui dati noti, ma fallirà drasticamente davanti a nuove informazioni. L'obiettivo fondamentale è trovare il giusto equilibrio per garantire che il sistema sia capace di estrapolare previsioni accurate in contesti reali.