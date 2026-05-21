MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 15/05/2026
FONTE: 

--- 

![[Pasted image 20260521170744 1.png]]
![[Pasted image 20260521170854 1.png]]
Usi del Deep Learning.

![[Pasted image 20260521170912 1.png]]
Alcune demo di Machine Learning.

![[Pasted image 20260521170937.png]]
Per lo studio e lo sviluppo di progetti nell'ambito del deep learning, l'ecosistema mette a disposizione diverse risorse chiave:

- **Supporto della community:** Il canale Slack ufficiale del team di Keras.
    
- **Materiale didattico e documentazione:** La documentazione ufficiale di Keras, insieme ad articoli e tutorial dedicati.
    
- **Ricerca scientifica avanzata (arXiv):** Per lo sviluppo di progetti accademici, tesi di laurea o capstone projects, la piattaforma di riferimento è **arXiv**.

---
![[Pasted image 20260521171313.png]]
La gestione dei requisiti software per l'utilizzo della versione di Keras integrata in TensorFlow richiede spesso la configurazione di ambienti virtuali dedicati, specialmente in presenza di vincoli di compatibilità con l'interprete Python (come nel caso storico analizzato, in cui TensorFlow richiedeva la release `3.6.x` a causa della mancanza di supporto iniziale per la `3.7`).

La distribuzione Anaconda Python semplifica questo processo attraverso la gestione di configurazioni isolate:

- **Isolamento delle dipendenze:** Gli ambienti personalizzati (_custom environments_) permettono di separare configurazioni logiche differenti, all'interno delle quali installare librerie specifiche o versioni differenti dei medesimi pacchetti, senza generare conflitti globali sul sistema operativo.
    
- **Riproducibilità del codice:** Questo approccio garantisce che gli script e i modelli rimangano riproducibili nel tempo, bloccando le versioni esatte dei pacchetti da cui il software dipende.
    
- **Ambiente Base (`base environment`):** È l'ambiente di default generato automaticamente durante l'installazione di Anaconda. Include pre-installate tutte le librerie standard della suite. Qualsiasi ulteriore pacchetto installato senza specificare un target differente viene posizionato qui per impostazione predefinita.
    
- **Controllo granulare:** La creazione di un ambiente custom sottrae lo sviluppatore dai vincoli del pacchetto globale, fornendo il pieno controllo sulle versioni e sulle dipendenze necessarie per completare task specifici.

![[Pasted image 20260521171545.png]]
Qui manca la parte grafica dello snippet che mostra il comando completo.

Bash

```
conda create -n tf_env python=3.6 anaconda tensorflow ipython jupyterlab scikit-learn matplotlib seaborn h5py pydot graphviz nodejs
```

La creazione di un ambiente virtuale isolato in Anaconda viene eseguita tramite il comando di terminale `conda create`. In questo caso specifico, l'istruzione configura un ambiente ottimizzato per il deep learning denominato `tf_env` (il nome può essere personalizzato a piacimento tramite il flag `-n`).

L'istruzione esegue in un unico blocco le seguenti operazioni:

- Specifica la versione esatta dell'interprete inserendo `python=3.6` per garantire la compatibilità con la release di TensorFlow utilizzata.
    
- Installa l'intero meta-pacchetto `anaconda`, che include le librerie matematiche e scientifiche di base.
    
- Scarica e configura la libreria `tensorflow` insieme al core di strumenti per lo sviluppo e la visualizzazione (`ipython`, `jupyterlab`, `scikit-learn`, `matplotlib`, `seaborn`).
    
- Introduce dipendenze ausiliarie specifiche per il deep learning come `h5py` (per il salvataggio dei modelli in formato HDF5), `pydot` e `graphviz` (per la renderizzazione grafica delle architetture delle reti neurali).
    

Se il sistema ospitante è dotato di una GPU NVIDIA dedicata e compatibile, per massimizzare le prestazioni computazionali e abilitare l'accelerazione hardware è necessario sostituire l'argomento generico `tensorflow` con il pacchetto specifico `tensorflow-gpu`.

![[Pasted image 20260521171739.png]]
Attivare e disattivare l'ambiente anaconda personalizzato tramite linea di comando.

Per farlo tramite app, apri Anaconda Navigator $\rightarrow$ Environments (menù a sinistra) $\rightarrow$ Create (in basso).
Una volta creato, posso attivarlo cliccandoci sopra. Dopodichè posso installare i pacchetti necessari come Keras, Tensorflow, Seaborn, pydot e matplotlib.

Puoi trovare degli esempi nella cartella examples di ch15.

