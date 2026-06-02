MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 12/05/2026
FONTE: 

---

![[Cognitive Computing Systems/Immagini/Img - Lezione 14/Pasted image 20260520130649.png]]
L'analisi del clustering tramite l'algoritmo k-Means introduce uno dei modelli più semplici, diffusi e fondamentali dell'apprendimento non supervisionato. Il compito principale di questa tecnica consiste nell'esaminare un insieme di campioni non etichettati (_unlabeled_) e raggrupparli autonomamente in clusters. All'interno del workflow, la variabile $k$ rappresenta l'iperparametro cruciale impostato dall'utente, il quale definisce a priori il numero esatto di raggruppamenti che l'algoritmo deve imporre sulla distribuzione spaziale degli elementi.
Per organizzare e consolidare la struttura dei diversi gruppi, il modello esegue ciclicamente calcoli di distanza geometrica nello spazio multidimensionale in modo analogo a quanto avviene nella classificazione k-NN (_k-Nearest Neighbors_), associando progressivamente ciascun punto della matrice al centroide matematico più vicino per minimizzare la varianza interna a ogni singolo cluster.

![[Pasted image 20260520130722 1.png]]
La seconda parte dello studio sul **k-Means** ne descrive il funzionamento algoritmico iterativo, basato sulla convergenza geometrica attorno ai punti centrali dei gruppi. I gruppi prendono il nome di **cluster** mentre i punti centrali si chiamano **centroidi**.

Ecco come si articola l'ottimizzazione del modello:

- **Inizializzazione casuale:** Inizialmente non esistono nè clusters nè centroidi. L'algoritmo seleziona in modo casuale $k$ elementi dal dataset per fungere da **centroidi** iniziali (i punti centrali **temporanei** dei cluster).
    
- **Assegnazione dei punti:** I rimanenti campioni vengono associati al cluster il cui centroide si trova alla distanza geometrica minore.
    
- **Aggiornamento iterativo:** I centroidi di ogni cluster vengono ricalcolati considerando la media matematica delle coordinate di tutti i punti assegnati al singolo cluster. Una volta spostato il centroide, i campioni vengono riassegnati ai nuovi centri più vicini.

Questo processo ciclico si ripete fino a quando le distanze tra i centroidi e i rispettivi punti vengono **minimizzate** (condizione di convergenza o stabilità). Al termine dell'esecuzione, l'algoritmo restituisce due strutture dati principali:

1. Un **array unidimensionale di etichette (labels)**, che indica l'indice del cluster a cui è stato assegnato ciascun campione.
    
2. Un **array bidimensionale dei centroidi**, contenente le coordinate spaziali finali del centro di ogni raggruppamento.

![[Pasted image 20260520131239.png]]
L'analisi si sposta sull'**Iris Dataset**, uno dei banchi di prova storici e più celebri nell'ambito del machine learning, ampiamente utilizzato per testare sia algoritmi di classificazione supervisionata che di clustering non supervisionato. Il dataset include nativamente le etichette delle classi; tuttavia, per scopi didattici legati al clustering, queste etichette vengono inizialmente ignorate per permettere all'algoritmo di raggruppare i dati in modo cieco, venendo poi riutilizzate alla fine come metro di paragone per valutare l'accuratezza e la bontà della segmentazione operata dal k-Means.

Definito tipicamente come un _"toy dataset"_ per via delle sue dimensioni ridotte e della sua pulizia strutturale, l'archivio raccoglie un totale di soli 150 campioni perfettamente bilanciati, suddividendosi in 50 record per ciascuna delle tre diverse specie di fiore analizzate: _Iris setosa_, _Iris versicolor_ e _Iris virginica_. Ogni singolo fiore è descritto nello spazio lineare da quattro caratteristiche morfologiche quantitative, espresse interamente in centimetri: la lunghezza del sepalo, la larghezza del sepalo, la lunghezza del petalo e la larghezza del petalo, dove i sepali costituiscono le parti verdi più esterne e grandi che racchiudono e proteggono i petali interni prima della fioritura.

![[Pasted image 20260520131553 1.png]]
Per prima cosa carichiamo il dataset Iris.

![[Pasted image 20260520131645.png]]
Eccone le caratteristiche principali.

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
        
    - **`mean` e `std`:** Rappresentano rispettivamente la media aritmetica e la deviazione standard (indice di dispersione).
        
    - **I cinque numeri di sintesi (`min`, `25%`, `50%`, `75%`, `max`):** Mostrano i valori minimi e massimi insieme ai tre quartili (dove il $50\%$ corrisponde alla mediana). 

