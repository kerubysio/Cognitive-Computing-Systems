Consiglio: come supporto per lo sviluppo del progetto puoi usare AI. Consiglio di usare Deepseek come base e Claude.
In alternativa anche Gemini Pro è utile ma va guidato molto.
Un'idea potrebbe essere quella di usare Claude per farsi generare dei prompt dettagliati da mandare a Deepseek e Gemini.

---

# PANORAMICA GENERALE DEL PROGETTO

Il problema fondamentale è questo: un attacco _zero-day_ è per definizione **sconosciuto**. Non puoi addestrare un classificatore binario (benigno/maligno) su di esso perché non hai esempi di quella classe nuova. La strategia è quindi ribaltare il problema: **insegna al modello solo com'è fatto il traffico normale**, e poi classificare come anomalo tutto ciò che se ne discosta.

Questo paradigma si chiama **one-class learning** o **semi-supervised anomaly detection**, e il tuo autoencoder lo implementa così:

1. Viene addestrato **esclusivamente su traffico benigno** → impara a ricostruire fedelmente la "normalità"
2. Quando gli viene presentato un attacco (pattern diverso da tutto ciò che ha visto), **la ricostruzione sarà pessima** → errore di ricostruzione alto
3. L'errore di ricostruzione diventa il **punteggio di anomalia**
4. Una soglia (τ) decide cosa è anomalo e cosa no

La pipeline complessiva è:

```
CSV → Pulizia → Feature Engineering → Training (solo benigni)
       → Threshold → Classification → KPI → Dashboard
```

---

# CELLA 1 — Setup e Import delle Librerie

### Cosa fa questa cella

Carica in memoria tutti gli strumenti necessari al progetto. È una cella di puro setup: nessun dato viene ancora toccato.

### Le librerie in generale

- **pandas** — Fornisce il `DataFrame`, una struttura dati tabulare analoga a un foglio Excel. Ti permette di leggere CSV, filtrare righe per condizione, manipolare colonne, gestire valori mancanti. In questo progetto lo usi per tutta la fase di preprocessing.
	
- **numpy** — Libreria per il calcolo numerico su array multidimensionali.
	
- **matplotlib / seaborn** — Librerie di visualizzazione. 
	
- **scikit-learn (sklearn)** — La libreria Python standard per il machine learning "classico" (algoritmi non deep). Tu la usi per tre scopi distinti:
	- `MinMaxScaler` → normalizzazione dei dati
	- `train_test_split` → divisione training/validation
	- `roc_auc_score`, `confusion_matrix` → calcolo delle metriche di valutazione

- **PyTorch** — Framework per il deep learning sviluppato da Meta.

### Le scelte specifiche del tuo progetto

Oggi PyTorch è il framework dominante nella ricerca accademica (la maggior parte dei paper recenti rilascia codice in PyTorch). È più esplicito: ogni passo del training loop è scritto a mano, il che significa che capisci esattamente cosa succede. 

---

# CELLA 2 — Caricamento Dati e Pulizia Iniziale

### Cosa fa questa cella

Carica i due CSV del dataset UNSW-NB15 in DataFrame pandas e applica due operazioni di pulizia: gestione dei valori mancanti e rimozione delle righe duplicate.

### Il dataset UNSW-NB15

UNSW-NB15 è stato prodotto dall'Università del New South Wales (Australia). Le etichette sono:

- **`label = 0`**: traffico benigno (normale)
- **`label = 1`**: traffico malevolo (9 categorie: DoS, Reconnaissance, Backdoor, Exploits, Fuzzers, Generic, Shellcode, Worms, Analysis)

La colonna `attack_cat` specifica il tipo preciso di attacco.

Il fatto che i file siano già divisi in `training-set.csv` e `testing-set.csv` è importante: garantisce che la divisione sia predefinita e riproducibile, e che training e test provengano da periodi/scenari distinti.

### Gestione dei valori mancanti (NaN)

**In generale**: I dataset reali raramente sono completi. I valori mancanti (`NaN`, _Not a Number_) possono derivare da errori di raccolta, feature non applicabili a certi tipi di pacchetti, o lacune nei log di rete. Il problema è che i modelli matematici non sanno gestire NaN — se provi a fare la media di una lista che contiene NaN, il risultato è NaN. Devi decidere cosa farne:

- **Rimozione delle righe con NaN**: semplice, ma perdi informazione
- **Imputazione con la media**: sostituisci con il valore medio di quella feature su tutto il dataset
- **Imputazione con la mediana**: più robusta agli outlier della media
- **Imputazione con la moda**: usata per variabili categoriali
- **Imputazione con modelli**: usi un altro modello per predire il valore mancante (K-NN imputer, ecc.)

**Nel tuo progetto**: usi `fillna(mean(numeric_only=True))`, cioè sostituisci ogni NaN con la media della propria colonna, e solo per le colonne numeriche (`numeric_only=True` esclude le colonne categoriali, che verranno gestite dopo). Per feature di rete continue, la media rappresenta il comportamento "tipico" di quella feature: è una scelta solida e difendibile.

**Un punto tecnico da saper citare**: idealmente, la media usata per imputare il test set dovrebbe essere quella calcolata sul solo training set (non sul test set). Usare la media del test set per imputare il test set è un lieve data leakage. Nel tuo codice vengono usate medie separate per i due file (`df_train.mean()` per train, `df_test.mean()` per test). L'impatto è trascurabile perché UNSW-NB15 ha pochissimi NaN, ma è un dettaglio da conoscere.

### Rimozione dei duplicati

**In generale**: I duplicati sono righe identiche in ogni campo. In un dataset di rete possono apparire se lo stesso flusso viene registrato due volte (per artefatti del processo di cattura), o per errori di fusione dei file. Tenerli distorce il modello: alcune osservazioni vengono implicitamente "pesate di più" semplicemente perché compaiono più volte.

**Nel tuo progetto**: `drop_duplicates()` viene applicato **solo al training set**, non al test. Questo è corretto: vuoi un training set pulito e non ridondante, ma il test set deve riflettere la realtà così com'è, senza modifiche.

---

# CELLA 3 — Feature Engineering

Questa è la cella concettualmente più densa. Contiene 6 passaggi distinti che analizzo uno per uno.

### 3.1 — One-Hot Encoding (OHE)

**In generale**: Le variabili _categoriali_ sono variabili che assumono un numero finito di valori discreti e non ordinati. Nel traffico di rete, esempi tipici sono:

- il **protocollo** (`proto`): tcp, udp, icmp, ...
- il **servizio applicativo** (`service`): http, ftp, dns, ssh, ...
- lo **stato della connessione** (`state`): FIN (connessione terminata regolarmente), INT (incompleta), CON (stabilita), ...

Il problema è che le reti neurali operano su numeri reali. Se codifichi "tcp"=1, "udp"=2, "icmp"=3, stai introducendo un **ordine artificiale** che non esiste: il modello interpreterebbe "icmp" come "tre volte tcp", il che è semanticamente assurdo.

La soluzione è l'**One-Hot Encoding**: per una variabile con N categorie distinte, si creano N nuove colonne binarie (0 o 1), una per ogni categoria possibile. Per ogni osservazione, esattamente una colonna vale 1 e tutte le altre 0. Esempio con `proto`:

|proto_tcp|proto_udp|proto_icmp|
|---|---|---|
|1|0|0|
|0|1|0|
|0|0|1|

Questo non introduce nessun ordine artificiale: le tre colonne sono equidistanti nello spazio delle feature.

**Nel tuo progetto**: `pd.get_dummies(df, columns=['proto', 'service', 'state'])` applica OHE alle tre colonne categoriali identificate. Il numero di feature cresce notevolmente rispetto alle 47 originali (le categorie di `proto` e `service` sono molte), ma questo è il prezzo necessario per una rappresentazione numerica corretta.

---

### 3.2 — Allineamento delle colonne (align)

