MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 22/05/2026
FONTE: 

---

![[Pasted image 20260522171028 1.png]]

Python

```
cnn.summary()
```

L'ispezione dell'architettura di una rete neurale convoluzionale avviene tramite il metodo `summary()`, il quale genera un prospetto tabellare che descrive la topologia dei livelli, le dimensioni dei tensori in uscita e la densità dei parametri da ottimizzare.

All'interno della tabella si distinguono tre colonne fondamentali:

- **Layer (type):** Mostra il nome univoco dell'istanza e la tipologia di livello strutturale (es. `Conv2D`, `MaxPooling2D`, `Flatten`, `Dense`).
    
- **Output Shape:** Specifica la dimensionalità dei tensori propagati in avanti al livello successivo. La presenza del valore `None` come prima coordinata indica che la dimensione del batch (_batch size_) non è prefissata rigidamente a priori; il modello è configurato in modo dinamico per elaborare un numero arbitrario di campioni di addestramento o di test all'interno del flusso di input.
    
- **Param #:** Rappresenta il numero totale di pesi sinaptici (_weights_) e bias che l'algoritmo deve apprendere e aggiornare iterativamente tramite backpropagation durante la fase di training.
    

Nonostante questa specifica convnet sia considerata un'architettura relativamente piccola e minimale, progettata per elaborare immagini minuscole (i campioni MNIST sono inferiori a un quarto della dimensione di una comune icona da smartphone), la rete richiede l'ottimizzazione di quasi 500.000 parametri totali. La stragrande maggioranza di questi parametri si concentra nel primo livello completamente connesso (`dense`), in cui la linearizzazione operata dal livello `flatten` genera un'elevata densità di interconnessioni verso i 128 neuroni successivi. Questa evidenza strutturale sottolinea l'esplosione della complessità computazionale e la conseguente richiesta di hardware parallelo quando si passa dall'elaborazione di micro-immagini a flussi video ad alta risoluzione (4K) o a immagini scattate da moderne fotocamere digitali.

![[Pasted image 20260522173617.png]]
Output:
![[Pasted image 20260522173739.png|383]]
![[Pasted image 20260522173853.png|384]]
Questo codice ci permette di visualizzare la struttura del modello e di salvare l'output sotto forma di immagine.

![[Pasted image 20260522174248.png]]
Python
```
cnn.compile(optimizer='adam',
            loss='sparse_categorical_crossentropy',
            metrics=['accuracy'])
```

La configurazione del processo di addestramento del modello avviene invocando il metodo `compile()`, il quale definisce gli algoritmi e le metriche necessarie per guidare l'ottimizzazione dei pesi della rete.

![[Pasted image 20260522174034.png|699]]
Il metodo richiede la parametrizzazione di tre argomenti principali:

- **`optimizer='adam'`**: Configura l'algoritmo di ottimizzazione Adam. Si tratta di una variante avanzata della discesa stocastica del gradiente (SGD) che adatta dinamicamente il tasso di apprendimento (_learning rate_) per ciascun parametro durante il training.
    
- **`loss='categorical_crossentropy'`**: Specifica la funzione di perdita usata dall'ottimizzatore. L'ottimizzatore tenta di minimizzare i valori restituiti dalla loss function. Per la classificazione binaria, Keras mette a disposizione $binary\_crossentropy$ mentre per la regressione usa $mean\_squared\_error$.
    
- **`metrics=['accuracy']`**: Definisce la lista di metriche da monitorare per valutare il modello durante le fasi di training e testing. L'accuratezza esprime semplicemente la percentuale di immagini classificate correttamente. A differenza della loss function, questa metrica serve esclusivamente come indicatore di comprensione immediata per lo sviluppatore e non viene utilizzata direttamente dall'ottimizzatore per calcolare i gradienti.

![[Pasted image 20260522180346.png]]

Python

```
cnn.fit(X_train, y_train, epochs=5, batch_size=64, validation_split=0.1)
```

L'addestramento vero e proprio di un modello in Keras viene avviato invocando il metodo `fit`. Questo metodo accetta in ingresso i dati di training e le relative etichette, coordinando il ciclo di ottimizzazione attraverso la configurazione di specifici iperparametri:

- **`epochs=5`:** Definisce il numero di epoche, ossia i cicli completi di addestramento. Durante ogni singola epoca, l'algoritmo elabora esattamente una volta ogni singolo campione presente nel dataset di training. Rappresenta un iperparametro cruciale che potrebbe richiedere una fase di ottimizzazione (_tuning_) per evitare fenomeni di underfitting o overfitting.
    