![[Pasted image 20260520133026.png]]
La slide mostra l'applicazione del metodo `describe()` su una variabile categorica (la colonna `species`) anziché su colonne numeriche, evidenziando una differenza fondamentale nel modo in cui Pandas riassume i dati non quantitativi.

L'analisi dello snippet e delle note mette in luce i seguenti punti:

- **Statistiche per dati categorici:** Quando viene invocato su una colonna di testo (tipo `object`), il metodo `iris_df['species'].describe()` restituisce metriche specifiche per stringhe o categorie:
    
    - **`count`:** Indica il numero totale di valori non nulli presenti.
        
    - **`unique`:** Conferma la presenza di esattamente 3 categorie distinte (corrispondenti alle tre specie di Iris).
        
    - **`top`:** Mostra la stringa che appare con maggiore frequenza nel dataset.
        
    - **`freq`:** Indica quante volte compare l'elemento più frequente (50). Poiché il dataset è perfettamente bilanciato con 50 campioni per ciascuna delle tre specie, l'algoritmo ha selezionato arbitrariamente una delle tre come "top" evidenziando la frequenza di 50.
        
- **Nota concettuale sul Machine Learning:** Il testo sottolinea come, grazie al dataset pre-etichettato, si conosca in anticipo il numero esatto delle classi. Questa condizione rappresenta un'eccezione didattica: nell'apprendimento non supervisionato reale, i dati sono privi di etichette e il numero ideale di raggruppamenti non è noto a priori, ma deve essere stimato o dedotto attraverso l'analisi dei dati stessi.

---

![[Pasted image 20260520133308.png]]
![[Pasted image 20260520133432 1.png|609]]
Visualizziamo i dati per capire meglio cosa stiamo analizzando. Per farlo, utilizziamo la funzione `pairplot` della libreria Seaborn per visualizzare le relazioni e le correlazioni incrociate tra tutte le variabili del dataset.
Il concetto e i passaggi del codice si strutturano nel seguente modo:

- **Il problema della dimensionalità:** Avendo a disposizione 4 caratteristiche morfologiche distinte, risulta impossibile proiettarle simultaneamente all'interno di un unico grafico standard. Per superare questo limite geometrico, l'approccio ideale consiste nel confrontare le feature a coppie (_pairs_).
    
- **La griglia di grafici:** La funzione `sns.pairplot` genera automaticamente una matrice simmetrica di grafici cartesiani. Nelle intersezioni tra variabili diverse vengono disegnati dei diagrammi di dispersione (_scatter plots_), mentre lungo la diagonale principale, dove una feature incontrerebbe se stessa, la libreria traccia tipicamente istogrammi o curve di densità (KDE) per mostrare la distribuzione univariata del singolo attributo.
    
- **Configurazione e parametri del codice:**
    
    - Viene importata la libreria con `import seaborn as sns` e impostato lo sfondo dei grafici a griglia bianca tramite `sns.set_style('whitegrid')`.
        
    - L'istruzione `grid = sns.pairplot(...)` mappa i dati passando tre argomenti chiave: `data=iris_df` definisce la sorgente tabulare; `vars=iris_df.columns[0:4]` limita l'analisi numerica esclusivamente alle prime quattro colonne (escludendo il testo della specie); infine, il parametro `hue='species'` impone al sistema di colorare i punti in base alla specie reale di appartenenza, permettendo di valutare visivamente il livello di separazione e sovrapposizione tra i diversi gruppi biologici.

![[Pasted image 20260520134245.png]]
Togliendo l'argument hue, ottengo il diagramma con un solo colore perchè pairplot non sa distinguere da solo tra le varie specie.

---

![[Pasted image 20260520134433.png]]
La slide mostra l'effettiva inizializzazione dell'algoritmo **k-Means** per raggruppare i campioni dell'Iris Dataset.
Il blocco descrive la logica di creazione dell'estimatore e la gestione dei suoi parametri:
 -  **Analisi del Codice Python:**
    - Viene importata la classe dedicata tramite l'istruzione `from sklearn.cluster import KMeans`.
    - L'oggetto viene istanziato all'interno della variabile `kmeans` configurando due argomenti: `n_clusters=3`, per forzare la suddivisione del dataset in tre gruppi distinti, e `random_state=11`, che blocca la componente stocastica legata al posizionamento iniziale dei centroidi, garantendo la totale riproducibilità dei risultati a ogni computazione.

