MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 08/05/2026
FONTE: capitolo 15.5 e successivi

--- 

![[Pasted image 20260519192407.png]]
In questa lezione studieremo la regressione lineare multipla tramite il caso di studio della **California Housing Dataset**, che è integrato direttamente all'interno della libreria scikit-learn. Questo archivio rappresenta un benchmark del mondo immobiliare reale molto più ampio e complesso rispetto ai modelli accademici standard, raccogliendo complessivamente 20.640 campioni statistici indipendenti strutturati ciascuno su otto distinte feature numeriche. L'obiettivo metodologico consiste nell'eseguire un'analisi di **regressione lineare multipla** sfruttando simultaneamente l'intero set delle otto variabili indipendenti, una strategia che consente di formulare previsioni sul valore economico degli immobili decisamente più sofisticate, robuste e accurate rispetto a una stima parziale condotta su una singola caratteristica o su un sottoinsieme limitato di feature.
Dal punto di vista software, l'estimatore `LinearRegression` fornito dal framework scikit-learn è nativamente progettato per scalare su più dimensioni, eseguendo in modo del tutto automatico l'algoritmo di regressione multipla non appena rileva che la matrice dei dati d'ingresso contiene più di un vettore di caratteristiche.

![[Pasted image 20260519192746.png]]

La base dati è stata interamente derivata dal censimento nazionale degli Stati Uniti condotto nel 1990, strutturando le informazioni in modo da dedicare una singola riga della matrice a ciascun gruppo di blocchi censuari (_census block group_). 

![[Pasted image 20260519192943.png]]
Il California Housing dataset è composto da 20.640 campioni totali, ciascuno dei quali fa riferimento a uno specifico gruppo di blocchi censuari e raccoglie otto caratteristiche numeriche fondamentali. Tra le feature relative al reddito e alla struttura degli immobili troviamo il reddito mediano, l'età mediana degli edifici, il numero medio di stanze e il numero medio di camere da letto. Le restanti variabili riguardano invece la demografia e la localizzazione geografica della zona d'interesse, includendo la popolazione totale del blocco censuario, l'occupazione media delle abitazioni e, infine, le coordinate spaziali espresse tramite latitudine e longitudine del blocco immobiliare.

![[Pasted image 20260519193300.png]]
![[Pasted image 20260519194215.png]]
Per prima cosa carichiamo il dataset. Il valore target da predire nel dataset è il valore mediano delle case, espresso in centinaia di migliaia di dollari, il che significa che un coefficiente pari a 3.55 equivale a 355.000 dollari, con un limite massimo prefissato per questa feature impostato a 5 (ovvero 500.000 dollari). Dal punto di vista logico, è ragionevole attendersi che un incremento nel numero di stanze, di camere da letto o del livello di reddito si traduca in un valore immobiliare più alto; l'algoritmo combina quindi tutte e otto le caratteristiche numeriche per generare stime multivariate, garantendo una precisione predittiva nettamente superiore rispetto a una regressione lineare semplice basata su una sola variabile.
Operativamente, il caricamento dei dati all'interno del Jupyter Notebook avviene importando la funzione `fetch_california_housing` dal modulo `sklearn.datasets` e assegnando l'oggetto di tipo Bunch risultante alla variabile `california`, non prima di aver abilitato la visualizzazione grafica dei grafici direttamente nel documento tramite il comando magico `%matplotlib inline`.

La descrizione ufficiale delle caratteristiche del California Housing dataset mostra un totale di 20.640 istanze complessive, prive di qualsiasi valore nullo o mancante (Missing Attribute Values: None). La struttura dei dati comprende 8 attributi predittivi di tipo numerico più la relativa variabile target, dettagliando formalmente i nomi tecnici delle colonne: `MedInc` per il reddito mediano del blocco censuario, `HouseAge` per l'età mediana degli edifici, `AveRooms` e `AveBedrms` rispettivamente per il numero medio di stanze e di camere da letto per nucleo familiare. Le restanti metriche descrivono la demografia e la geografia locale attraverso la variabile `Population` (popolazione totale del blocco), `AveOccup` (numero medio di componenti per nucleo familiare) e le coordinate spaziali espresse dalle colonne `Latitude` e `Longitude`.