---
Riassunto veloce sulle reti neurali.

![[Pasted image 20260521172411.png]]

![[Pasted image 20260521172517.png]]

![[Pasted image 20260521172547.png]]

![[Pasted image 20260521172626.png]]

![[Pasted image 20260521172647.png]]

![[Pasted image 20260521172707.png]]

![[Pasted image 20260521172727.png]]

---

Ora parliamo di tensori.

![[Pasted image 20260521172845.png]]
![[Pasted image 20260521173144.png]]
I framework di deep learning manipolano i dati strutturandoli sotto forma di **tensori**, che rappresentano essenzialmente degli array multidimensionali. Sistemi come TensorFlow impacchettano l'intero set informativo all'interno di uno o più tensori, utilizzandoli per eseguire in modo parallelo i calcoli matematici che permettono alle reti neurali di apprendere. A causa della mole di dati elaborati, queste strutture vettoriali possono raggiungere dimensioni estremamente elevate.

Si distinguono diverse tipologie di tensori in base al loro grado di dimensionalità (n-D):

- **Tensore 0D (Zero-Dimensionale):** Contiene un singolo valore numerico isolato ed è formalmente noto come **scalare**.
    
- **Tensore 1D (Mono-Dimensionale):** Corrisponde alla struttura logica di un array a una dimensione ed è noto come **vettore**. Viene tipicamente impiegato per rappresentare sequenze di dati sequenziali ordinati, come le rilevazioni orarie della temperatura provenienti da un sensore o la codifica dei token di testo che compongono una recensione cinematografica.
    
- **Tensore 2D (Bi-Dimensionale):** Corrisponde alla struttura di un array a due dimensioni ed è noto come **matrice**. Un esempio classico di utilizzo è la rappresentazione di un'immagine in scala di grigi: le due dimensioni della matrice mappano rispettivamente la larghezza e l'altezza dell'immagine in pixel, mentre il valore numerico memorizzato in ciascun elemento definisce l'intensità di grigio di quel determinato punto.

- **Tensore 3D (Tri-Dimensionale):** Corrisponde alla struttura logica di un array a tre dimensioni. Trova applicazione in due scenari tipici:
    
    - **Immagini a colori:** Le prime due dimensioni mappano la larghezza e l'altezza dell'immagine in pixel, mentre la terza dimensione (la profondità) descrive i canali cromatici RGB (rosso, verde e blu) necessari a comporre il colore di ciascun pixel.
        
    - **Raccolte di immagini in scala di grigi:** Può essere strutturato come un'insieme ordinato di tensori 2D, in cui ogni livello della terza dimensione rappresenta un singolo fotogramma indipendente in bianco e nero.
        
- **Tensore 4D (Tetra-Dimensionale):** Aggiunge un ulteriore livello di astrazione e viene utilizzato principalmente per descrivere:
    
    - **Batch di immagini a colori:** Una collezione di più tensori 3D, utile per passare interi gruppi di immagini simultaneamente alla rete neurale durante il training.
        
    - **Singoli file video:** Poiché un video è composto da una sequenza temporale di fotogrammi, le quattro dimensioni mappano rispettivamente: il numero del frame (tempo), la larghezza, l'altezza e il canale di colore RGB del pixel.
        
- **Tensore 5D (Penta-Dimensionale):** Rappresenta una struttura ad altissima dimensionalità, impiegata tipicamente per memorizzare **raccolte di video (batch di video)**. In questa configurazione, la quinta dimensione funge da indice per distinguere i diversi filmati all'interno dello stesso blocco di dati, dove ciascun elemento referenziato è a sua volta un tensore 4D completo.

![[Pasted image 20260521173317.png]]
Per comprendere la scalabilità e le dimensioni dei dati gestiti dal deep learning, si può analizzare il caso di studio di una rete neurale progettata per l'identificazione e il tracciamento di oggetti all'interno di flussi video ad altissima risoluzione.

- **Modellazione di un singolo fotogramma 4K:**
    - Un video in formato 4K presenta una risoluzione spaziale di $3840 \times 2160$ pixel. Considerando la codifica cromatica standard basata sui tre canali RGB (rosso, verde, blu), ogni singolo frame viene mappato come un **tensore 3D**. Il numero di elementi quantitativi (valori di intensità dei pixel) contenuti in un solo fotogramma è pari a: $3840 \times 2160 \times 3 = 24.883.200 \text{ elementi}$
        