**In generale**: Dopo l'OHE, training set e test set potrebbero avere colonne diverse. Esempio: una categoria rara di protocollo ("proto_sctp") che compare nel test ma non nel training genererà una colonna in più nel test. Se poi provi a passare questo test set a un modello addestrato su una dimensione diversa, ottieni un errore di dimensione.

**Nel tuo progetto**: `df_train.align(df_test, join='outer', axis=1, fill_value=0)` risolve il problema elegantemente. Con `join='outer'` si prendono **tutte** le colonne dell'unione di entrambi i DataFrame; dove una colonna manca in uno dei due, viene inserito `0` (cioè "questa categoria non era presente in questo campione"). Il risultato è che training e test hanno esattamente le stesse colonne nello stesso ordine, garantendo coerenza dimensionale.

---

### 3.3 — Isolamento del traffico benigno per il training

Questo è il **cuore filosofico** del tuo sistema.

**In generale**: Il tipo di anomaly detection che hai implementato si chiama _one-class classification_. L'idea è: invece di addestrare un classificatore su entrambe le classi (benigno e malevolo), si addestra il modello **esclusivamente sulla classe normale**, e si classifica come anomalo tutto ciò che non rientra in essa. Questo è potente per due ragioni:

- Gli attacchi zero-day sono sconosciuti: non puoi avere esempi di ciò che ancora non è stato scoperto
- Il traffico normale è stabile e abbondante, mentre il traffico malevolo è raro e variabile

**Nel tuo progetto**: `df_train[df_train['label'] == 0]` seleziona solo le righe benigne dal training set. Da questo momento in poi, l'autoencoder non vedrà **mai** un esempio di attacco durante il training. Imparerà solo com'è fatto il traffico normale, e questo è sufficiente per rilevare qualsiasi anomalia — anche quelle mai viste prima.

---

### 3.4 — Estrazione delle label e drop delle colonne di leakage

**In generale**: Il _data leakage_ è uno dei problemi più insidiosi in machine learning. Si verifica quando il modello ha accesso, durante il training, a informazioni che **non potrebbe avere in uno scenario reale di produzione**. Il risultato è un modello che sembra eccellente sulle metriche ma che fallisce in deployment. Le colonne da eliminare sono:

- **`label`**: è esattamente ciò che vogliamo predire. Se la lasciassi nel vettore di input, l'autoencoder la userebbe banalmente per ricostruire sé stessa, bypassando l'apprendimento delle vere caratteristiche del traffico.
- **`attack_cat`**: specifica il tipo di attacco. Ancora peggio di `label` per il leakage: non solo dichiara che è un attacco, ma dice anche quale tipo. In produzione non sapremmo nulla di questo.
- **`id`**: un identificatore progressivo numerico. Non contiene alcuna informazione semantica sul traffico: è solo un numero di riga. Lasciarlo inside causerebbe il modello ad imparare correlazioni spurie con l'ordine di inserimento dei dati.

**Nel tuo progetto**: `drop(columns=['label', 'attack_cat', 'id'], errors='ignore')` rimuove correttamente queste tre colonne sia da training che da test, prima di passare i dati al modello. Il flag `errors='ignore'` previene errori se una colonna non fosse presente (robustezza del codice).

---

### 3.5 — Trasformazione Logaritmica (log1p)

**In generale**: Pensa alla dimensione dei pacchetti: la stragrande maggioranza trasmette poche centinaia di byte, ma alcuni flussi ne trasmettono milioni. Questa asimmetria crea problemi:

- I pochi valori estremi dominano il training, il modello diventa insensibile alle variazioni nei valori piccoli
- Lo scaler successivo viene distorto dagli outlier

La **trasformazione logaritmica** comprime la scala dei valori grandi e espande quella dei piccoli, avvicinando la distribuzione a una forma più simmetrica. Si usa `log1p(x) = log(1 + x)` invece di `log(x)` per gestire i valori pari a zero: `log(0)` è −∞ e causerebbe errori, mentre `log1p(0) = log(1) = 0` è ben definito.

**Nel tuo progetto**: `np.log1p()` viene applicata a tutto il dataset (training e test) **prima** dello scaling. L'ordine è fondamentale: prima si normalizza la distribuzione con il log, poi si scala nell'intervallo [0,1]. Così il MinMaxScaler lavora su dati già ragionevolmente simmetrici invece di essere distorto da outlier estremi.

---

### 3.6 — Feature Scaling con MinMaxScaler

**In generale**: Le feature numeriche di un dataset raramente vivono sulla stessa scala. "Durata della connessione" potrebbe variare tra 0 e 3600 (secondi), "numero di byte trasferiti" tra 0 e 10^7. Se non normalizzi, la rete neurale tenderà a dare più peso alle feature con valori numericamente grandi — non perché siano più informative, ma semplicemente perché i loro gradienti sono più grandi. Questo distorce l'apprendimento.

Esistono vari approcci di scaling:

- **StandardScaler** (_Z-score normalization_): sottrae la media e divide per la deviazione standard → `x' = (x − μ) / σ`. L'output ha media 0 e deviazione standard 1. Funziona bene quando i dati sono approssimativamente gaussiani.
    
- **MinMaxScaler**: normalizza i valori nell'intervallo [0, 1] secondo la formula:
    
    `x' = (x − x_min) / (x_max − x_min)`
    
    Preserva la forma della distribuzione originale, porta tutto in [0, 1].
    
- **RobustScaler**: usa mediana e IQR (interquartile range) invece di media e deviazione standard. È più robusto agli outlier di entrambi i precedenti.
    

**Nel tuo progetto**: hai scelto `MinMaxScaler`, e la scelta è ottimale per il tuo autoencoder per due ragioni tecniche precise:

1. **Compatibilità con l'output layer**: il tuo decoder termina con un layer lineare (nessuna funzione di attivazione). Questo significa che l'output della ricostruzione è un numero reale non vincolato. Avere i target (i dati originali) nell'intervallo [0,1] facilita enormemente la convergenza del modello, perché l'output non deve "inseguire" valori in range arbitrari.
    
2. **Complementarità con log1p**: dopo la trasformazione logaritmica le distribuzioni sono già meno skewed, quindi il MinMaxScaler non viene distorto dai pochi outlier rimasti.
    

**Punto cruciale da sapere**: il `scaler` viene fittato **solo sul training set** con `fit_transform(x_train_log)` — cioè calcola min e max dal training — e poi applicato al test set con il solo `transform(x_test_log)`, usando gli stessi min e max. Usare `fit_transform` anche sul test set sarebbe data leakage: il modello conoscerebbe implicitamente la distribuzione del test prima di vederlo.

---

# CELLA 4 — Definizione dell'Architettura Autoencoder

### Cosa fa questa cella

Definisce la struttura matematica del modello, la funzione di perdita e l'ottimizzatore. È la cella in cui l'autoencoder prende forma come classe Python e viene istanziato pronto per il training.

---

### 4.1 — Le reti neurali in generale

Una **rete neurale artificiale** è un modello matematico ispirato vagamente al funzionamento del cervello biologico. È composta da **strati** (_layers_) di **neuroni artificiali**. Ogni neurone riceve un vettore di input, lo moltiplica per un vettore di pesi (parametri appresi), somma un termine di bias, e passa il risultato attraverso una funzione di attivazione non lineare. L'output di uno strato diventa l'input dello strato successivo.

La potenza delle reti neurali sta in due proprietà:

- Sono **approssimatori universali**: con abbastanza neuroni e strati, possono approssimare qualsiasi funzione continua
- I loro parametri (i pesi) vengono appresi automaticamente dai dati tramite un algoritmo chiamato **backpropagation** (di cui parleremo nella cella 5)

---

### 4.2 — L'Autoencoder: cos'è e come funziona

