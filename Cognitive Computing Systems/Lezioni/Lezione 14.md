MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 12/05/2026
FONTE: 

---

![[Pasted image 20260520130649.png]]
L'analisi del clustering tramite l'algoritmo k-Means introduce uno dei modelli più semplici, diffusi e fondamentali dell'apprendimento non supervisionato. Il compito principale di questa tecnica consiste nell'esaminare un insieme di campioni non etichettati (_unlabeled_) e raggrupparli autonomamente in clusters. All'interno del workflow, la variabile $k$ rappresenta l'iperparametro cruciale impostato dall'utente, il quale definisce a priori il numero esatto di raggruppamenti che l'algoritmo deve imporre sulla distribuzione spaziale degli elementi.
Per organizzare e consolidare la struttura dei diversi gruppi, il modello esegue ciclicamente calcoli di distanza geometrica nello spazio multidimensionale in modo analogo a quanto avviene nella classificazione k-NN (_k-Nearest Neighbors_), associando progressivamente ciascun punto della matrice al centroide matematico più vicino per minimizzare la varianza interna a ogni singolo cluster.

![[Pasted image 20260520130722 1.png]]
La seconda parte dello studio sul **k-Means** ne descrive il funzionamento algoritmico iterativo, basato sulla convergenza geometrica attorno ai punti centrali dei gruppi.

Ecco come si articola l'ottimizzazione del modello:

- **Inizializzazione casuale:** All'avvio, ogni cluster è raggruppato attorno ad un punto detto centroide. L'algoritmo seleziona in modo casuale $k$ campioni dal dataset per fungere da **centroidi** iniziali (i punti centrali temporanei dei cluster).
    
- **Assegnazione dei punti:** I rimanenti campioni della matrice vengono associati al cluster il cui centroide si trova alla distanza geometrica minore.
    
- **Aggiornamento iterativo:** I centroidi vengono ricalcolati stabilendo la media matematica delle coordinate di tutti i punti assegnati a quel gruppo. Una volta spostato il centroide, i campioni vengono riassegnati ai nuovi centri più vicini.

Questo processo ciclico si ripete fino a quando le distanze tra i centroidi e i rispettivi punti non vengono **minimizzate** (condizione di convergenza o stabilità). Al termine dell'esecuzione, l'algoritmo restituisce due strutture dati principali:

1. Un **array unidimensionale di etichette (labels)**, che indica l'indice del cluster a cui è stato assegnato ciascun campione.
    
2. Un **array bidimensionale dei centroidi**, contenente le coordinate spaziali finali del centro di ogni raggruppamento.

![[Pasted image 20260520131239.png]]
L'analisi si sposta sull'**Iris Dataset**, uno dei banchi di prova storici e più celebri nell'ambito del machine learning, ampiamente utilizzato per testare sia algoritmi di classificazione supervisionata che di clustering non supervisionato. Il dataset include nativamente le etichette delle classi; tuttavia, per scopi didattici legati al clustering, queste etichette vengono inizialmente ignorate per permettere all'algoritmo di raggruppare i dati in modo cieco, venendo poi riutilizzate alla fine come metro di paragone per valutare l'accuratezza e la bontà della segmentazione operata dal k-Means.

Definito tipicamente come un _"toy dataset"_ per via delle sue dimensioni ridotte e della sua pulizia strutturale, l'archivio raccoglie un totale di soli 150 campioni perfettamente bilanciati, suddividendosi in 50 record per ciascuna delle tre diverse specie di fiore analizzate: _Iris setosa_, _Iris versicolor_ e _Iris virginica_. Ogni singolo fiore è descritto nello spazio lineare da quattro caratteristiche morfologiche quantitative, espresse interamente in centimetri: la lunghezza del sepalo, la larghezza del sepalo, la lunghezza del petalo e la larghezza del petalo, dove i sepali costituiscono le parti verdi più esterne e grandi che racchiudono e proteggono i petali interni prima della fioritura.

![[Pasted image 20260520131553 1.png]]
Per prima cosa carichiamo il dataset Iris.

![[Pasted image 20260520131645.png]]
Ecc

![[Pasted image 20260520131958.png]]
Possiamo controllare alcune caratteristiche così.

![[Pasted image 20260520132112.png]]
Esploriamo il dataset.
L'esplorazione e l'analisi statistica descrittiva dell'Iris Dataset vengono avviate strutturando i dati all'interno di un DataFrame Pandas, strumento essenziale per manipolare e visualizzare i record in formato tabulare.