- **`batch_size=64`:** Specifica la dimensione del lotto, ovvero il numero di campioni da processare simultaneamente prima di calcolare il gradiente e aggiornare i pesi del modello. Nella maggior parte delle architetture si tende a impostare questo valore utilizzando potenze di 2, generalmente comprese in un intervallo che va da 32 a 512.
    
- **`validation_split=0.1`:** Indica al modello di isolare e mettere da parte l'ultimo 10% dei campioni di addestramento forniti per utilizzarli esclusivamente come set di validazione.
    

Al termine di ciascuna epoca, la rete sfrutta questi campioni di validazione per generare delle predizioni e calcolare in modo trasparente i relativi valori di loss e accuratezza di validazione (_validation loss and accuracy_). Il monitoraggio di queste metriche permette allo sviluppatore di capire se sia necessario modificare la struttura interna dei livelli, sintonizzare gli iperparametri del metodo `fit` o alterare la composizione stessa dei layer all'interno della rete. In alternativa a una suddivisione automatica, è possibile passare un set di validazione esterno e già separato sfruttando l'apposito argomento `validation_data`.

![[Pasted image 20260522180645.png]]

Il tempo richiesto per questo processo dipende dalla CPU utilizzata.

![[Pasted image 20260522180738 1.png]]
L'output mostra il log dettagliato generato da Keras durante l'esecuzione del metodo `fit()`. Analizzando le informazioni riga per riga, è possibile valutare l'andamento del training e la convergenza della rete:

- **Partizione dei campioni:** Avendo impostato `validation_split=0.1` sui 60.000 campioni originari di MNIST, Keras isola automaticamente l'ultimo 10% dei dati. Il log conferma infatti che il modello si addestra su 54.000 campioni (_training set_) e ne riserva 6.000 per la verifica (_validation set_).
    
- **Messaggi di Warning:** L'avviso iniziale di deprecazione (`WARNING:tensorflow`) segnala semplicemente che una funzione matematica interna ereditata dai backend di TensorFlow (`to_int32`) verrà sostituita nelle release successive con `tf.cast`. Non influisce sul corretto completamento del calcolo corrente.

Per ogni singola epoca (da `Epoch 1/5` a `Epoch 5/5`), Keras stampa una barra di avanzamento e un riepilogo metrico strutturato:

- **Tempo di calcolo:** Indica la durata complessiva espressa in secondi per completare l'epoca e il tempo medio richiesto per elaborare un singolo campione (`1ms/sample`).
    
- **`loss` e `acc` (Metrics di Training):** Mostrano le prestazioni calcolate progressivamente sui 54.000 campioni di addestramento. Si nota un comportamento ottimale: la loss diminuisce drasticamente da `0.1424` a `0.0154`, mentre l'accuratezza di training sale da `0.956` ($95.6\%$) fino a `0.994` ($99.4\%$).
    
- **`val_loss` e `val_acc` (Metrics di Validazione):** Rappresentano l'indice fondamentale per verificare la capacità di generalizzazione della rete su dati non utilizzati per l'ottimizzazione dei pesi. Alla quinta epoca, il modello tocca un'accuratezza di validazione pari a `0.9903` ($99.03\%$).
    

Al termine del ciclo, il metodo restituisce un oggetto memorizzato in memoria (`<tensorflow.python.keras.callbacks.History>`). Questo oggetto contiene un dizionario interno (`History.history`) che traccia i valori numerici di tutte queste metriche per ogni epoca, permettendo di generare grafici successivi per ispezionare visivamente le curve di apprendimento.

![[Pasted image 20260522181122.png]]
Python

```
loss, accuracy = cnn.evaluate(X_test, y_test)
```

La valutazione finale del modello su dati completamente sconosciuti viene eseguita invocando il metodo evaluate(). Questa funzione accetta il set di test isolato originariamente (X_test, y_test) per calcolare le prestazioni effettive della rete neurale senza aggiornare i pesi.

L'output dell'esecuzione mostra i seguenti risultati metrici:

- **Analisi dei campioni:** Il metodo elabora in un unico passaggio tutti i 10.000 campioni inclusi nel set di test.
    
- **Valore di Loss:** La perdita calcolata sul set di test, stampata richiamando la variabile loss, è pari a circa 0.0341.
    
- **Valore di Accuracy:** L'accuratezza finale sul set di test, stampata richiamando la variabile accuracy, si attesta a 0.9905.
    