![[Pasted image 20260520134800.png]]
La slide illustra la fase di addestramento (_fitting_) del modello k-Means e descrive gli attributi principali che l'oggetto espone una volta completata la convergenza geometrica.

**Esecuzione dell'algoritmo:** L'istruzione `kmeans.fit(iris.data)` avvia l'ottimizzazione iterativa passando come unico argomento la matrice numerica delle feature. Trattandosi di un approccio non supervisionato, non viene fornito alcun vettore dei target reali.
L'output mostra i parametri di default di scikit-learn, tra cui l'inizializzazione ottimizzata `init='k-means++'` e il limite massimo di iterazioni `max_iter=300`.

![[Pasted image 20260520172621.png]]
![[Pasted image 20260520172949.png|637]]
Per valutare la bontà del clustering effettuato in modo cieco da k-Means, si confrontano gli indici predetti nell'array `labels_` con i valori reali dell'array `target` del dataset Iris.

- **Distribuzione dei target originari:** I 150 campioni del dataset sono ordinati in modo sequenziale: i primi 50 appartengono alla specie _Iris setosa_ (valore `0`), i successivi 50 a _Iris versicolor_ (valore `1`) e gli ultimi 50 a _Iris virginica_ (valore `2`).
    
- **Condizione di clustering perfetto:** Se l'algoritmo avesse separato i dati in modo impeccabile, l'array `labels_` dovrebbe presentare tre blocchi omogenei da 50 elementi ciascuno, dove ogni blocco contiene un unico indice numerico distinto.

>[!NOTE]
> **Nota Fondamentale:** Gli indici numerici assegnati da k-Means (`0`, `1`, `2`) vengono scelti in modo del tutto arbitrario in base al posizionamento iniziale dei centroidi e **non hanno alcuna relazione semantica** con i valori codificati nell'array `target` originario. Ad esempio, il cluster che raccoglie le _Setose_ potrebbe essere etichettato dall'estimatore con l'indice `1` anziché `0`, senza che questo costituisca un errore di segmentazione.

In uno scenario reale di apprendimento non supervisionato (dove i dati sono nativamente privi di etichette), non è possibile effettuare questo tipo di confronto diretto; la validità delle classi predette deve essere determinata analizzando le caratteristiche strutturali dei gruppi o ricorrendo alla valutazione di un esperto di dominio.

---

![[Pasted image 20260520173100.png]]
Per ridurre la dimensione di questo dataset da 4 a 2 dimensioni, useremo un estimatore PCA, non ci interessano i dettagli del suo algoritmo.

![[Pasted image 20260520173336 1.png]]
Il codice mostra la separazione delle fasi di addestramento e trasformazione tramite l'algoritmo PCA per proiettare le 4 caratteristiche originarie dell'Iris Dataset in uno spazio bidimensionale.
A differenza del metodo combinato `fit_transform`, l'esecuzione viene qui suddivisa in due passaggi distinti per consentire il riutilizzo del modello memorizzato:

- **Fase di Fit (`pca.fit`):** Allena l'estimatore sulla matrice originaria `iris.data` per calcolare gli autovettori e le componenti principali dello spazio lineare. Questa operazione viene eseguita una sola volta.
    
- **Fase di Transform (`pca.transform`):** Restituisce una nuova matrice, salvata in `iris_pca`, che mantiene inalterato il numero di righe (150 campioni) ma riduce le colonne a 2 (`n_components=2`), come confermato dall'output di `iris_pca.shape` pari a `(150, 2)`.

Mantenere il metodo `transform` indipendente dal `fit` risulta fondamentale nel workflow di clustering per due motivi principali:

1. **Elaborazione di Nuovi Dati:** Consente di proiettare nello stesso spazio vettoriale 2D eventuali nuovi campioni raccolti in futuro, senza dover ricalcolare l'intera struttura delle componenti principali.
    
2. **Proiezione dei Centroidi:** Permette di richiamare una seconda volta il metodo `pca.transform` sui centroidi calcolati da k-Means (i quali nascono nello spazio originario a 4 dimensioni), riducendo anch'essi a 2 dimensioni per poterli mappare graficamente e sovrapporre correttamente alla nuvola di punti dei campioni.

![[Pasted image 20260520173835.png]]
Adesso vogliamo visualizzare i dati.

Il codice mostra come strutturare i dati compressi tramite PCA in un nuovo DataFrame Pandas e descrive la logica necessaria per includere i centroidi nello stesso spazio grafico bidimensionale.