![[Pasted image 20260519194739.png]]
Esploriamo il Dataset con Pandas.
L'esplorazione del dataset viene avviata importando la libreria Pandas e configurando le opzioni di visualizzazione per migliorare la leggibilità dei dati numerici nel notebook. Attraverso l'istruzione `pd.set_option('display.precision', 4)`, la precisione di stampa dei numeri a virgola mobile viene fissata a quattro cifre decimali, mentre i comandi commentati suggeriscono impostazioni opzionali per forzare la visualizzazione in terminale di un massimo di 9 colonne senza mandare a capo il testo. Operativamente, la struttura dati tabulare viene generata invocando il costruttore `pd.DataFrame`, a cui vengono passati la matrice dei campioni `california.data` e l'elenco delle etichette dei rispettivi attributi `california.feature_names`. Subito dopo, per completare l'insieme informativo, viene inserita una nuova colonna denominata `MedHouseValue` associandola a un oggetto Pandas Series contenente i valori target reali dell'archivio, ovvero `california.target`, consentendo così di manipolare l'intero dataset, comprensivo di feature e riscontri, all'interno di un unico frame logico integrato.

![[Pasted image 20260519194944.png]]
Osservo le prime 5 righe.

![[Pasted image 20260519195401 1.png]]
Con describe possiamo vedere vari valori relativi al dataset. In questo caso: numero di campioni, mediana, media, minimo, 25%, 50%, 75% (del massimo) e massimo totale.

![[Pasted image 20260519195551.png]]
La visualizzazione delle caratteristiche si rivela estremamente utile per analizzare graficamente le relazioni esistenti tra il valore target e ciascun attributo predittivo, mostrando in che modo il prezzo mediano delle case vari in funzione delle singole variabili.
Al fine di rendere i grafici più chiari, leggibili ed evitare un sovraffollamento visivo dei punti, viene utilizzato il metodo `sample` del DataFrame per estrarre in modo casuale un sottoinsieme pari al 10% dei 20.640 campioni totali.
Sui dati così ridotti vengono poi generati diversi diagrammi di dispersione (_scatter plots_), all'interno dei quali ogni grafico mappa una specifica caratteristica numerica sull'asse delle ascisse (asse x) e il valore mediano delle abitazioni sull'asse delle ordinate (asse y), evidenziando visivamente tendenze, correlazioni o la presenza di eventuali valori fuori norma.

![[Pasted image 20260519195837.png]]
![[Pasted image 20260519200249.png|410]]
![[Pasted image 20260519200218 1.png|412]]
![[Pasted image 20260519200417.png|412]]
Il codice di sopra serve a produrre questi diagrammi (sul libro ce ne sono altri ma il concetto è sempre lo stesso).

![[Pasted image 20260519200602.png]]
Dai grafici si possono notare differenze tra le varie aree geografiche.

---

![[Pasted image 20260519200651.png]]
Dobbiamo dividere il dataset in training e testing usando la funzione $train\_test\_split$.
Questo passaggio spacchetta la matrice delle caratteristiche `california.data` e il vettore dei target `california.target` in quattro array distinti: `X_train` e `y_train` per il training, `X_test` e `y_test` per la successiva fase di validazione, garantendo la riproducibilità del campionamento casuale tramite il parametro `random_state=11`. Di default, l'algoritmo alloca il 75% dei record complessivi all'addestramento e il restante 25% al testing.