Questo significa che, senza aver effettuato alcuna operazione di ottimizzazione fine (_tuning_) sugli iperparametri o sulla struttura, il modello basato su convnet raggiunge un'accuratezza superiore al **99%** su campioni di dati mai visti prima.

Sebbene questo risultato sia estremamente elevato, è possibile trovare online modelli e architetture ottimizzate in grado di predire il dataset MNIST con un'accuratezza ancora maggiore. Per comprendere meglio il comportamento della rete, è consigliabile sperimentare modificando il numero di livelli, introducendo tipologie differenti di layer o variando i parametri interni dei singoli blocchi per osservare direttamente in che modo tali modifiche influenzino i risultati finali.

![[Pasted image 20260522181824.png]]
![[Pasted image 20260522184343.png]]
Python

```
predictions = cnn.predict(X_test)
```

La generazione delle predizioni su un intero insieme di dati avviene invocando il metodo predict() del modello. Questo metodo esegue il passaggio in avanti (_forward pass_) dei campioni attraverso la rete, restituendo un array multidimensionale che contiene le distribuzioni di probabilità per ciascun elemento di input.

L'analisi del primo campione di test evidenzia la precisione del modello:

- **Verifica del target reale:** Interrogando il valore memorizzato in y_test[0], l'output restituisce un array in cui è presente il valore 1. esattamente all'indice 7, confermando che la prima cifra reale del dataset è un 7.
    
- **Ispezione delle probabilità predette:** Eseguendo un ciclo iterativo per stampare i singoli valori restituiti da predict per il primo campione (predictions[0]), si osserva la distribuzione percentuale per ciascuna classe da 0 a 9.
    
- **Predizione dominante:** La classe associata all'indice 7 mostra un valore di probabilità schiacciante pari al **99.9845504761%**. Tutte le altre classi presentano valori infinitesimali o prossimi allo zero (la seconda percentuale più "elevata" si riscontra all'indice 2 con appena lo $0.0129567663\%$). Questo indica che il modello ha classificato l'immagine come un 7 con un livello di confidenza pressoché assoluto.

![[Pasted image 20260522184716 1.png]]

Per comprendere quali tipologie di cifre mettono in difficoltà il modello, è utile individuare e analizzare visivamente le immagini classificate in modo errato. Ad esempio, se si nota che il modello sbaglia sistematicamente a predire il numero 8, potrebbe essere necessario integrare il dataset di addestramento con un numero maggiore di campioni rappresentanti quella specifica cifra.

Per determinare in modo programmatico se una singola predizione sia corretta o meno, si procede confrontando due valori geometrici:

- L'indice associato al valore di probabilità più elevato all'interno del vettore predictions[0].
    
- L'indice dell'elemento che contiene il valore 1.0 all'interno dell'array del target reale y_test[0].

Se questi due indici coincidono, significa che la predizione effettuata dalla rete neurale convoluzionale è corretta; in caso contrario, ci si trova di fronte a un errore di classificazione.

![[Pasted image 20260522185000.png]]
![[Pasted image 20260522185047.png]]
Python

```
images = X_test.reshape((10000, 28, 28))
incorrect_predictions = []

for i, (p, e) in enumerate(zip(predictions, y_test)):
    predicted, expected = np.argmax(p), np.argmax(e)
    
    if predicted != expected: # prediction was incorrect
        incorrect_predictions.append(
            (i, images[i], predicted, expected))

len(incorrect_predictions) # number of incorrect predictions
```

Per poter visualizzare graficamente le immagini analizzate dal modello, è necessario effettuare un'operazione di ridimensionamento geometrico dei dati. Keras richiede che i campioni di input abbiano una struttura tridimensionale definita dalla forma (28, 28, 1) per poter eseguire correttamente l'addestramento convoluzionale; tuttavia, la libreria Matplotlib necessita di una matrice bidimensionale pura con shape (28, 28) per poter renderizzare a schermo le immagini. Per questo motivo, l'intero array X_test viene rimodellato su 10.000 campioni bidimensionali.

Successivamente, viene inizializzata una lista vuota denominata incorrect_predictions per raccogliere programmaticamente tutti gli errori di classificazione attraverso un ciclo iterativo:

- All'interno dello snippet, la variabile p rappresenta l'array dei valori predetti (_predicted value array_), mentre la variabile e mappa l'array dei valori attesi reali (_expected value array_).
    