- **Modellazione su asse temporale (Singolo Video):**
    - Configurando la frequenza di campionamento a 30 fotogrammi al secondo (fps), la sequenza temporale dei frame richiede l'estensione delle coordinate in un **tensore 4D**. Per un filmato della durata di appena un minuto (60 secondi), il volume di dati complessivo per quel singolo tensore sale a: $24.883.200 \times 30 \times 60 = 44.789.760.000 \text{ elementi}$
        
- **Impatto sui Big Data (Scala YouTube):**
    - Estendendo la metrica su piattaforme su larga scala come YouTube, in cui vengono caricate oltre 600 ore di video ogni minuto, l'insieme informativo combinato per l'addestramento dei modelli si traduce potenzialmente in un unico macro-tensore da oltre 1,6 mila miliardi di elementi.

Questa crescita esponenziale della dimensionalità evidenzia come i tensori diventino rapidamente enormi. La necessità critica di manipolare e computare queste strutture in modo efficiente costituisce la ragione principale per cui il deep learning moderno viene eseguito in modo quasi esclusivo su architetture hardware parallele massive come le GPU e le TPU, espressamente ingegnerizzate per l'algebra tensoriale.

![[Pasted image 20260521173737.png]]
L'applicazione del deep learning in scenari di produzione reali richiede l'utilizzo di processori ad altissime prestazioni. Questo vincolo hardware è dettato dalle dimensioni mastodontiche dei tensori: l'esecuzione di operazioni algebriche su macro-matrici genera un carico computazionale critico che manderebbe in saturazione le architetture di calcolo sequenziali tradizionali.

Un'innovazione chiave in questo settore è rappresentata dai **Tensor Core** della microarchitettura **Volta** (e successive) di NVIDIA. Si tratta di unità di calcolo dedicate che eseguono operazioni di moltiplicazione e accumulo di matrici in un solo ciclo di clock.

![[Pasted image 20260521174011.png]]
Il database **MNIST** è una raccolta storica e standardizzata di cifre numeriche scritte a mano, utilizzata come benchmark fondamentale per validare i modelli di classificazione multiclasse.
Per l'elaborazione di questo dataset si ricorre alla creazione di una **Rete Neurale Convoluzionale** (nota anche come **convnet** o **CNN**), un'architettura profonda specializzata nell'estrazione di feature spaziali e geometriche dai dati.

Le CNN trovano il loro impiego primario e naturale all'interno di applicazioni di **computer vision**:

- Riconoscimento automatico di caratteri alfanumerici e cifre scritte a mano.
    
- Rilevamento e tracciamento di oggetti complessi all'interno di flussi di immagini e video.
    
- Sistemi di percezione visiva per la guida autonoma (_self-driving cars_).
    

Oltre ai task strettamente legati alla visione artificiale, è possibile anche l'applicazione anche in domini **non-vision**, come:

- **Natural Language Processing (NLP):** Utilizzando convoluzioni 1D sui vettori di embedding per l'analisi del testo o la classificazione dei sentimenti.
    
- **Sistemi di raccomandazione (_recommender systems_):** Per modellare le interazioni sequenziali degli utenti nel tempo.

![[Pasted image 20260521174244 1.png]]
Il dataset MNIST è strutturato in due partizioni principali fornite di etichette reali: un set di training da 60.000 campioni per l'addestramento della rete e un set di test da 10.000 campioni per valutarne le capacità di generalizzazione su dati mai visti.

Le caratteristiche tecniche e geometriche dei dati sono così definite:

- **Risoluzione spaziale:** Ogni immagine rappresenta una cifra scritta a mano digitalizzata in una griglia bidimensionale da $28 \times 28$ pixel. All'interno del codice, i campioni vengono manipolati tramite strutture NumPy array.
    