Un **autoencoder** è un tipo speciale di rete neurale addestrata a **riprodurre il suo input come output**. Non è un classificatore: non predice un'etichetta. Il suo obiettivo è imparare una rappresentazione compressa dei dati.

L'architettura si divide in due parti simmetriche:

**L'Encoder** è la prima metà della rete. Riceve il vettore di input (tutte le feature del traffico di rete) e lo comprime progressivamente in una rappresentazione a dimensionalità ridotta. Questo vettore compresso si chiama **spazio latente** o **collo di bottiglia** (_bottleneck_). L'encoder impara a estrarre le caratteristiche essenziali dei dati, scartando il rumore.

**Il Decoder** è la seconda metà, speculare all'encoder. Riceve il vettore compresso dello spazio latente e cerca di ricostruire il vettore originale. L'obiettivo di training è che l'output del decoder sia il più simile possibile all'input originale.

Il flusso è:

```
Input (alta dimensione) → Encoder → Spazio Latente (bassa dimensione) → Decoder → Output ≈ Input
```

**Perché questo è utile per l'anomaly detection?** Durante il training, l'encoder impara a comprimere i pattern del traffico **benigno** in modo efficiente. Il decoder impara a ricostruirli. Quando presenti un **attacco** (pattern mai visto durante il training), l'encoder non sa come comprimerlo in modo significativo, e il decoder non sa come ricostruirlo: l'errore di ricostruzione sarà alto. Questo errore diventa il tuo segnale di anomalia.

---

### 4.3 — L'architettura specifica del tuo Autoencoder

Il tuo autoencoder ha questa struttura a imbuto:

**Encoder**: `input_dim → 128 → 64 → 32` **Decoder**: `32 → 64 → 128 → input_dim`

Ogni freccia rappresenta uno strato lineare completamente connesso (`nn.Linear`), che esegue la trasformazione `y = Wx + b` dove W è la matrice dei pesi e b il vettore di bias.

La dimensione di `input_dim` è determinata automaticamente dalla forma dei dati scalati (il numero di feature dopo OHE e preprocessing). Il **collo di bottiglia** è a dimensione **32**: il modello deve imparare a comprimere decine o centinaia di feature in soli 32 numeri, forzandolo a catturare solo i pattern strutturali più importanti.

La simmetria encoder/decoder non è obbligatoria, ma è una scelta progettuale comune che spesso funziona bene: garantisce che la capacità di compressione e quella di ricostruzione siano bilanciate.

---

### 4.4 — Le funzioni di attivazione (ReLU)

**In generale**: Senza funzioni di attivazione non lineari, una sequenza di strati lineari equivale a un unico strato lineare — non importa quanti strati metti, la rete non può imparare nulla di più di una trasformazione lineare. Le funzioni di attivazione introducono la **non-linearità** che permette alla rete di approssimare funzioni complesse.

Le principali funzioni di attivazione sono:

- **Sigmoid**: schiaccia i valori nell'intervallo (0,1). Soffre del problema del _vanishing gradient_ per valori molto grandi o piccoli.
- **Tanh**: simile alla sigmoid ma nell'intervallo (-1,1).
- **ReLU** (_Rectified Linear Unit_): `f(x) = max(0, x)`. Restituisce 0 per valori negativi, e il valore stesso per quelli positivi. Semplice, computazionalmente efficiente, mitiga il vanishing gradient.
- **Leaky ReLU**, **ELU**, **GELU**: varianti di ReLU che gestiscono meglio i neuroni con output sempre nullo (_dying ReLU problem_).

**Nel tuo progetto**: usi **ReLU** negli strati interni dell'encoder e del decoder. È la scelta di default per reti feed-forward profonde: funziona bene, è stabile, e non aggiunge complessità inutile. Il layer finale del decoder **non ha attivazione** (output lineare), il che è corretto perché il tuo target (i dati scalati) è nell'intervallo [0,1] ma non è strettamente binario — una funzione lineare può raggiungere qualsiasi valore reale, lasciando la rete libera di ricostruire anche valori vicini a 0 o 1 senza saturazione.

---

### 4.5 — Batch Normalization

**In generale**: Durante il training di reti profonde, le distribuzioni degli output intermedi degli strati tendono a spostarsi continuamente man mano che i pesi vengono aggiornati — fenomeno chiamato _internal covariate shift_. Questo rallenta il training e rende necessari learning rate molto bassi.

La **Batch Normalization** risolve il problema normalizzando l'output di ogni strato (all'interno di ogni mini-batch) in modo che abbia media zero e varianza unitaria, e poi riscalandolo con due parametri appresi (gamma e beta). I benefici sono:

- Training più veloce e stabile
- Permette di usare learning rate più alti
- Ha un lieve effetto regolarizzante

**Nel tuo progetto**: `nn.BatchNorm1d(128)` e `nn.BatchNorm1d(64)` vengono inserite **dopo** i layer lineari e **prima** della ReLU, negli strati interni dell'encoder. Noti che nel decoder non ci sono BatchNorm: è una scelta comune, poiché il decoder ha il compito di ricostruire i dati originali e la normalizzazione dell'output intermedio può disturbare questo processo.

---

### 4.6 — Dropout

**In generale**: Il **dropout** è una tecnica di regolarizzazione. Durante ogni passo di training, ogni neurone viene "spento" (il suo output posto a 0) in modo casuale con probabilità `p`. Questo forza la rete a non dipendere da nessun singolo neurone e a sviluppare rappresentazioni ridondanti e robuste. Il risultato è una riduzione dell'**overfitting**: la rete generalizza meglio su dati nuovi.

Il dropout viene applicato **solo durante il training**, non durante l'inferenza (per questo motivo in PyTorch si distingue tra `model.train()` e `model.eval()`, come vedremo nella cella 5).

**Nel tuo progetto**: `nn.Dropout(0.2)` con probabilità 0.2 viene inserito nell'encoder dopo ogni BatchNorm. Significa che durante ogni forward pass di training, il 20% dei neuroni viene spento casualmente. È una scelta conservativa e ragionevole: un dropout più alto (es. 0.5) potrebbe rendere il training instabile su questo tipo di dati.

---

### 4.7 — La funzione di perdita: MSELoss

**In generale**: La **funzione di perdita** (_loss function_) è la misura che quantifica quanto l'output del modello si discosta dall'output desiderato. È la bussola del training: l'algoritmo di ottimizzazione cerca di minimizzare questa quantità modificando i pesi della rete.

Per un problema di ricostruzione, le scelte più comuni sono:

- **MSE** (_Mean Squared Error_): `L = (1/n) Σ (x_i − x̂_i)²`. Penalizza molto gli errori grandi (per via del quadrato). È la scelta più comune per autoencoder.
- **MAE** (_Mean Absolute Error_): `L = (1/n) Σ |x_i − x̂_i|`. Meno sensibile agli outlier rispetto a MSE.
- **Huber Loss**: un ibrido MSE/MAE, quadratica per errori piccoli e lineare per errori grandi. Bilancia i vantaggi di entrambi.
- **BCE** (_Binary Cross-Entropy_): usata quando l'output è binario o strettamente nell'intervallo (0,1).

**Nel tuo progetto**: usi `nn.MSELoss()`. È la scelta naturale per un autoencoder su dati continui scalati in [0,1]. Nota che questa stessa funzione viene poi usata come **punteggio di anomalia** nella cella 6 (errore di ricostruzione per campione), il che crea consistenza perfetta tra la fase di training e quella di detection: il modello viene addestrato a minimizzare esattamente quella quantità che poi userai per rilevare le anomalie.

---

### 4.8 — L'ottimizzatore: Adam

**In generale**: L'ottimizzatore è l'algoritmo che usa il gradiente della loss per aggiornare i pesi della rete. Il gradiente indica la direzione in cui i pesi devono muoversi per aumentare la loss — quindi si va nella direzione opposta (discesa del gradiente).