I dati bidimensionali ottenuti dalla PCA vengono organizzati in una tabella per facilitare il plotting con Seaborn, riassociando le etichette delle specie per gestire i colori dei punti.

- **`columns=['Component1', 'Component2']`**: Assegna nomi generici alle due nuove coordinate prodotte dalla riduzione dimensionale (le due componenti principali).
    
- **Mappatura dei Colori**: Copiando la colonna `species` dal DataFrame originale a quello ridotto, si isola l'informazione semantica che verrà passata al parametro `hue` delle funzioni grafiche.
    
I centroidi calcolati da k-Means si trovano originariamente nello spazio a 4 dimensioni, poiché rappresentano il "campione medio" (il baricentro) delle caratteristiche morfologiche originarie dei fiori appartenenti a quel gruppo.

- **Vincolo Geometrico**: Non è possibile inserire direttamente i centroidi a 4 dimensioni su un grafico cartesiano basato sulle due componenti della PCA.
    
- **Soluzione**: Ciascun centroide contenuto nell'array `cluster_centers_` deve subire la medesima trasformazione lineare applicata ai campioni. Verrà quindi passato al metodo `pca.transform` dell'istanza PCA precedentemente addestrata, proiettando anche i centri dei cluster nello spazio bidimensionale (`Component1`, `Component2`), come segue:

![[Pasted image 20260520174147.png|624]]
Dove s ne indica la dimensione. Otteniamo così i seguenti grafici:

![[Pasted image 20260520174227.png|615]]

---

![[Pasted image 20260520174602.png]]
Come si sceglie il miglior algoritmo di clustering allora?
Ne eseguiamo più di uno e vediamo quale performa meglio. Useremo in particolare quattro estimatori, senza entrare nei dettagli dei loro algoritmi.

![[Pasted image 20260520174837.png]]
Useremo quindi KMeans, DBSCAN, MeanShift, SpectralClustering e AgglomerativeClustering.

![[Pasted image 20260520175051 1.png]]

```
import numpy as np

for name, estimator in estimators.items():
    estimator.fit(iris.data)
    print(f'\n{name}:')
    for i in range(0, 101, 50):
        labels, counts = np.unique(
            estimator.labels_[i:i+50], return_counts=True)
        print(f'{i}-{i+50}:')
        for label, count in zip(labels, counts):
            print(f'    label={label}, count={count}')
```

Il codice implementa un ciclo di valutazione quantitativa per confrontare le performance di diversi stimatori di clustering memorizzati nel dizionario `estimators`. Poiché il dataset Iris è ordinato sequenzialmente in tre blocchi contigui da 50 campioni (0-50 per _setosa_, 50-100 per _versicolor_, 100-150 per _virginica_), l'algoritmo sfrutta questa struttura per misurare la purezza dei cluster generati.

All'interno del ciclo principale, dopo aver addestrato lo stimatore corrente tramite `estimator.fit(iris.data)`, un ciclo nidificato esegue uno slicing statico sull'array `estimator.labels_` muovendosi a passi di 50 elementi tramite l'istruzione `range(0, 101, 50)`.
La funzione `np.unique` riceve la sotto-porzione di array estratta (`[i:i+50]`) e, grazie al parametro `return_counts=True`, restituisce due vettori: le etichette dei cluster individuate in quel blocco e la frequenza assoluta di ciascuna di esse. Infine, un ciclo finale stampa la distribuzione dei conteggi, permettendo di verificare visivamente se ciascuno scaglione reale da 50 elementi sia stato raggruppato sotto un'unica etichetta dominante o se presenti contaminazioni dovute a errori di assegnazione spaziale dell'algoritmo.
Output:

![[Pasted image 20260520175229.png|254]]![[Pasted image 20260520175320.png|194]]
Analisi dei risultati:
L'output mostra la distribuzione dei conteggi di clustering per cinque diversi algoritmi applicati all'Iris Dataset, permettendo di valutarne l'accuratezza analizzando la purezza dei tre blocchi sequenziali da 50 campioni (0-50: _setosa_, 50-100: _versicolor_, 100-150: _virginica_).

- **Isolamento di Iris setosa (0-50):** Tutti gli algoritmi riescono a isolare perfettamente il blocco relativo alla specie _setosa_ assegnando tutti i 50 campioni a un unico cluster omogeneo (con l'eccezione di DBSCAN che classifica 1 punto come rumore, assegnandogli l'etichetta `-1`). Questo conferma la netta separazione geometrica di questa classe nello spazio delle feature.
    