- **Profondità di colore:** Le immagini sono memorizzate nativamente in scala di grigi (_grayscale_). L'intensità luminosa di ciascun pixel è codificata a 8 bit, esprimendo un valore intero compreso nell'intervallo `0-255` (dove 0 indica l'assenza totale di colore, ovvero il nero dello sfondo, e 255 rappresenta la massima intensità, ovvero il bianco del tratto scritto).

La rete neurale convoluzionale affronta il problema eseguendo una classificazione probabilistica multiclasse:

- **Output probabilistico:** Lo strato finale della rete restituisce un vettore composto da 10 valori di probabilità distinti. Ciascun valore quantifica la verosimiglianza che l'immagine in ingresso appartenga a una delle dieci classi numeriche possibili, comprese nell'intervallo `0-9`.
    
- **Criterio di predizione:** La classe predetta dal modello corrisponde in modo deterministico all'indice associato al valore di probabilità più elevato all'interno del vettore di output (operazione di _arg max_).

![[Pasted image 20260521174735.png]]
La riproducibilità dei risultati rappresenta una delle sfide principali nel deep learning ed è un obiettivo intrinsecamente difficile da raggiungere. Questa complessità non dipende da errori nel codice, ma dall'architettura hardware e software su cui poggiano i framework come Keras e TensorFlow.

Le librerie di deep learning parallelizzano in modo massivo i calcoli in virgola mobile (_floating-point_) per poter sfruttare la potenza di calcolo di GPU e CPU multicore. A causa di questa parallelizzazione estrema e della gestione asincrona dei thread, l'ordine esatto in cui i calcoli matematici vengono eseguiti può variare da un'esecuzione all'altra.

Poiché le operazioni sui numeri in virgola mobile non godono della proprietà associativa in senso stretto a livello hardware (l'ordine degli addendi o delle approssimazioni di arrotondamento influisce sul risultato finale infinitesimale), piccoli cambiamenti nell'ordine di esecuzione accumulano differenze che possono portare a pesi finali della rete leggermente diversi. Di conseguenza, avvii successivi dello stesso identico script sullo stesso hardware possono produrre metriche di accuratezza o valori di loss differenti. Per mitigare questo comportamento e bloccare i generatori di numeri pseudocasuali, è necessario seguire configurazioni specifiche descritte nella documentazione ufficiale (Keras FAQ on reproducibility).

![[Pasted image 20260521174920.png]]
La costruzione di una rete neurale in Keras si basa sulla configurazione e sull'interazione di tre componenti logiche fondamentali che governano la struttura, la valutazione e l'ottimizzazione del modello.

- **Network (o Modello):** È una sequenza lineare o topologica di livelli (_layers_), ciascuno dei quali contiene un insieme di neuroni artificiali deputati all'estrazione delle caratteristiche dai campioni di input. I neuroni di ogni livello ricevono i segnali dallo strato precedente, li combinano linearmente tramite pesi e bias, e li elaborano attraverso una **funzione di attivazione** (es. ReLU, Sigmoide) per produrre l'output da trasmettere al livello successivo. Maggiore è il numero di livelli impilati (_stacked_), più la rete diventa profonda, dando origine al termine _deep learning_.
    
- **Loss function (Funzione di Perdita):** È la metrica matematica che quantifica lo scostamento tra le predizioni generate dalla rete e i valori target reali del dataset. Fornisce una misura oggettiva di quanto il modello stia performando male: valori di loss inferiori indicano una convergenza corretta e predizioni più accurate.
    
- **Optimizer (Ottimizzatore):** È l'algoritmo (es. SGD, Adam) incaricato di aggiornare iterativamente i pesi sinaptici e i bias della rete basandosi sul gradiente della loss function (tramite il processo di _backpropagation_). Il suo obiettivo primario è minimizzare il valore prodotto dalla loss function per calibrare la rete e massimizzarne la capacità predittiva.
    

Per avviare lo sviluppo pratico su JupyterLab, il workflow richiede l'attivazione preliminare del terminale all'interno dell'ambiente Anaconda configurato (`conda activate tf_env`) e il successivo lancio del server di Jupyter posizionandosi nella cartella contenente i file dei notebook di esempio.

![[Pasted image 20260521175353.png]]
Python

```
from tensorflow.keras.datasets import mnist

(X_train, y_train), (X_test, y_test) = mnist.load_data()
```

L'importazione del modulo e il caricamento dei dati avvengono tramite le API integrate di Keras. L'errore di runtime `ModuleNotFoundError: No module named 'tensorflow'` mostrato nel tracciato indica che lo script è stato eseguito all'interno di un ambiente Python in cui TensorFlow non risulta installato (ad esempio, l'ambiente globale o l'ambiente base di Anaconda), evidenziando la necessità di attivare preventivamente l'ambiente virtuale dedicato (`conda activate tf_env`) prima di lanciare JupyterLab.

Una volta risolta la dipendenza dell'ambiente, l'invocazione della funzione `mnist.load_data()` esegue automaticamente il download del dataset dai server remoti (se non è già presente nella cache locale dell'utente) e ne gestisce il parsing strutturale.

La funzione restituisce una tupla nidificata di quattro NumPy array, spacchettata direttamente in due macro-gruppi:

- **Set di addestramento (`X_train`, `y_train`):** Contiene i 60.000 tensori bidimensionali delle immagini e i relativi target numerici associati.
    
- **Set di test (`X_test`, `y_test`):** Contiene i 10.000 campioni isolati necessari per la successiva validazione delle metriche di generalizzazione della rete.