Il parametro chiave è il **learning rate** (tasso di apprendimento): quanto grande è ogni aggiornamento. Troppo alto → i pesi oscillano e non convergono. Troppo basso → il training è lentissimo o si blocca in minimi locali.

I principali ottimizzatori sono:

- **SGD** (_Stochastic Gradient Descent_): la versione base. Aggiornamento semplice, richiede tuning manuale del learning rate.
- **SGD con momentum**: accumula una "velocità" nelle direzioni frequentemente aggiornate, accelerando la convergenza.
- **Adam** (_Adaptive Moment Estimation_): mantiene una stima della media e della varianza dei gradienti passati per ogni parametro, e adatta il learning rate individualmente per ogni peso. È oggi l'ottimizzatore di default per la maggior parte dei modelli deep learning.

**Nel tuo progetto**: `optim.Adam(autoencoder.parameters(), lr=0.0007)`. La scelta di Adam è quasi universale per questo tipo di problema. Il learning rate di `0.0007` è leggermente inferiore al classico default di Adam (`0.001`): è una scelta conservativa che favorisce una convergenza più stabile a scapito di una leggera lentezza iniziale.

---

# CELLA 5 — Addestramento del Modello

### Cosa fa questa cella

Esegue il training dell'autoencoder: divide i dati in training e validation, costruisce i DataLoader, esegue il loop di addestramento con early stopping, e ripristina i pesi migliori trovati.

---

### 5.1 — Divisione Training / Validation

**In generale**: Già hai un test set separato, ma il test set non deve essere mai visto durante il training — nemmeno per monitorare l'andamento. Serve quindi un terzo sottoinsieme: il **validation set**. I tre ruoli sono:

- **Training set**: i dati su cui il modello aggiorna i suoi pesi
- **Validation set**: dati mai visti dal modello durante il training, usati per monitorare le performance _durante_ il training e fare early stopping
- **Test set**: dati tenuti completamente separati, usati **una sola volta** alla fine per la valutazione finale

La divisione training/validation serve a rilevare l'**overfitting**: quando il modello smette di generalizzare e inizia a "memorizzare" i dati di training. Si manifesta come una loss di training che continua a scendere mentre la loss di validation smette di scendere o risale.

**Nel tuo progetto**: `train_test_split(x_train_scaled, test_size=0.2, random_state=42)` divide il training set benigno in 80% training e 20% validation. Il parametro `random_state=42` garantisce la riproducibilità: ogni volta che esegui il notebook ottieni la stessa divisione. Nota che entrambi i set contengono solo traffico benigno (il test set misto viene usato solo nella cella 7).

---

### 5.2 — TensorDataset e DataLoader

**In generale**: PyTorch lavora con **tensori** (la sua versione degli array numpy). Per gestire il training in mini-batch in modo efficiente, offre due astrazioni:

- **`TensorDataset`**: wrappa uno o più tensori in un oggetto dataset, rendendoli indicizzabili come una lista di campioni.
- **`DataLoader`**: si occupa di iterare sul dataset a lotti (_batch_), opzionalmente mescolando i dati a ogni epoca (shuffle) e parallelizzando il caricamento.