![[Pasted image 20260519200912 1.png]]
L'addestramento del modello si basa sull'utilizzo dell'estimatore `LinearRegression`, il quale tenta nativamente di elaborare l'insieme completo delle caratteristiche presenti nella matrice dei dati d'ingresso. Un vincolo matematico fondamentale di questo algoritmo è l'incapacità di gestire direttamente variabili non numeriche: la presenza di feature categoriche genera infatti un errore di runtime, imponendo che tali attributi vengano preventivamente convertiti in valori numerici (tramite tecniche di pre-processing come l'one-hot encoding) oppure del tutto esclusi dal dataset. Fortunatamente, i dataset inclusi nativamente all'interno del framework scikit-learn sono già distribuiti nel formato corretto e standardizzato per il training, eliminando la necessità di interventi di pulizia preliminare.
Operativamente, lo script importa l'estimatore dal modulo `sklearn.linear_model`, ne istanzia l'oggetto assegnandolo alla variabile `linear_regression` e avvia il calcolo dei coefficienti ottimali invocando il metodo `fit` sulle matrici di addestramento `X_train` e `y_train`, completando l'ottimizzazione dei pesi come confermato dall'output testuale del notebook.

![[Pasted image 20260519201340.png]]
A seguito dell'addestramento, l'estimatore memorizza i parametri calcolati all'interno di due specifici attributi: un array di coefficienti angolari indipendenti per ciascuna caratteristica, accessibile tramite `coef_`, e un singolo valore di intercetta sull'asse delle ordinate, registrato in `intercept_`.
L'analisi matematica di questi pesi permette di interpretare la relazione lineare tra le variabili e il target: i coefficienti con segno positivo indicano che il valore mediano delle case tende a crescere all'aumentare del valore della feature correlata, mentre i coefficienti negativi evidenziano una proporzionalità inversa. Esaminando l'output del ciclo di stampa, gli attributi `HouseAge`, `AveOccup` e `Population` presentano valori estremamente prossimi allo zero (nel caso della popolazione l'ordine di grandezza è regolato da un esponente $e-06$), dimostrando un impatto apparentemente nullo o del tutto trascurabile sulla determinazione del prezzo immobiliare finale, a fronte di un'intercetta globale calcolata a $-36.88$.

![[Pasted image 20260519201531.png]]
I coefficienti numerici e l'intercetta calcolati durante l'addestramento vengono combinati all'interno di un'equazione algebrica lineare per formulare le predizioni sui nuovi dati: $y = m_1x_1 + m_2x_2 + \dots + m_nx_n + b$. All'interno di questa formulazione matematica multivariata, le variabili identificano componenti ben distinte dell'algoritmo predittivo: i termini $m_1, m_2, \dots, m_n$ rappresentano i coefficienti angolari associati a ciascuna caratteristica, $b$ definisce il valore costante dell'intercetta globale, mentre i fattori $x_1, x_2, \dots, x_n$ indicano i valori numerici delle feature d'ingresso, noti anche come variabili indipendenti del modello.
Sulla base di questo prodotto scalare tra vettori, il termine finale $y$ esprime il valore numerico predetto dall'estimatore (la variabile dipendente), che nel caso specifico della regressione multipla sul California Housing dataset corrisponde alla stima economica finale del valore mediano dell'immobile preso in esame.

![[Pasted image 20260519201754.png]]
Per testare il modello si usa il metodo $predict$ dell'estimatore. Predicted ed expected sono due vettori.

![[Pasted image 20260519201901.png]]
Visualizziamo la differenza tra valori Expected e Predicted.
Per analizzare l'accuratezza del modello attraverso un confronto diretto tra i riscontri reali e le stime calcolate, viene generato un nuovo DataFrame Pandas strutturato per raccogliere entrambe le metriche.
Operativamente, lo script inizializza un oggetto tabella vuoto tramite l'istruzione `df = pd.DataFrame()` e inserisce in successione due colonne principali per mappare i vettori dei dati: la colonna `Expected` e `Predicted`. Entrambe le sorgenti vengono convertite in oggetti di tipo Pandas Series per garantire il perfetto allineamento degli indici di riga, strutturando così una tabella comparativa preliminare che funge da base logica per la successiva costruzione dei grafici di regressione e la visualizzazione degli scostamenti residui.
Dopodichè:

![[Pasted image 20260519202252.png]]
![[Pasted image 20260519202221.png|926]]
La visualizzazione grafica del confronto tra i prezzi reali e quelli predetti viene implementata configurando un diagramma di dispersione tramite le librerie Matplotlib e Seaborn. Il codice inizializza una figura quadrata di dimensioni 9x9 pollici con `plt.figure(figsize=(9, 9))` e invoca `sns.scatterplot` per mappare la colonna `Expected` sull'asse x e la colonna `Predicted` sull'asse y, applicando una sfumatura cromatica basata sui valori predetti tramite la tavolozza `cool`.
Per garantire una rappresentazione perfettamente simmetrica e proporzionata del grafico, le variabili `start` ed `end` calcolano dinamicamente il valore minimo assoluto e il valore massimo assoluto registrati nell'intero set di dati, applicando poi tali estremi ai limiti di visualizzazione di entrambi gli assi coordinati mediante i metodi `set_xlim` e `set_ylim`. Infine, l'istruzione `plt.plot([start, end], [start, end], 'k--')` traccia una linea di riferimento diagonale tratteggiata di colore nero (indicata dall'argomento `'k--'`) che interseca il piano a 45 gradi: questa retta rappresenta il comportamento di un modello ideale a predizione perfetta, permettendo di valutare visivamente l'accuratezza dell'estimatore reale in base a quanto i punti si distribuiscano o si discostino da essa.

I punti posizionati al di sopra della diagonale indicano i casi di sovra-stima (dove $Predicted > Expected$), mentre i punti situati al di sotto segnalano le sotto-stime ($Predicted < Expected$).

---

![[Pasted image 20260519203134.png]]
La valutazione delle prestazioni del modello si basa sull'analisi delle metriche di regressione, tra le quali spicca il coefficiente di determinazione, noto formalmente come punteggio $R^2$ (R-squared score). Questa metrica esprime un valore numerico standardizzato generalmente compreso nell'intervallo tra $0.0$ e $1.0$: un punteggio pari a $1.0$ indica che l'estimatore è in grado di prevedere in modo perfetto il valore della variabile dipendente a partire dalle variabili indipendenti, mentre un punteggio pari a $0.0$ segnala che il modello non possiede alcuna accuratezza predittiva, comportandosi sostanzialmente come una linea orizzontale basata sulla semplice media dei dati.
Il calcolo matematico viene effettuato confrontando direttamente gli array dei risultati attesi (expected) e di quelli stimati (predicted). Operativamente, lo script importa il modulo `metrics` dalla libreria `sklearn` e invoca la funzione `metrics.r2_score(expected, predicted)`, la quale restituisce nel notebook un valore finale di circa $0.60$, evidenziando che il modello di regressione lineare multipla riesce a spiegare e catturare il 60% della varianza complessiva presente nei prezzi del California Housing dataset.

![[Pasted image 20260519203410.png]]
Per scegliere il modello migliore si può eseguire un confronto. Usiamo, oltre a linear regression, anche ElasticNet, Lasso e Ridge.

![[Pasted image 20260519203522 1.png]]
![[Pasted image 20260519203556.png]]
La selezione del modello migliore viene effettuata eseguendo una procedura di validazione incrociata chiamata _k-fold cross-validation_, utile per testare la stabilità degli algoritmi su diverse porzioni del dataset. Il codice importa `KFold` e `cross_val_score` dal modulo `sklearn.model_selection` e implementa un ciclo che itera su un dizionario di stimatori.
All'interno del ciclo, la funzione `cross_val_score` riceve l'argomento `scoring='r2'`, che impone al sistema di calcolare e restituire il punteggio $R^2$ per ciascuno dei 10 fold temporanei estratti dalle matrici globali. L'output del blocco di calcolo mostra la media aritmetica dei punteggi ottenuti da quattro diversi algoritmi di regressione lineare e regolarizzata.
Con questo abbiamo concluso la parte sulla Regressione Lineare Multipla.

---

Cambiamo argomento, parliamo adesso di Machine Learning Non Supervisionato, cominciando dal problema della riduzione del dataset.

![[Pasted image 20260519204127.png]]
L'introduzione all'apprendimento non supervisionato si focalizza sulla riduzione della dimensionalità (_Dimensionality Reduction_) come strumento essenziale per esplorare e comprendere la struttura intrinseca dei dati.
A differenza dei modelli supervisionati, gli algoritmi non supervisionati lavorano su campioni privi di etichette (_unlabeled_), aiutando a identificare pattern nascosti, raggruppamenti naturali e relazioni complesse tra le variabili. Mentre la visualizzazione geometrica risulta immediata quando si gestiscono dataset a due dimensioni (tramite grafici cartesiani 2D) o a tre dimensioni (sfruttando rendering spaziali 3D), la rappresentazione visiva diventa impraticabile quando le feature aumentano, imponendo il ricorso a tecniche di compressione dello spazio lineare per proiettare le informazioni in coordinate graficamente interpretabili.

![[Pasted image 20260519204355 1.png]]
La compressione dello spazio lineare tramite tecniche di riduzione della dimensionalità risulta indispensabile per proiettare dataset multidimensionali complessi all'interno di uno spazio a due o tre dimensioni, rendendoli graficamente interpretabili. La visualizzazione di questi output a bassa dimensionalità permette di individuare pattern geometrici nascosti, i quali guidano lo scienziato dei dati nella selezione degli algoritmi di machine learning più idonei per le fasi successive del workflow.
Ad esempio, la formazione spontanea di raggruppamenti geometrici distinti, noti come _clusters_, suggerisce la presenza di classi di informazioni ben separate all'interno dei dati non etichettati, rendendo opportuno l'eventuale impiego futuro di un modello di classificazione supervisionata.

![[Pasted image 20260519204617.png]]
L'addestramento degli estimatori su dataset massicci caratterizzati da un numero elevato di dimensioni può richiedere tempi di calcolo proibitivi, a causa della complessità matematica intrinseca nel tentare di concettualizzare spazi iperdimensionali. Attraverso la compressione dello spazio delle feature, questa tecnica permette di eliminare o combinare tra loro gli attributi fortemente correlati, riducendo la ridondanza informativa e migliorando sensibilmente le performance e la velocità di training delle reti; tuttavia, la contrazione geometrica delle variabili comporta un compromesso strutturale, poiché la rimozione di parte dell'informazione originaria potrebbe ridurre l'accuratezza predittiva complessiva del modello finale.

![[Pasted image 20260519204645.png]]
Per capire quello che stiamo dicendo, analizziamo il Dataset Digits. Il caricamento del Digits dataset viene finalizzato ignorando momentaneamente le etichette reali delle classi (_labels_) per simulare un approccio non supervisionato, applicando la riduzione della dimensionalità al fine di proiettare e visualizzare la struttura geometrica dei dati all'interno di uno spazio bidimensionale. Per abilitare il rendering dei grafici direttamente nel Jupyter Notebook, viene lanciato in apertura il comando magico `%matplotlib inline`. L'importazione e l'inizializzazione dell'archivio avvengono richiamando la funzione `load_digits` dal modulo `sklearn.datasets` e assegnando l'oggetto risultante alla variabile `digits`, strutturando così la base dati iperdimensionale che verrà successivamente compressa per mappare le relazioni visive tra le diverse cifre scritte a mano.

![[Pasted image 20260519205032.png]]
L'istanza dell'algoritmo di riduzione della dimensionalità viene creata importando la classe `TSNE` dal modulo `sklearn.manifold`. L'algoritmo t-SNE (t-distributed Stochastic Neighbor Embedding) rappresenta una tecnica avanzata e non lineare particolarmente efficace per proiettare dataset ad alta dimensionalità in spazi bidimensionali o tridimensionali, preservando al contempo le relazioni di prossimità locale tra i campioni (non ci interessano i dettagli di questo algoritmo).
Operativamente, il modello viene configurato specificando due iperparametri fondamentali: `n_components=2`, che impone la compressione dei dati in uno spazio a due dimensioni, e `random_state=11`, che fissa il seme del generatore di numeri casuali per assicurare la perfetta riproducibilità statistica dei risultati a ogni successiva esecuzione del codice.

![[Pasted image 20260519205406.png]]
La trasformazione dei dati e la compressione vera e propria dello spazio lineare vengono eseguite invocando il metodo `fit_transform` sull'istanza dell'oggetto t-SNE precedentemente configurata. Questo metodo accetta come argomento la matrice iperdimensionale `digits.data`, che contiene i vettori originari a 64 caratteristiche per ciascuna immagine, e restituisce in output una nuova matrice bidimensionale dotata di sole due colonne, salvata all'interno della variabile `reduced_data`. Il completamento di questa operazione geometrica trova riscontro immediato nell'analisi della forma del tensore risultante tramite l'attributo `reduced_data.shape`, il quale conferma che il dataset mantiene inalterato il numero complessivo di 1.797 campioni originari, ma ne mostra la contrazione strutturale a due sole coordinate per riga, rendendo le informazioni pronte per essere mappate su un comune diagramma cartesiano.

![[Pasted image 20260519210857.png]]
La visualizzazione dei dati compressi viene realizzata generando un diagramma di dispersione monocromatico tramite la funzione `scatter` di Matplotlib, preferita in questa fase a Seaborn per poter memorizzare la collezione di punti all'interno della variabile `dots` in vista di successive elaborazioni grafiche. Il codice definisce una figura quadrata di dimensioni 5x5 pollici e mappa le coordinate bidimensionali estratte proiettando la prima colonna della matrice dei dati ridotti (`reduced_data[:, 0]`) sull'asse delle ascisse e la seconda colonna (`reduced_data[:, 1]`) sull'asse delle ordinate, colorando tutti i marker interamente di nero. L'output grafico risultante mostra la disposizione spaziale dei 1.797 campioni del Digits dataset, evidenziando in modo chiaro come l'algoritmo non supervisionato t-SNE sia riuscito a raggruppare i dati ad alta dimensionalità in diversi ammassi geometrici compatti e nettamente separati tra loro, i quali suggeriscono la presenza di relazioni intrinseche e classi strutturali ben distinte all'interno del dataset originale.
Per capire meglio quei puntini isolati a quale cluster appartengano possiamo colorare il diagramma, come segue:

![[Pasted image 20260519211225.png]]
![[Pasted image 20260519211316.png]]
La validazione dei raggruppamenti geometrici emersi dall'approccio non supervisionato viene effettuata introducendo le etichette reali del dataset per verificare se ogni ammasso isolato corrisponda effettivamente a una specifica cifra numerica scritta a mano.
Per mappare questa informazione visiva, la funzione `plt.scatter` viene configurata applicando il parametro `c=digits.target`, il quale associa il colore di ciascun punto al rispettivo valore numerico reale, mentre l'argomento `cmap=plt.cm.get_cmap('nipy_spectral_r', 10)` definisce una mappa cromatica discretizzata su 10 tonalità nettamente distinte per rappresentare l'intervallo delle cifre da 0 a 9. Il codice inizializza una figura rettangolare di dimensioni 6x5 pollici e, tramite l'istruzione finale `colorbar = plt.colorbar(dots)`, inserisce sul lato del grafico una legenda a barre colorate che funge da chiave di lettura per i vettori d'intensità, permettendo di confermare visivamente se la separazione spaziale elaborata in modo cieco dall'algoritmo t-SNE rifletta con accuratezza le reali classi di appartenenza dei campioni.