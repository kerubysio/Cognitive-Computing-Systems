MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 26/05/2026
FONTE: 

---

# ConvnetJS

**ConvnetJS permette di addestrare e visualizzare Reti Neurali Convoluzionali (CNN) direttamente all'interno del web browser**. 

Il grande vantaggio di questo strumento è la sua natura interattiva e visiva: non si tratta solo di scrivere codice, ma di "guardare" letteralmente dentro la scatola nera di un'intelligenza artificiale mentre sta imparando.

### La Demo su MNIST e l'Interfaccia

Il file si concentra specificamente su una dimostrazione pratica inclusa in ConvnetJS: **il training di una CNN sul celebre dataset MNIST** (una vasta raccolta di immagini di cifre scritte a mano, dallo 0 al 9).

Quando avvii la demo, ti trovi di fronte a una dashboard scorrevole che si aggiorna in tempo reale. Questa plancia di comando è divisa in diverse sezioni fondamentali per comprendere come il modello sta "ragionando":

#### 1. Training Stats (Statistiche di Addestramento)

Questa è la centralina di controllo del processo. Mentre la rete elabora i dati, vedrai dei grafici e dei numeri che cambiano continuamente.

In particolare, vengono mostrate:

- L'**accuratezza di training** (quanto il modello è bravo a riconoscere le immagini su cui sta studiando) e l'**accuratezza di validazione** (quanto è bravo a riconoscere immagini nuove che non ha mai visto).
    
- Un **grafico della funzione di perdita (_training loss_)**, che indica il margine di errore del modello (più scende, meglio è).
    

Una funzione utilissima qui è il tasto **Pause**. Cliccandolo, l'addestramento si blocca ("congela"), dandoti tutto il tempo necessario per ispezionare lo stato attuale delle visualizzazioni prima di riprendere con il tasto **Resume**.

#### 2. Instantiate a Network and Trainer (Creazione della Rete)

Mostra una casella di testo contenente il vero e proprio codice JavaScript utilizzato per definire l'architettura della rete (cioè quanti strati ha, di che tipo sono, ecc.). L'architettura predefinita è molto simile alle CNN base che si studiano solitamente.

La cosa interessante è che **puoi modificare questo codice al volo**. Puoi aggiungere un livello, rimuoverne un altro, cambiare i parametri (consultando la documentazione di ConvnetJS per vedere i tipi di layer supportati) e poi cliccare su **"Change network"**. Il modello verrà ricreato all'istante e ricomincerà ad addestrarsi con la tua nuova architettura personalizzata.

#### 3. Network Visualization (Esplorare la mente della Rete)

Mettendo in pausa il sistema, puoi letteralmente vedere **come ogni livello trasforma l'immagine**, evidenziando bordi, curve o pattern specifici. È un modo perfetto per capire cosa la rete stia "guardando" per prendere la sua decisione.

#### 4. Example Predictions on Test Set (Verifica delle Predizioni)