**Nel tuo progetto**: `TensorDataset(train_tensor, train_tensor)` — nota che il tensore di input e il tensore di target sono **lo stesso oggetto**. Questo perché l'autoencoder impara a riprodurre il suo stesso input: non c'è un'etichetta separata. Il `DataLoader` con `batch_size=128` divide i dati in mini-batch da 128 campioni, con `shuffle=True` per il training (importante: rimescolare ad ogni epoca previene che il modello memorizzi l'ordine dei campioni) e `shuffle=False` per la validation (l'ordine non ha importanza, si vuole solo calcolare la loss media).

---

### 5.3 — Il training loop (il ciclo di addestramento)

**In generale**: In PyTorch, a differenza di Keras, il loop di training è scritto esplicitamente. Questo ti permette di capire esattamente cosa succede ad ogni passo. Il ciclo si ripete per un numero massimo di **epoche** (_epochs_): una epoca corrisponde a un passaggio completo su tutto il training set.

All'interno di ogni epoca, il training avviene per mini-batch. Per ogni batch:

1. **Forward pass**: il batch viene passato attraverso la rete (`outputs = autoencoder(batch_x)`). Il modello calcola il suo output corrente.
    
2. **Calcolo della loss**: si misura quanto l'output differisce dall'input target (`loss = criterion(outputs, batch_x)`).
    
3. **Azzeramento dei gradienti**: `optimizer.zero_grad()`. In PyTorch i gradienti si accumulano di default: devi azzerarli prima di ogni backward pass per evitare che si sommino a quelli del batch precedente.
    
4. **Backward pass (backpropagation)**: `loss.backward()`. PyTorch calcola automaticamente il gradiente della loss rispetto a ogni parametro della rete, usando la regola della catena (chain rule). Questo è il cuore matematico del deep learning.
    
5. **Aggiornamento dei pesi**: `optimizer.step()`. Adam usa i gradienti calcolati per aggiornare i pesi della rete nella direzione che riduce la loss.
    

**Nel tuo progetto**: il loop dura al massimo 100 epoche. La loss viene mediata sul numero di campioni (`train_loss += loss.item() * batch_x.size(0)`) invece che sui batch, per ottenere una media più stabile indipendentemente dalla dimensione dei batch.

---

### 5.4 — Modalità Train vs Eval

**In generale**: In PyTorch, il modello ha due modalità:

- `model.train()`: attiva il Dropout e la BatchNorm in modalità training (BatchNorm usa le statistiche del batch corrente)
- `model.eval()`: disattiva il Dropout (tutti i neuroni attivi) e fa usare alla BatchNorm le statistiche accumulate durante il training. Questa è la modalità corretta per l'inferenza.

**Nel tuo progetto**: prima della fase di training di ogni epoca chiami `autoencoder.train()`, e prima della fase di validation chiami `autoencoder.eval()`. Questo è essenziale: senza questo, calcoleresti la validation loss con il Dropout attivo, ottenendo valori diversi ad ogni run e rendendo impossibile il confronto affidabile tra epoche.

Nella fase di validation usi anche `torch.no_grad()`: questo dice a PyTorch di non costruire il grafo computazionale per il calcolo dei gradienti, risparmiando memoria e velocizzando il calcolo (la validation non aggiorna i pesi, quindi i gradienti non servono).

---

### 5.5 — Early Stopping

**In generale**: L'**early stopping** è una tecnica per prevenire l'overfitting. Il principio è semplice: monitora la loss di validation ad ogni epoca. Se la validation loss smette di migliorare per un numero consecutivo di epoche (la **patience**), interrompi il training prima di raggiungere il numero massimo di epoche. Questo perché, quando la validation loss risale mentre la training loss continua a scendere, il modello sta iniziando a "memorizzare" i dati di training perdendo capacità di generalizzazione.

**Nel tuo progetto**:

- `patience = 10`: se per 10 epoche consecutive la validation loss non migliora, il training si ferma
- `best_val_loss = float('inf')`: inizialmente la "migliore loss" è infinita, così qualsiasi loss reale sarà migliore
- `patience_counter`: conta le epoche senza miglioramento
- `best_model_weights = copy.deepcopy(autoencoder.state_dict())`: quando viene trovata una validation loss migliore, si salva una copia profonda dei pesi correnti

L'uso di `copy.deepcopy` è fondamentale: senza di esso, salveresti un riferimento alla struttura dei pesi, che continua a essere modificata dalle epoche successive. Con `deepcopy` ottieni uno snapshot immutabile.

Alla fine del training, `autoencoder.load_state_dict(best_model_weights)` ripristina i pesi migliori trovati durante tutto il training — non necessariamente quelli dell'ultima epoca. Questo è il punto chiave dell'early stopping: ci si ferma quando la validation loss è peggiorata (segno di overfitting), ma si torna indietro ai pesi del momento migliore.

---

# CELLA 6 — Calcolo Dinamico della Soglia (Scoring)

### Cosa fa questa cella

Calcola l'errore di ricostruzione su tutti i campioni benigni del training set, e da questa distribuzione di errori deriva automaticamente la soglia τ (_tau_) che verrà usata per classificare i campioni di test come normali o anomali.

---

### 6.1 — Il punteggio di anomalia

**In generale**: In un sistema di anomaly detection, ogni campione deve ricevere un **punteggio di anomalia** (_anomaly score_): un numero che rappresenta "quanto è anomalo questo campione". Un punteggio alto significa alta probabilità di anomalia.

Esistono molti modi per definire questo punteggio: distanza dal cluster più vicino, log-likelihood sotto un modello probabilistico, ecc. Per un autoencoder, la scelta naturale è l'**errore di ricostruzione**.

**Nel tuo progetto**: per ogni campione x, l'errore di ricostruzione è `MSE(x) = (1/d) Σ (x_i − x̂_i)²` dove d è la dimensione del vettore di feature e x̂ è l'output del decoder. Un campione benigno (simile a ciò su cui il modello è stato addestrato) avrà un errore basso. Un attacco avrà un errore alto.

Il codice calcola questo su tutti i campioni benigni del training set:

```
mse_train = np.mean((x_train_benign - pred_train)**2, axis=1)
```

Il parametro `axis=1` significa: media lungo le feature (per ogni riga/campione), non su tutti i campioni insieme. Il risultato è un array di un valore per ogni campione.

---

### 6.2 — La soglia e come viene calcolata

**In generale**: La soglia (_threshold_) converte il punteggio continuo in una decisione binaria: normale o anomalo. Ogni campione con punteggio sopra la soglia è classificato come anomalia. La scelta della soglia implica un **tradeoff**: abbassarla aumenta la detection rate ma aumenta anche i falsi allarmi; alzarla riduce i falsi allarmi ma fa perdere attacchi reali.

Le strategie più comuni per determinare la soglia sono:

- **Percentile della distribuzione degli errori benigni**: si guarda la distribuzione degli errori sui campioni normali e si sceglie il percentile alto (es. 95°, 99°) come soglia — "voglio che il 99% del traffico normale venga classificato correttamente come normale"
- **Ottimizzazione su validation set**: si cerca la soglia che massimizza una metrica (es. F1-score) sul validation set
- **Metodo statistico**: media + k × deviazione standard degli errori benigni

**Nel tuo progetto**: `tau = np.percentile(mse_train, 99)`. Il **99° percentile** degli errori di ricostruzione sul training benigno diventa la soglia. Questo significa: stai dicendo che il 99% del traffico benigno visto durante il training ha un errore di ricostruzione sotto τ, e solo l'1% supera questa soglia. In produzione, un campione con errore > τ è dichiarato anomalia.

La parola "dinamica" nel titolo della cella è importante: la soglia non è fissata a priori da te, ma viene **calcolata automaticamente** dal comportamento del modello sui dati benigni. Se il modello cambia (dopo un re-training), la soglia si aggiorna di conseguenza.

La scelta del 99° percentile implica un **false alarm rate teorico dell'1%** sul training set benigno, il che è compatibile con il tuo KPI target di FAR < 5%.

---

# CELLA 7 — Valutazione e Calcolo dei KPI sul Test Set

### Cosa fa questa cella

Esegue l'inferenza dell'autoencoder su tutto il test set (che contiene traffico misto: benigno e malevolo), calcola il punteggio di anomalia per ogni campione, applica la soglia τ per ottenere le predizioni binarie, e misura le performance con le metriche di valutazione.

---

### 7.1 — Il test set e l'inferenza

Il test set contiene sia traffico benigno (label=0) che tutti i tipi di attacco (label=1). È il momento della verità: il modello non ha mai visto questi dati, e deve classificare ogni campione come normale o anomalia basandosi solo sull'errore di ricostruzione.

Il codice calcola l'errore di ricostruzione per ogni campione del test set (`mse_test`), poi applica la soglia:

```python
y_pred = (mse_test > tau).astype(int)
```

Campioni con errore sopra τ → predetti come anomalia (1); sotto τ → predetti come normali (0).

---

### 7.2 — La Confusion Matrix

**In generale**: La **matrice di confusione** è il punto di partenza per valutare qualsiasi classificatore binario. Confronta le predizioni del modello con le etichette reali e conta quattro quantità:

||Predetto: Normale|Predetto: Anomalia|
|---|---|---|
|**Reale: Normale**|TN (True Negative)|FP (False Positive)|
|**Reale: Anomalia**|FN (False Negative)|TP (True Positive)|

- **TP** (_True Positive_): un attacco reale che il sistema ha correttamente identificato come attacco → rilevamento corretto
- **TN** (_True Negative_): traffico benigno che il sistema ha correttamente classificato come normale → nessun falso allarme
- **FP** (_False Positive_): traffico benigno che il sistema ha erroneamente classificato come attacco → **falso allarme**. Costoso in un sistema di sicurezza perché richiede tempo di analisi umana
- **FN** (_False Negative_): un attacco reale che il sistema non ha rilevato → **mancato rilevamento**. Il più pericoloso: l'attacco passa inosservato

**Nel tuo progetto**: `cm.ravel()` decompone la matrice di confusione nei quattro valori TN, FP, FN, TP nell'ordine specifico di scikit-learn.

---

### 7.3 — AUC-ROC

**In generale**: L'**AUC** (_Area Under the Curve_) è la metrica più robusta per valutare un classificatore binario. Si calcola a partire dalla **curva ROC** (_Receiver Operating Characteristic_):

La curva ROC si ottiene variando la soglia di classificazione da 0 a ∞ e, per ogni soglia, plottando:

- Asse X: **False Positive Rate** (FPR) = FP / (FP + TN)
- Asse Y: **True Positive Rate** (TPR) = TP / (TP + FN)

Un classificatore casuale (che ignora completamente l'input) produce una curva ROC che è una diagonale retta → AUC = 0.5. Un classificatore perfetto ha AUC = 1.0 (curva che tocca l'angolo in alto a sinistra, 100% di TP con 0% di FP).

L'AUC è **indipendente dalla soglia scelta**: valuta la qualità del punteggio di anomalia in sé, a prescindere da dove metti τ. Questo la rende ideale per confrontare modelli diversi.

**Nel tuo progetto**: `roc_auc_score(y_test, mse_test)` — nota che passi `mse_test` (il punteggio continuo) e non `y_pred` (la classificazione binaria). Questo è corretto per l'AUC: sklearn varia internamente la soglia su tutti i possibili valori di `mse_test` per costruire la curva ROC. Il tuo KPI target è **AUC > 0.90**.

---

### 7.4 — Detection Rate (Recall / Sensitivity)

**In generale**: Il **Detection Rate** (DR), anche chiamato _Recall_, _Sensitivity_ o _True Positive Rate_, misura la frazione di attacchi reali che il sistema riesce a identificare:

`DR = TP / (TP + FN)`

Un DR del 95% significa che su 100 attacchi reali, il sistema ne rileva 95 e ne manca 5. Nel contesto della sicurezza informatica, un FN è molto pericoloso: un attacco non rilevato può causare danni enormi. Per questo il target è alto.

**Nel tuo progetto**: target **DR > 95%**, calcolato come `TP / (TP + FN)`. Corrisponde alla _sensibilità_ del tuo rilevatore: quanto è bravo a "sentire" gli attacchi.

---

### 7.5 — False Alarm Rate (FPR)

**In generale**: Il **False Alarm Rate** (FAR), anche chiamato _False Positive Rate_ o _Fall-out_, misura la frazione di traffico benigno che viene erroneamente classificato come attacco:

`FAR = FP / (FP + TN)`

In un sistema di sicurezza operativo, un FAR alto è devastante: il team SOC (_Security Operations Center_) si ritrova sommerso di falsi allarmi, i veri attacchi si perdono nel rumore, e il sistema viene disabilitato dagli operatori frustrati. È il problema noto come _alert fatigue_.

**Nel tuo progetto**: target **FAR < 5%**, calcolato come `FP / (FP + TN)`.

---

### 7.6 — Il tradeoff fondamentale DR/FAR

Il DR e il FAR sono **in tensione**: spostando la soglia τ verso il basso, si rilevano più attacchi (DR sale) ma anche più falsi allarmi (FAR sale). Alzando la soglia, si riducono i falsi allarmi (FAR scende) ma si perdono alcuni attacchi (DR scende).

La curva ROC è esattamente la visualizzazione di questo tradeoff al variare di τ. Il fatto che il tuo sistema voglia contemporaneamente **DR > 95% e FAR < 5%** è un vincolo stringente che giustifica l'impegno nella scelta dell'architettura e nel preprocessing. Se il tuo AUC finale è > 0.90, significa che esiste una soglia τ che soddisfa entrambi i vincoli simultaneamente.

---

# CELLA 8 — Simulazione

### Cosa fa questa cella

Simula il funzionamento del sistema in uno scenario operativo reale: per un campione di traffico che arriva, l'autoencoder lo analizza in tempo reale e decide se è benigno o anomalia, misurando anche la latenza.

---

### 8.1 — Perché una simulazione

**In generale**: Le metriche aggregate (AUC, DR, FAR) dicono quanto bene funziona il sistema in media su migliaia di campioni. Ma in un sistema di sicurezza reale, il rilevamento avviene **campione per campione** con una latenza che deve essere sufficientemente bassa da non bloccare il traffico di rete. La simulazione dimostra che il sistema è praticabile operativamente.

**Nel tuo progetto**: il codice seleziona casualmente 5 campioni benigni e 5 campioni di attacco dal test set, li mescola, e li analizza uno per uno. Per ognuno misura:

- L'errore di ricostruzione (anomaly score)
- Il confronto con τ (predizione: normale o anomalia)
- La latenza in millisecondi (`time.time()` prima e dopo l'inferenza)
- Il confronto con l'etichetta reale (predizione corretta o errata)

La latenza dell'inferenza di un autoencoder su CPU è dell'ordine di pochi millisecondi per campione, il che dimostra la fattibilità del sistema in un contesto di analisi di traffico di rete.

---

# CELLA 9 — Salvataggio del Modello

### Cosa fa questa cella

Persiste su disco tutti gli artefatti necessari per usare il sistema in produzione: i pesi del modello, lo scaler, e un riepilogo dei KPI.

---

### 9.1 — Perché salvare il modello

**In generale**: Il training è costoso in tempo e risorse. Una volta completato, il modello addestrato deve essere salvato in modo che possa essere caricato in produzione senza riaddestrare da zero. Questo processo si chiama **serializzazione** o **persistenza** del modello.

**Nel tuo progetto** salvi tre artefatti distinti:

**`autoencoder_zeroday.pth`** — I pesi della rete neurale in formato PyTorch. `torch.save(autoencoder.state_dict(), ...)` salva lo _state dict_: un dizionario Python che mappa il nome di ogni layer ai suoi pesi (tensori). Non salva l'architettura: per ricaricare il modello devi definire di nuovo la classe `Autoencoder` e poi caricare i pesi con `load_state_dict()`.

**`scaler_zeroday.joblib`** — Lo scaler MinMaxScaler addestrato sui dati benigni. Questo è **fondamentale**: in produzione, ogni nuovo campione di traffico deve essere scalato usando esattamente gli stessi parametri (min e max) usati durante il training. Se usi un scaler diverso, le feature avranno range diversi e le previsioni del modello saranno prive di senso. `joblib` è la libreria standard per serializzare oggetti scikit-learn in modo efficiente.

**`results_summary.json`** — Un file JSON con i KPI finali (AUC, DR, FAR), la soglia τ, la confusion matrix, e i target prefissati. Serve per documentazione, auditing, e per caricare i KPI nella dashboard senza rieseguire tutto il notebook.

---

# CELLA 10 — Preparazione Dashboard Operativa

### Cosa fa questa cella

Esporta i dati necessari per alimentare una dashboard di monitoraggio in tempo reale, costruita con Streamlit.

---

### 10.1 — Cos'è e perché serve una dashboard

**In generale**: Un modello di anomaly detection non viene usato in isolamento da un ricercatore: in un contesto operativo reale, i risultati devono essere presentati agli analisti di sicurezza (SOC) in modo comprensibile e azionabile. Una **dashboard** è un'interfaccia visuale che mostra in tempo reale lo stato del sistema, le metriche di performance, e gli allarmi attivi.

**Streamlit** è un framework Python open-source che permette di costruire web app interattive direttamente da script Python, senza scrivere HTML o JavaScript. È molto popolare per il deployment rapido di tool di data science e ML.

---

### 10.2 — I dati esportati

**Nel tuo progetto**: esporti in `dashboard_data.json` un campione di 400 osservazioni casuali dal test set (per rendere la dashboard reattiva senza caricare tutti i dati), con:

- `mse`: i punteggi di anomalia per ogni campione → permette di visualizzare la distribuzione degli errori
- `labels`: le etichette reali → permette di colorare i punti (rosso/verde) nel grafico
- `tau`: la soglia → permette di disegnare la linea di confine nel grafico
- `auc`, `dr`, `far`: i KPI → visualizzati come metriche numeriche nella dashboard

`np.random.seed(42)` garantisce che il campione sia sempre lo stesso ad ogni esecuzione.

---

# RIEPILOGO GENERALE

Se dovessi spiegare il progetto in 30 secondi al professore, potresti dire:

> _"Ho implementato un sistema di rilevamento di attacchi zero-day basato su un autoencoder deep learning. Il modello viene addestrato esclusivamente su traffico benigno, imparando a ricostruirlo fedelmente. Quando gli viene presentato un attacco — un pattern che non ha mai visto — l'errore di ricostruzione è alto. Una soglia τ calcolata al 99° percentile degli errori sul training set benigno distingue normale da anomalia. Il sistema raggiunge AUC > 0.90, Detection Rate > 95% e False Alarm Rate < 5% sul dataset standard UNSW-NB15."_

I concetti chiave su cui prepararti per eventuali domande del professore sono:

- Perché l'anomaly detection con autoencoder funziona per gli zero-day (one-class learning)
- Il ruolo di ogni componente dell'architettura (encoder, bottleneck, decoder, BatchNorm, Dropout)
- Perché si addestra solo su traffico benigno e perché lo scaler non viene fittato sul test set
- Il significato preciso di AUC, Detection Rate e False Alarm Rate, e il tradeoff tra loro
- Cosa rappresenta la soglia τ e perché si usa il 99° percentile

---

# PANORAMICA GENERALE DELLA DASHBOARD

Il notebook produce un modello addestrato e dei risultati. Ma un sistema operativo di sicurezza non viene usato dentro un Jupyter Notebook: serve un'**interfaccia** che permetta a un analista di vedere i risultati in modo chiaro, senza toccare il codice. La dashboard è esattamente questo: un layer di presentazione che consuma gli artefatti prodotti dal notebook (`dashboard_data.json`) e li mostra come un'applicazione web interattiva.

Il flusso è:

```
Notebook → dashboard_data.json → dashboard.py → Browser
```

---

# LE LIBRERIE IMPORTATE

```python
import streamlit as st
import json
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

**In generale**: Ogni import porta in memoria un insieme di strumenti. `pandas`, `numpy` e `matplotlib` li conosci già dal notebook. Le due novità sono:

- **`streamlit`** — Un framework Python open-source che permette di costruire web application interattive scrivendo solo Python puro, senza HTML, CSS o JavaScript. Ogni volta che salvi il file, Streamlit esegue il codice dall'alto verso il basso e aggiorna il browser automaticamente. Il suo modello di esecuzione è semplice ma potente: ogni elemento visuale (titolo, grafico, tabella, metrica) viene creato chiamando una funzione `st.qualcosa()`.
	
- **`json`** — Libreria della standard library Python per leggere e scrivere file JSON. Ti serve per caricare `dashboard_data.json` prodotto dal notebook.

---

# CONFIGURAZIONE DELLA PAGINA

```python
st.set_page_config(page_title="Zero-Day Anomaly Detection", layout="wide")
```

**In generale**: Ogni applicazione Streamlit ha una configurazione globale che determina come appare la pagina nel browser. Questa funzione deve essere chiamata **come prima istruzione Streamlit** del file, prima di qualsiasi altro elemento visuale, altrimenti Streamlit genera un errore.

I parametri principali di `set_page_config` sono:

- `page_title`: il testo che appare nella scheda del browser
- `layout`: `"centered"` (default, contenuto centrato con larghezza limitata) o `"wide"` (il contenuto usa tutta la larghezza del browser)
- `page_icon`: un'emoji o un'immagine che appare come favicon nella scheda
- `initial_sidebar_state`: se la sidebar è aperta o chiusa all'avvio

**Nel tuo progetto**: hai scelto `layout="wide"` perché la dashboard mostra dati tabulari e metriche affiancate, che beneficiano di tutto lo spazio orizzontale disponibile. Con il layout centrato di default, la tabella sarebbe stretta e le colonne delle metriche si sovrapporrebbero su schermi normali.

---

# TITOLO E DESCRIZIONE

```python
st.title("🛡️ Dashboard Operativa: Rilevamento Attacchi Zero-Day")
st.markdown("Monitoraggio delle anomalie...")
```

**In generale**: Streamlit offre diversi livelli di elementi testuali:

- `st.title()`: titolo principale della pagina (H1 HTML)
- `st.header()`: intestazione di sezione (H2)
- `st.subheader()`: sottointestazione (H3)
- `st.text()`: testo semplice non formattato
- `st.markdown()`: testo con supporto completo a Markdown (grassetto, corsivo, link, LaTeX, ecc.)
- `st.caption()`: testo piccolo, usato per note o didascalie

**Nel tuo progetto**: usi `st.title()` per il titolo principale e `st.markdown()` per la descrizione. La scelta di `markdown` invece di `text` ti permette di usare la formattazione Markdown nella descrizione se necessario — oggi è testo semplice, ma potresti aggiungere **grassetto** o link senza cambiare il tipo di elemento.

---

# CARICAMENTO DATI CON CACHING

```python
@st.cache_data
def load_data():
    with open("dashboard_data.json", "r") as f:
        return json.load(f)
```

### Il decoratore `@st.cache_data`

**In generale**: Streamlit ha un comportamento peculiare che è importante capire: **riesegue l'intero script Python dall'inizio alla fine ogni volta** che l'utente interagisce con la pagina (clicca un bottone, sposta uno slider, ecc.). Questo semplifica enormemente la gestione dello stato, ma crea un problema: se ogni interazione rilegge un file JSON (o, peggio, ricarica un modello da disco o fa una query al database), la dashboard diventa lenta.

Il decoratore `@st.cache_data` risolve il problema: la prima volta che viene chiamata `load_data()`, Streamlit esegue la funzione normalmente e **mette in cache il risultato**. Alle chiamate successive, invece di rieseguire la funzione, restituisce direttamente il risultato dalla cache. La cache viene invalidata solo se il codice della funzione cambia.

Esistono due tipi di cache in Streamlit:

- `@st.cache_data`: per dati serializzabili (DataFrame, dizionari, liste, ecc.) — la tua scelta
- `@st.cache_resource`: per risorse non serializzabili che non vanno copiate (connessioni al database, modelli ML caricati in memoria)

**Nel tuo progetto**: `@st.cache_data` è perfetto perché stai cachando il contenuto di un file JSON (un dizionario Python), che è perfettamente serializzabile. Significa che il file viene letto da disco **una sola volta** per tutta la sessione, rendendo la dashboard reattiva.

---

### Gestione dell'errore FileNotFoundError

```python
try:
    data = load_data()
except FileNotFoundError:
    st.error("File 'dashboard_data.json' non trovato. Eseguire il Jupyter Notebook")
    st.stop()
```

**In generale**: In Python, il blocco `try/except` permette di intercettare eccezioni (errori a runtime) e gestirle in modo controllato invece di far crashare il programma. `FileNotFoundError` è l'eccezione specifica che Python solleva quando si tenta di aprire un file che non esiste.

In Streamlit, se non gestisci gli errori, l'utente vede una traccia di stack Python grezza nel browser — poco professionale. Le alternative per mostrare errori in modo elegante sono:

- `st.error("messaggio")`: box rosso con messaggio di errore
- `st.warning("messaggio")`: box giallo per avvertimenti
- `st.info("messaggio")`: box blu per informazioni
- `st.success("messaggio")`: box verde per conferme

**Nel tuo progetto**: se il file JSON non esiste (cioè se il notebook non è stato eseguito), la dashboard mostra un messaggio di errore leggibile in un box rosso e poi chiama `st.stop()`. Quest'ultima funzione è importante: interrompe immediatamente l'esecuzione del resto dello script. Senza di essa, il codice successivo tenterebbe di accedere alle variabili `tau`, `auc`, ecc. che non esistono ancora, generando una serie di errori a cascata.

---

# ESTRAZIONE DELLE METRICHE

```python
tau = data["tau"]
auc = data["auc"]
dr = data["dr"]
far = data["far"]
mse_list = data["mse"]
labels_list = data["labels"]
```

**In generale**: `json.load()` restituisce un dizionario Python che rispecchia la struttura del file JSON. L'accesso ai valori avviene con la sintassi `dizionario["chiave"]`. Qui stai semplicemente scompattando il dizionario in variabili con nome significativo, per rendere il codice successivo più leggibile.

**Nel tuo progetto**: queste variabili corrispondono esattamente alle chiavi che hai inserito nel JSON nella cella 10 del notebook (`"tau"`, `"auc"`, `"dr"`, `"far"`, `"mse"`, `"labels"`). La coerenza tra i nomi nel notebook e nella dashboard è fondamentale: se cambiassi un nome nel notebook senza aggiornare la dashboard, otterresti un `KeyError`.

---

# VISUALIZZAZIONE DEI KPI

```python
st.header("Metriche di Riferimento del Modello")
col1, col2, col3, col4 = st.columns(4)

col1.metric("Soglia di Rilevamento (τ)", f"{tau:.5f}")
col2.metric("AUC", f"{auc:.4f}")
col3.metric("Detection Rate (DR)", f"{dr}%", "+ Target >95%")
col4.metric("False Alarm Rate (FAR)", f"{far}%", "- Target <5%", delta_color="inverse")
```

### Il layout a colonne

**In generale**: Streamlit, per default, dispone gli elementi in verticale (uno sotto l'altro). Per affiancare elementi orizzontalmente, si usa `st.columns(n)`, che divide la larghezza disponibile in `n` colonne uguali. Si possono anche dare pesi diversi: `st.columns([1, 2, 1])` crea tre colonne dove quella centrale è larga il doppio delle laterali.

Il risultato è una tupla di oggetti colonna. Ogni oggetto si usa come un "mini-contesto Streamlit": invece di chiamare `st.metric()` nel flusso principale, chiami `col1.metric()` per mettere l'elemento nella prima colonna.

**Nel tuo progetto**: `st.columns(4)` crea quattro colonne uguali, una per ogni KPI principale. Questa è la disposizione standard delle _scorecard_ nelle dashboard di monitoring: i numeri più importanti in cima, affiancati, immediatamente visibili.

---

### Il widget `metric`

**In generale**: `st.metric()` è un widget specifico di Streamlit progettato proprio per visualizzare KPI numerici. Mostra:

- Un'**etichetta** (label): il nome della metrica
- Un **valore principale** (value): il numero grande al centro
- Un **delta** (opzionale): una variazione rispetto a un riferimento, mostrata con una freccia colorata verde (positivo) o rossa (negativo)
- `delta_color`: controlla come interpretare il segno del delta (`"normal"`: verde=positivo, `"inverse"`: verde=negativo, `"off"`: grigio neutro)

**Nel tuo progetto**:

`col3.metric("Detection Rate (DR)", f"{dr}%", "+ Target >95%")` — il delta `"+ Target >95%"` non è una variazione reale (non confronti con un valore precedente), ma stai usando creativamente il campo delta come **etichetta del target**. Il `+` all'inizio fa sì che Streamlit mostri la freccia verde, che visivamente comunica "questo è un valore da massimizzare".

`col4.metric("False Alarm Rate (FAR)", f"{far}%", "- Target <5%", delta_color="inverse")` — qui `delta_color="inverse"` è la scelta chiave: senza di essa, un FAR basso verrebbe mostrato con una freccia rossa (perché il delta inizia con `-`). Con `"inverse"`, Streamlit inverte la logica: valori negativi/bassi diventano verdi. Semanticamente corretto: un FAR basso è un **buon risultato**, deve essere verde.

---

# PREPARAZIONE DEI DATI

```python
df = pd.DataFrame({
    "MSE (Errore Ricostruzione)": mse_list,
    "Label Reale": ["Attacco" if l == 1 else "Benigno" for l in labels_list]
})
```

**In generale**: `pd.DataFrame()` crea un DataFrame a partire da un dizionario dove le chiavi sono i nomi delle colonne e i valori sono le liste di dati. La **list comprehension** `["Attacco" if l == 1 else "Benigno" for l in labels_list]` è un modo compatto di trasformare una lista di valori binari (0 e 1) in etichette testuali leggibili.

**Nel tuo progetto**: converti le etichette numeriche in stringhe perché queste compariranno nella tabella mostrata all'utente finale. Un analista di sicurezza capisce "Attacco" e "Benigno" immediatamente; vedrebbe "0" e "1" e dovrebbe ricordarsi quale è quale.

---

### Classificazione con lambda

```python
df["Predizione del Modello"] = df["MSE (Errore Ricostruzione)"].apply(
    lambda x: "🚨 MINACCIA ZERO-DAY" if x > tau else "✅ Normale"
)
```

**In generale**: `Series.apply()` è uno dei metodi più usati di pandas: applica una funzione a ogni elemento di una colonna e restituisce una nuova serie con i risultati. La funzione può essere definita separatamente oppure passata come **lambda** — una funzione anonima monoriga della forma `lambda argomento: espressione`.

**Nel tuo progetto**: per ogni valore di MSE nella colonna, confronti con `tau` e restituisci la stringa appropriata. Questo ricrea la logica di classificazione del notebook (`y_pred = (mse_test > tau).astype(int)`) in forma leggibile per la dashboard, aggiungendo anche emoji che rendono la classificazione immediatamente riconoscibile a colpo d'occhio: 🚨 per gli attacchi, ✅ per il traffico normale.

---

### Verifica della correttezza con `np.where`

```python
df["Esito Classificazione"] = np.where(
    ((df["Label Reale"] == "Attacco") & (df["MSE (Errore Ricostruzione)"] > tau)) | 
    ((df["Label Reale"] == "Benigno") & (df["MSE (Errore Ricostruzione)"] <= tau)),
    "Corretto", "Errato"
)
```

**In generale**: `np.where(condizione, valore_se_vero, valore_se_falso)` è la versione vettorizzata dell'if-else, applicata su tutto l'array contemporaneamente — molto più efficiente di un ciclo Python. La condizione può essere composta con operatori booleani vettoriali:

- `&` → AND elemento per elemento (non `and`, che è scalare)
- `|` → OR elemento per elemento (non `or`)
- `~` → NOT elemento per elemento

**Nel tuo progetto**: la condizione implementa la logica della **matrice di confusione**: il modello ha classificato correttamente se:

- il campione è un Attacco **E** l'MSE supera τ (→ True Positive), **OPPURE**
- il campione è Benigno **E** l'MSE non supera τ (→ True Negative)

In tutti gli altri casi (FP e FN) l'esito è "Errato". Questa colonna rende immediatamente visibili gli errori del modello nella tabella.

---

# TABELLA CON EVIDENZIAZIONE DEGLI ERRORI

```python
def color_errors(val):
    color = '#ffcccc' if val == 'Errato' else ''
    return f'background-color: {color}'

styled_df = df.style.map(color_errors, subset=['Esito Classificazione'])
st.dataframe(styled_df, use_container_width=True, height=400)
```

### Il pandas Styler

**In generale**: pandas offre un sistema di styling che permette di applicare formattazione visuale alle celle di un DataFrame prima di mostrarlo. Si accede tramite `df.style`, che restituisce un oggetto `Styler`. I metodi principali sono:

- `.map(funzione, subset=[...])`: applica una funzione cella per cella alle colonne specificate, la funzione deve restituire una stringa CSS
- `.apply(funzione, subset=[...])`: applica una funzione per colonna/riga intera, utile per evidenziare minimo/massimo di una colonna
- `.format(...)`: formatta i valori numerici (decimali, percentuali, ecc.)
- `.background_gradient(...)`: applica una scala di colori in base ai valori numerici

**Nel tuo progetto**: `color_errors` è una funzione che riceve il valore di ogni cella della colonna `'Esito Classificazione'`. Se il valore è `'Errato'`, restituisce la stringa CSS `'background-color: #ffcccc'` (un rosa/rosso chiaro). Per le celle con valore `'Corretto'`, restituisce una stringa vuota (nessuno stile applicato, sfondo bianco di default).

Il risultato visivo è che tutti gli errori del modello (FP e FN) vengono evidenziati in rosso nella tabella, permettendo all'analista di identificarli istantaneamente.

---

### `st.dataframe`

**In generale**: Streamlit ha due widget per mostrare tabelle:

- `st.dataframe()`: tabella **interattiva** — l'utente può ordinare le colonne cliccandoci sopra, filtrare, cercare, e ridimensionare le colonne
- `st.table()`: tabella **statica** — visualizzazione semplice senza interattività, si adatta automaticamente all'altezza del contenuto

**Nel tuo progetto**: usi `st.dataframe()` con due parametri:

- `use_container_width=True`: la tabella si espande per occupare tutta la larghezza disponibile della colonna (coerente con `layout="wide"`)
- `height=400`: fissa l'altezza della tabella a 400 pixel, abilitando lo **scroll verticale** per i 400 campioni. Senza questo parametro, Streamlit mostrerebbe tutte le righe in un'unica tabella altissima, rendendo necessario scrollare tutta la pagina per raggiungere gli eventuali elementi sotto.

---

# RIEPILOGO DELLA DASHBOARD

> _"La dashboard è il layer operativo del sistema. Legge i dati prodotti dall'inferenza del modello, espone i KPI principali in cima con colori semanticamente corretti (verde = buono), e mostra la tabella completa di ogni campione analizzato con l'errore di ricostruzione, la predizione del modello e l'esito. Gli errori di classificazione vengono evidenziati in rosso. È costruita con Streamlit, che trasforma script Python in web app senza richiedere conoscenze di HTML o JavaScript."_

I concetti chiave da sapere su questa parte del progetto sono:

- Il modello di esecuzione di Streamlit (riesecuzione completa ad ogni interazione) e perché `@st.cache_data` è necessario
- La differenza tra `delta_color="normal"` e `"inverse"` e perché per il FAR si usa inverse
- Come `np.where` implementa la logica della matrice di confusione in modo vettorizzato
- Il pandas Styler e come la funzione `color_errors` restituisce CSS