- La funzione argmax di NumPy viene utilizzata per determinare l'indice esatto corrispondente all'elemento con il valore numerico più elevato all'interno di ciascun array, estraendo così la classe finale predetta e quella attesa.
    
- Se il valore di predicted non coincide con quello di expected, la predizione viene considerata errata e i relativi dettagli (indice posizionale, matrice dell'immagine, valore predetto e valore reale) vengono aggiunti alla lista.

Invocando infine il metodo len(incorrect_predictions), l'output restituisce il numero esatto di predizioni errate compiute dal modello sull'intero set di test, che in questo caso specifico ammonta a 95 immagini su 10.000.

![[Pasted image 20260522185828.png]]
Python

```
def display_probabilities(prediction):
    for index, probability in enumerate(prediction):
        print(f'{index}: {probability:.10%}')
```

Per analizzare nel dettaglio il comportamento del modello nei casi di errore, definiamo una funzione personalizzata denominata display_probabilities. Questa funzione accetta in ingresso un singolo vettore di predizione e mostra a schermo la distribuzione percentuale di probabilità formattata per ciascuna delle dieci classi possibili, facilitando l'individuazione del grado di incertezza o dei falsi positivi della rete.

Nell'esempio pratico viene esaminato il vettore di predizioni associato all'indice 340 del set di dati (predictions[340]):

- **Distribuzione probabilistica:** L'output mostra come la probabilità principale si concentri in modo predominante sull'indice 3 con un valore pari al **95.1051056385%**.
    
- **Seconda scelta competitiva:** A differenza dei casi di corretta classificazione (in cui la confidenza sfiora il 100%), qui si rileva una percentuale secondaria non trascurabile localizzata sull'indice 5, attestata al **4.8939082772%**. Tutti gli altri indici presentano invece valori prossimi allo zero o infinitesimali.

Questa specifica distribuzione evidenzia dal punto di vista matematico l'ambiguità riscontrata dalla convnet nella classificazione del campione, suggerendo che le caratteristiche geometriche o i tratti grafici della cifra analizzata presentavano forti somiglianze strutturali tra il numero 3 e il numero 5.

![[Pasted image 20260522190415.png]]
In Keras e TensorFlow, la gestione del ciclo di vita di un'architettura neurale prevede la possibilità di esportare e ripristinare i dati di calcolo appresi:

- **Salvataggio dello stato:** È possibile salvare lo stato completo di un modello. Questa operazione archivia su disco sia la struttura topologica della rete (i livelli e le connessioni) sia l'insieme dei pesi sinaptici e dei bias ottimizzati durante l'addestramento.
    
- **Caricamento successivo:** Il modello può essere caricato in un secondo momento all'interno di un nuovo script o ambiente operativo per diversi scopi applicativi, come ad esempio:
    
    - **Generare nuove predizioni** 
    - **Continuare l'addestramento** 
    - **Addestrare su nuovi problemi** 
    - **Applicare il Transfer Learning** Consente di trasferire la conoscenza appresa da un modello pre-addestrato verso un nuovo modello target.

![[Pasted image 20260522190645.png]]
Python

```
cnn.save('mnist_cnn.h5')
```

Il salvataggio dell'architettura e dello stato completo del modello avviene invocando il metodo `save()`, il quale esporta tutti i dati rilevanti all'interno di un file basato sullo standard **Hierarchical Data Format (HDF5)**, identificabile dall'estensione `.h5`.

Python

```
from tensorflow.keras.models import load_model
cnn = load_model('mnist_cnn.h5')
```

Il ripristino di un modello precedentemente archiviato viene eseguito importando e utilizzando la funzione dedicata `load_model` dal sottomodulo dei modelli di Keras. Questa operazione ricostruisce interamente la struttura della rete e ne ricarica i pesi ottimizzati memorizzati nel file HDF5 senza dover ridefinire il codice dell'architettura.

Una volta completato il caricamento, è possibile invocare direttamente sul modello ripristinato tutti i suoi metodi nativi:

- **`predict`:** Può essere chiamato per generare ulteriori predizioni su nuovi campioni di dati.
    
- **`fit`:** Può essere chiamato per continuare l'addestramento della rete utilizzando dati aggiuntivi.
    

Oltre a queste procedure standard, l'ecosistema mette a disposizione funzioni addizionali che abilitano il salvataggio e il caricamento selettivo di vari aspetti specifici del modello (come la sola struttura topologica o i soli pesi sinaptici).

---

![[Pasted image 20260522195645.png]]
Comprendere appieno l'architettura intima e tutti i dettagli operativi delle reti neurali profonde è un compito notoriamente complesso. Questa natura intrinsecamente opaca genera sfide significative nelle fasi di testing, debugging, aggiornamento dei modelli e ottimizzazione degli algoritmi. I modelli di deep learning estraggono e apprendono un numero mastodontico di feature che, molto spesso, non risultano palesi o facilmente interpretabili a livello umano.

Per ovviare a queste limitazioni, Google mette a disposizione **TensorBoard**, uno strumento di dashboard interattivo progettato per visualizzare in modo chiaro le reti neurali implementate in TensorFlow e Keras. L'utilizzo di questa suite diagnostica offre numerosi vantaggi:

- Fornisce approfondimenti e intuizioni dettagliate su come il modello stia apprendendo nel tempo.
    
- Aiuta concretamente lo sviluppatore a calibrare e ottimizzare gli iperparametri (_hyperparameters tuning_) per massimizzare la convergenza.

![[Pasted image 20260522195748.png]]
L'esecuzione di TensorBoard si basa sul monitoraggio continuo di una cartella specifica all'interno del sistema, all'interno della quale cerca i file contenenti i dati di telemetria da mostrare graficamente.

Il workflow operativo per configurare e avviare lo strumento prevede i seguenti passaggi sequenziali:

1. **Spostarsi nella cartella corretta:** Accedere alla directory di progetto `ch16` tramite il terminale.
    
2. **Attivare l'ambiente virtuale:** Assicurarsi che l'ambiente personalizzato Anaconda denominato `tf_env` sia attivo eseguendo il comando:
    
    Bash
    
    ```
    conda activate tf_env
    ```
    

```
3. **Creare la directory dei log:** Generare una sottocartella denominata `logs`, che servirà come destinazione in cui i modelli di deep learning scriveranno tutte le informazioni necessarie alla visualizzazione.
4. **Avviare TensorBoard:** Eseguire l'applicazione indicando la cartella dei log appena creata tramite il comando:
   ```bash
   tensorboard --logdir=logs
```

5. **Accedere all'interfaccia web:** Aprire il proprio browser web e digitare l'indirizzo `http://localhost:6006` per interagire con la dashboard.
    

> [!NOTE]
> 
> Se ci si connette a TensorBoard prima di aver effettivamente eseguito un modello, l'interfaccia mostrerà inizialmente una pagina vuota contenente l'avviso: _"No dashboards are active for the current data set."_.

![[Pasted image 20260522195945 1.png]]

Non appena TensorBoard rileva nuovi aggiornamenti o file inediti scritti all'interno della cartella `logs`, provvede a caricare dinamicamente i dati in tempo reale all'interno della sua dashboard web, dove possiamo dei grafici che ci mostrano come variano alcuni parametri in base alle epoche.

![[Pasted image 20260522200226.png]]

Vediamo adesso come usare **TensorBoard** per monitorare e comprendere l'addestramento delle reti neurali.

- **Complessità delle reti profonde:** È difficile conoscere e comprendere appieno tutti i dettagli interni delle reti di deep learning.
    
- **Sfide di sviluppo:** Questa mancanza di trasparenza crea difficoltà quando si tratta di testare, fare debugging e aggiornare i modelli o gli algoritmi.
    
- **Estrazione delle feature:** I modelli di deep learning apprendono una quantità enorme di caratteristiche (_features_), molte delle quali potrebbero non essere evidenti o interpretabili direttamente dall'utente.
    
- **Lo strumento TensorBoard:** Viene introdotto TensorBoard, lo strumento di dashboard di Google progettato specificamente per visualizzare le reti neurali implementate in TensorFlow e Keras.
    
- **Ottimizzazione:** Utilizzare questa interfaccia permette di ottenere informazioni approfondite su come il modello sta apprendendo e aiuta a calibrare i suoi iperparametri (_tune its hyperparameters_).

TensorBoard funziona monitorando una cartella specifica sul sistema alla ricerca di file di dati da visualizzare. I primi passaggi operativi indicati sono:

1. **Spostarsi nella cartella di progetto:** Modificare la directory corrente nel terminale spostandosi nella cartella `ch16`.
    
2. **Attivazione dell'ambiente:** Assicurarsi che l'ambiente personalizzato di Anaconda (chiamato `tf_env`) sia attivato tramite il comando:
   Bash
   ```   conda activate tf_env    ```