In questa area, il sistema testa continuamente il modello su una **selezione casuale di immagini prelevate dal set di test** (quindi immagini non usate per l'addestramento).

Per ogni immagine testata, ConvnetJS ti dice quali sono le **tre classi (cifre) che il modello ritiene più probabili**. L'ipotesi con la probabilità più alta viene evidenziata con una **barra verde**, e la lunghezza di questa barra è una rappresentazione visiva immediata di quanto il modello sia sicuro della sua risposta (il suo grado di confidenza).

---

# Recurrent Neural Network

### Reti Neurali Ricorrenti e Analisi del Sentimento

Questo modulo introduce le **Reti Neurali Ricorrenti (RNN)**, una classe speciale di reti neurali progettata per lavorare con dati di natura sequenziale. Invece di analizzare un'immagine statica (come fanno le CNN), le RNN elaborano informazioni che si sviluppano nel tempo o nello spazio, come le serie temporali finanziarie, o le parole che formano una frase.

#### Il Concetto di "Ricorrenza"

Ciò che rende queste reti "ricorrenti" è la presenza di **cicli (loop)** nella loro architettura. In una rete neurale tradizionale, l'informazione viaggia in una sola direzione: dall'input verso l'output. In una RNN, invece, l'output generato da un livello in un determinato istante (chiamato _time step_) viene "reiniettato" nello stesso livello come input per il time step successivo.

Nel caso di un testo, il _time step_ rappresenta l'elaborazione della parola successiva nella frase. Grazie a questi cicli, la rete è in grado di **ricordare e apprendere le relazioni** tra i dati che compongono la sequenza.

Questa capacità di memoria è cruciale per comprendere il linguaggio umano. Ad esempio, la parola "buono" ha un sentimento positivo, ma se preceduta da "non" ("non buono"), il significato si inverte. Mentre in questo esempio le parole sono vicine, in frasi complesse il significato può dipendere da molte parole separate da un numero arbitrario di altri termini. Le RNN tengono conto di queste relazioni a distanza.

Per gestire questo processo in modo ottimizzato, si fa spesso uso di un livello specifico chiamato **Long Short-Term Memory (LSTM)**. Le architetture LSTM/RNN sono ampiamente utilizzate in scenari concreti come:

- La digitazione predittiva (suggerire la parola successiva sullo smartphone)
    
- La traduzione automatica tra lingue diverse
    
- Il riconoscimento vocale e la generazione di sottotitoli
    
- L'**analisi del sentimento (Sentiment Analysis)**

#### Il Dataset IMDb

Il caso di studio affrontato utilizza il dataset di IMDb, un database di recensioni cinematografiche. L'obiettivo è eseguire una **classificazione binaria** per determinare se una recensione esprime un parere **positivo** o **negativo**.

#### Codifica e Preparazione dei Dati

Keras gestisce i modelli richiedendo input numerici. Per questo motivo, le recensioni del dataset IMDb non si presentano come testo leggibile, ma sono già pre-elaborate e **codificate numericamente** (appaiono come array monodimensionali di interi).

- **Ranking:** Ogni numero rappresenta la frequenza di quella parola nell'intero dataset (il valore "1" è la parola più usata, il "2" la seconda e così via).
    
- **Offset:** I valori di ranking subiscono uno spostamento (offset) di 3 posizioni all'interno degli array dei campioni.
    
- **Valori Riservati:** I numeri 0, 1 e 2 hanno significati speciali. Lo `0` è usato per il _padding_ (riempimento), l'`1` segnala l'inizio di una sequenza, e il `2` rappresenta una parola "sconosciuta" (cioè una di quelle che non rientra tra le prime 10.000 scelte o non presente).

Per decodificare i numeri e visualizzare la recensione testuale originale, è possibile sfruttare il dizionario di mappatura fornito da Keras.

#### Creazione del Modello Keras (Sequential)

L'architettura utilizzata per questo problema è piuttosto snella, composta da tre strati principali:

1. **Livello Embedding (`Embedding`):** Rappresentare 10.000 parole usando la tecnica "one-hot encoding" creerebbe matrici immense (da decine di milioni di zeri), del tutto inefficienti. Il layer di embedding risolve questo problema trasformando ogni parola in un vettore compatto e denso. Questo approccio non solo riduce le dimensioni, ma cattura il contesto e le relazioni semantiche tra le parole.
    
2. **Livello LSTM (`LSTM`):** È il cuore pulsante della rete ricorrente. Si compone di 128 neuroni ("units"). Per prevenire la "memorizzazione" mnemonica dei dati e favorire una vera generalizzazione (evitando l'overfitting), vengono impostati due parametri cruciali: `dropout=0.2` (disabilita casualmente il 20% dei neuroni nell'input/output del layer) e `recurrent_dropout=0.2` (disabilita casualmente il 20% dei neuroni nel momento in cui l'output viene reinserito come input per il passaggio successivo).
    
3. **Livello Denso di Output (`Dense`):** Essendo una classificazione binaria (positivo o negativo), in output è sufficiente un solo neurone (`units=1`). Viene utilizzata la funzione di attivazione **`sigmoid`**, che ha il pregio di schiacciare qualsiasi valore producendo in uscita una comoda probabilità compresa tra 0.0 e 1.0.

Dopo l'addestramento, la funzione `evaluate` permette di testare la rete calcolandone l'accuratezza finale.

---
# Tuning dei Modelli di Deep Learning

Quando si addestra un modello di Machine Learning o Deep Learning, raramente i primi risultati sono quelli definitivi. Un segnale d'allarme tipico è notare che **l'accuratezza sui dati di validazione e di test è significativamente inferiore rispetto all'accuratezza misurata sui dati di addestramento**.

#### Le Variabili Cruciali per le Prestazioni

Modificare le performance di un modello significa giocare con la sua architettura e con i dati che lo alimentano. Tra i fattori principali che influenzano i risultati troviamo:

- La **quantità di dati** utilizzata (suddivisa per training, test e validazione).
    
- La **profondità della rete**, ovvero avere più o meno layer.
    
- Le **tipologie di layer** scelti e l'**ordine** in cui sono concatenati.

#### Quali aspetti possiamo "accordare"?

Per migliorare l'accuratezza e limitare l'overfitting, è possibile intervenire in modo pratico su diversi aspetti dell'architettura e del dataset:

- **Volume dei dati di training:** Nel nostro esempio sull'analisi del sentimento (IMDb), abbiamo considerato solo le 10.000 parole più frequenti. Aumentare questo numero offre alla rete un vocabolario più ricco da cui apprendere
    
- **Lunghezza delle sequenze:** Abbiamo troncato ogni recensione a 200 parole. Allungare o accorciare questo parametro può cambiare drasticamente ciò che il modello percepisce come "contesto"
    
- **Densità della rete:** Si può sperimentare cambiando il **numero di neuroni** all'interno di specifici layer oppure aggiungendo del tutto **nuovi layer** per aumentare la complessità e la capacità di apprendimento del modello
    
- **Utilizzo di risorse esterne:** Invece di far imparare alla rete le relazioni semantiche delle parole da zero, è estremamente vantaggioso **caricare vettori di parole (word embeddings) pre-addestrati**

#### Le Sfide Computazionali e l'AutoML

Ottimizzare i modelli manualmente, modificando uno per volta i parametri citati, richiede di avviare l'addestramento più e più volte. Nel Deep Learning, tuttavia, i **tempi di calcolo necessari sono enormi**. A causa di questo costo computazionale, approcci tradizionali come la **K-Fold Cross-Validation** (molto usati nel Machine Learning classico) vengono raramente utilizzati nel Deep Learning per trovare gli iperparametri ideali.

Per superare questa barriera, l'industria si sta muovendo verso soluzioni di **Automated Machine Learning (AutoML)**. Questi sistemi eseguono la ricerca e l'ottimizzazione in modo automatizzato. Strumenti notevoli in questo campo sono:

- **Auto-Keras**, che è specificamente progettato per cercare in automatico le migliori configurazioni architetturali per i modelli Keras.
    
- Piattaforme enterprise come **Google Cloud AutoML** e **Baidu EZDL**, che mettono a disposizione enormi risorse cloud per l'addestramento e il tuning automatizzato.