Il processo mostrato nelle slide si articola nelle seguenti fasi:

- **Inizializzazione del DataFrame:** L'istruzione `iris_df = pd.DataFrame(iris.data, columns=iris.feature_names)` converte la matrice numerica originaria `iris.data` in una tabella, assegnando alle intestazioni delle colonne i nomi ufficiali delle quattro caratteristiche morfologiche dei fiori contenuti in `iris.feature_names` (lunghezze e larghezze di sepali e petali).
    
- **Configurazione dell'ambiente (commentata):** Le righe relative a `pd.set_option` servono a regolare i parametri di visualizzazione dei contenuti, impostazioni che risultano necessarie principalmente quando si lavora in modalità interattiva all'interno di terminali IPython.
    
- **Predisposizione della colonna Target:** Nella parte inferiore viene introdotta la logica per arricchire la tabella inserendo il nome testuale della specie di appartenenza per ogni campione. Questa operazione viene eseguita tramite una _list comprehension_, la quale scorre gli indici numerici dell'array `target` (0, 1, 2) e li mappa sui rispettivi nomi scientifici (_setosa_, _versicolor_, _virginica_) memorizzati all'interno dell'array `target_names`.
![[Pasted image 20260520132206.png]]
La slide illustra il completamento della configurazione del DataFrame e l'ispezione iniziale dei dati contenuti al suo interno.

Il processo si divide in due passaggi chiave:

- **Aggiunta della colonna Species:** Viene eseguita l'assegnazione della lista di stringhe creata precedentemente (contenente i nomi scientifici _setosa_, _versicolor_ e _virginica_) a una nuova colonna del DataFrame chiamata `species`. L'istruzione `iris_df['species'] = [iris.target_names[i] for i in iris.target]` memorizza questa informazione semantica accanto alle caratteristiche fisiche dei fiori.
    
- **Visualizzazione dei primi record:** Viene invocato il metodo `iris_df.head()` per stampare a schermo le prime 5 righe della tabella. L'output mostra la struttura finale del dataset: le prime quattro colonne accolgono i valori numerici delle feature morfologiche (es. `sepal length (cm)`, `sepal width (cm)`, ecc.), mentre l'ultima colonna mostra la classificazione reale associata (in questo caso, tutti i primi 5 campioni appartengono alla specie _setosa_).
- 
![[Pasted image 20260520132800 1.png]]
Il blocco di codice e il relativo output evidenziano i seguenti passaggi:

- **Formattazione dei decimali:** Con l'istruzione `pd.set_option('precision', 2)`, viene impostata globalmente la precisione di visualizzazione di Pandas a due cifre decimali, rendendo la tabella dei dati più pulita e leggibile.
    
- **Generazione delle statistiche:** Invocando il metodo `iris_df.describe()`, Pandas calcola automaticamente un insieme di metriche statistiche chiave per ciascuna delle quattro caratteristiche geometriche:
    
    - **`count`:** Conferma la presenza di 150 campioni totali per ogni colonna, escludendo la presenza di valori nulli o mancanti.
        
    - **`mean` e `std`:** Rappresentano rispettivamente la media aritmetica e la deviazione standard (indice di dispersione). Si nota ad esempio come la lunghezza dei petali abbia una variabilità molto più marcata ($\text{std} = 1.77$) rispetto alla larghezza dei sepali ($\text{std} = 0.44$).
        
    - **I cinque numeri di sintesi (`min`, `25%`, `50%`, `75%`, `max`):** Mostrano i valori minimi e massimi insieme ai tre quartili (dove il $50\%$ corrisponde alla mediana). Questa scomposizione permette di intuire la forma della distribuzione e l'estensione dei dati per ciascun attributo morfologico del fiore.

![[Pasted image 20260520133026.png]]
La slide mostra l'applicazione del metodo `describe()` su una variabile categorica (la colonna `species`) anziché su colonne numeriche, evidenziando una differenza fondamentale nel modo in cui Pandas riassume i dati non quantitativi.

L'analisi dello snippet e delle note mette in luce i seguenti punti:

- **Statistiche per dati categorici:** Quando viene invocato su una colonna di testo (tipo `object`), il metodo `iris_df['species'].describe()` restituisce metriche specifiche per stringhe o categorie:
    
    - **`count`:** Indica il numero totale di valori non nulli presenti (150).
        
    - **`unique`:** Conferma la presenza di esattamente 3 categorie distinte (corrispondenti alle tre specie di Iris).
        
    - **`top`:** Mostra la stringa che appare con maggiore frequenza nel dataset (in questo caso `virginica`).
        
    - **`freq`:** Indica quante volte compare l'elemento più frequente (50). Poiché il dataset è perfettamente bilanciato con 50 campioni per ciascuna delle tre specie, l'algoritmo ha selezionato arbitrariamente una delle tre come "top" evidenziando la frequenza di 50.
        
- **Nota concettuale sul Machine Learning:** Il testo sottolinea come, grazie al dataset pre-etichettato, si conosca in anticipo il numero esatto delle classi (pari a 3). Questa condizione rappresenta un'eccezione didattica: nell'apprendimento non supervisionato reale (_unsupervised machine learning_), i dati sono privi di etichette e il numero ideale di raggruppamenti non è noto a priori, ma deve essere stimato o dedotto attraverso l'analisi dei dati stessi.

![[Pasted image 20260520133308.png]]
![[Pasted image 20260520133432 1.png|609]]
Visualizziamo i dati per capire meglio cosa stiamo analizzando. Per farlo, utilizziamo la funzione `pairplot` della libreria Seaborn per visualizzare le relazioni e le correlazioni incrociate tra tutte le variabili del dataset.
Il concetto e i passaggi del codice si strutturano nel seguente modo:

- **Il problema della dimensionalità:** Avendo a disposizione 4 caratteristiche morfologiche distinte, risulta impossibile proiettarle simultaneamente all'interno di un unico grafico standard. Per superare questo limite geometrico, l'approccio ideale consiste nel confrontare le feature a coppie (_pairs_).
    
- **La griglia di grafici:** La funzione `sns.pairplot` genera automaticamente una matrice simmetrica di grafici cartesiani (una griglia). Nelle intersezioni tra variabili diverse vengono disegnati dei diagrammi di dispersione (_scatter plots_), mentre lungo la diagonale principale, dove una feature incontrerebbe se stessa, la libreria traccia tipicamente istogrammi o curve di densità (KDE) per mostrare la distribuzione univariata del singolo attributo.
    
- **Configurazione e parametri del codice:**
    
    - Viene importata la libreria con `import seaborn as sns` e impostato lo sfondo dei grafici a griglia bianca tramite `sns.set_style('whitegrid')`.
        
    - L'istruzione `grid = sns.pairplot(...)` mappa i dati passando tre argomenti chiave: `data=iris_df` definisce la sorgente tabulare; `vars=iris_df.columns[0:4]` limita l'analisi numerica esclusivamente alle prime quattro colonne (escludendo il testo della specie); infine, il parametro `hue='species'` impone al sistema di colorare i punti in base alla specie reale di appartenenza, permettendo di valutare visivamente il livello di separazione e sovrapposizione tra i diversi gruppi biologici.

![[Pasted image 20260520134245.png]]
Togliendo l'argument hue, ottengo il diagramma con un solo colore perchè pairplot non sa distinguere da solo tra le varie specie.

![[Pasted image 20260520134433.png]]
La slide mostra l'effettiva inizializzazione dell'algoritmo **k-Means** per raggruppare i campioni dell'Iris Dataset.
Il blocco descrive la logica di creazione dell'estimatore e la gestione dei suoi parametri:
 -  **Analisi del Codice Python:**
    - Viene importata la classe dedicata tramite l'istruzione `from sklearn.cluster import KMeans`.
    - L'oggetto viene istanziato all'interno della variabile `kmeans` configurando due argomenti: `n_clusters=3`, per forzare la suddivisione del dataset in tre gruppi distinti, e `random_state=11`, che blocca la componente stocastica legata al posizionamento iniziale dei centroidi, garantendo la totale riproducibilità dei risultati a ogni computazione.

![[Pasted image 20260520134800.png]]
La slide illustra la fase di addestramento (_fitting_) del modello k-Means e descrive gli attributi principali che l'oggetto espone una volta completata la convergenza geometrica.

**Esecuzione dell'algoritmo:** L'istruzione `kmeans.fit(iris.data)` avvia l'ottimizzazione iterativa passando come unico argomento la matrice numerica delle feature. Trattandosi di un approccio non supervisionato, non viene fornito alcun vettore dei target reali. L'output mostra i parametri di default di scikit-learn, tra cui l'inizializzazione ottimizzata `init='k-means++'` e il limite massimo di iterazioni `max_iter=300`.