- **KMeans:** Dimostra un'ottima stabilità sul secondo blocco (48 punti assegnati al cluster `0` e solo 2 al cluster `2`), ma incontra difficoltà significative nella separazione del terzo blocco (14 campioni di _virginica_ vengono erroneamente raggruppati nel cluster `0` insieme alle _versicolor_).
    
- **MeanShift:** Fallisce nel compito di partizionamento a tre classi, collassando la quasi totalità dei campioni di _versicolor_ e _virginica_ all'interno dello stesso identico cluster (etichetta `0`). Questo comportamento è dovuto alla densità spaziale e alla parziale sovrapposizione delle due specie, che l'algoritmo interpreta come un'unica grande regione ad alta densità.
    
- **DBSCAN:** Identifica un elevato numero di campioni di rumore (`label=-1`) distribuiti tra le classi (1 nel primo blocco, 6 nel secondo, 10 nel terzo). Inoltre, non riesce a distinguere le _versicolor_ dalle _virginica_, accumulando la maggior parte di entrambi i blocchi (44 e 40 punti) sotto l'unica etichetta `1`.
    
- **SpectralClustering e AgglomerativeClustering:** Mostrano un comportamento speculare e quasi identico a KMeans. Entrambi isolano perfettamente il primo blocco e gestiscono in modo eccellente il secondo blocco (50 punti puri per lo Spectral, 49 per l'Agglomerative). Tuttavia, risentono della medesima sovrapposizione geometrica nel terzo blocco, mancando l'assegnazione di 15 campioni di _virginica_ che vengono confusi con il cluster delle _versicolor_.

![[Pasted image 20260520175942.png]]

---
Cambiamo argomento, iniziamo a parlare del Deep Learning partendo dal capitolo 16 del libro.

![[Pasted image 20260520180407 1.png]]
Il deep learning rappresenta un potente sottoinsieme del machine learning che ha permesso di raggiungere risultati straordinari e d'impatto in ambiti complessi come la computer vision (visione artificiale) e l'elaborazione del linguaggio naturale.

Le soluzioni di deep learning richiedono un'immensa quantità di risorse computazionali e la loro attuale fattibilità e diffusione sono legate alla convergenza di quattro fattori tecnologici abilitanti:

- **Big data:** La disponibilità di enormi volumi di dati, indispensabili per l'addestramento e combattere l'overfitting.
    
- **Potenza di calcolo dei processori:** L'evoluzione dell'hardware specifico in grado di sostenere i pesanti calcoli matematici richiesti dai modelli.
    
- **Velocità di rete internet più elevate:** Essenziali per il trasferimento rapido di dataset massicci distribuiti globalmente.
    
- **Avanzamenti nell'hardware e nel software per il calcolo parallelo:** Lo sviluppo di architetture dedicate e di framework software ottimizzati per parallelizzare l'esecuzione simultanea dei calcoli su matrici e tensori, riducendo drasticamente i tempi di training.

![[Pasted image 20260520180816.png]]
Keras offre un'interfaccia ad alto livello intuitiva e semplificata per Google TensorFlow, la libreria open-source più diffusa e utilizzata per lo sviluppo e l'addestramento di modelli di deep learning. Oltre a TensorFlow, Keras è stata originariamente progettata per interfacciarsi anche con altri backend computazionali, come CNTK di Microsoft e Theano.

![[Pasted image 20260520181107 1.png]]
L'addestramento dei modelli di deep learning richiede una straordinaria quantità di potenza di calcolo. Quando l'addestramento viene eseguito su dataset massivi (_big-data_), il processo di ottimizzazione dei parametri della rete può richiedere ore, giorni o persino settimane.

Per soddisfare queste importanti richieste computazionali, nell'ecosistema del deep learning si fa un uso intensivo di hardware dedicato ad alte prestazioni:

- **GPU (Graphics Processing Units):** Sviluppate principalmente da NVIDIA, sono chip ottimizzati per l'elaborazione parallela massiva, in grado di eseguire simultaneamente migliaia di operazioni matematiche elementari su matrici.
    
- **TPU (Tensor Processing Units):** ASIC (Application-Specific Integrated Circuits) progettati ad hoc da Google espressamente per accelerare i calcoli tensoriali tipici delle reti neurali, massimizzando il throughput e l'efficienza energetica rispetto alle GPU tradizionali.

In scenari didattici o con modelli di dimensioni ridotte, la complessità è tale da consentire il completamento del training in tempi brevi (da pochi minuti a meno di un'ora) anche utilizzando comuni e convenzionali CPU domestiche.

