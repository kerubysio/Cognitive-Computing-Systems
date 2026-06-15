# Setup iniziale

Ho iniziato creando un ambiente virtuale python e scaricando le librerie:

> pip install pandas numpy scikit-learn jupyter

Poi ho creato il progetto su vscode.

Il database utilizzato presenta queste cartelle:

-  pcap files: Contengono i pacchetti di rete "crudi", esattamente come viaggiavano nei cavi. Sono file pesantissimi e inadatti a essere letti subito da 
un'intelligenza artificiale.
	
- Argus Files e BRO Files: Sono i file crudi di rete già elaborati attraverso dei software specifici (Argus e Bro). Per il nostro scopo possiamo ignorarli.
	
- Reports: Sono documenti descrittivi che illustrano come era configurata la rete e quali specifiche azioni hanno compiuto le varie tipologie di attacco durante 
la simulazione.
	
- CSV Files: Il PDF stesso sottolinea che questi sono i file preparati appositamente per gli scopi di classificazione. Quindi useremo questi.

## Analisi dei dati

In CSV files ho trovato quelli di training e testing.
Per prima cosa importo questi file in vscode e ne verifico la struttura usando pandas.

```
import pandas as pd

df = pd.read_csv('dataset/UNSW_NB15_training-set.csv')

print(f"Il dataset contiene {df.shape[0]} righe e {df.shape[1]} colonne.")

df.head() # stampo le prime 5 righe
```

dopodichè controllo la qualità del dataset:

```
print("--- CONTROLLO VALORI MANCANTI ---")
print(df.isnull().sum().sum()) # Conto le celle vuote totali

print("\n--- TIPO DI DATI NELLE COLONNE ---")
print(df.dtypes.value_counts()) # stampo il numero di colonne numeriche e il numero di
                                # di colonne di testo


print("\n--- CONTEGGIO ATTACCHI (label 1) VS NORMALE (label 0) ---")
print(df['label'].value_counts()) # 0 per il traffico normale e 1 per gli attacchi

-- OUTPUT --

--- CONTROLLO VALORI MANCANTI ---
0

--- TIPO DI DATI NELLE COLONNE ---
int64      30
float64    11
str         4
Name: count, dtype: int64

--- CONTEGGIO ATTACCHI VS NORMALE ---
label
1    119341
0     56000
Name: count, dtype: int64
```

Primo passo: sistemare i dati. I modelli non lavorano bene con le stringhe, dobbiamo effettuare una fase di encoding.
Noto un punto critico: ci sono più del doppio degli attacchi rispetto al traffico normale. Questa cosa non corrsiponde al mondo reale, ma è un artifatto 
che nasce con scopi accademici.

L'assunto fondamentale dell'Anomaly Detection è questo: Il modello deve imparare cosa è normale, per poter poi riconoscere ciò che non lo è.
Per fare questo, un Autoencoder deve essere addestrato esclusivamente sul traffico "Normale" (Label 0).

Ecco il nostro problema ingegneristico: il file che hai appena caricato (training-set.csv) è un mix di attacchi e traffico normale. Se diamo in pasto
all'Autoencoder tutto questo file, lui imparerà che anche gli attacchi sono "normali", diventando completamente inutile come sistema di sicurezza.

## La Soluzione: Separare i Dati (Split Logico)

Per procedere correttamente secondo i principi del Machine Learning applicato alla Cyber Security, dobbiamo alterare la struttura che ci è stata fornita.

Invece di pensare in termini di training-set e testing-set, noi uniremo tutto in un grande "calderone", e poi applicheremo questo flusso logico:

- Estrazione Normale: Prenderemo tutti (e solo) i record con Label = 0 e li useremo per addestrare i nostri modelli (Autoencoder e Isolation Forest). Questo sarà il nostro vero Training Set.

- Creazione Test Sbilanciato: Prenderemo una porzione di traffico normale (non vista durante l'addestramento) e ci butteremo in mezzo gli attacchi (Label = 1). Questo set misto lo useremo per simulare la realtà e testare se il modello "suona l'allarme" (lo Scoring e il Threat Alerting di Fase 3 e 4).

```
df_test = pd.read_csv('dataset/UNSW_NB15_testing-set.csv') # carico il file di test

df_full = pd.concat([df, df_test], ignore_index=True) # unisco i due file ed elimino i
													  # numeri di riga per evitare doppioni

print("--- FUSIONE COMPLETATA ---")

print(f"Ora abbiamo un'unica mega-tabella con {df_full.shape[0]} righe e {df_full.shape[1]} colonne.")

colonne_testo = df_full.select_dtypes(include=['str']).columns # cerchiamo le colonne
														       # di testo

print("\n--- LE COLONNE DI TESTO ---")

print(list(colonne_testo))

```

Queste sono le nostre 4 colonne di testo. Traduciamo cosa significano nel contesto delle reti informatiche:

1. **proto (Protocol):** Il protocollo di comunicazione (es. TCP, UDP, ARP).
    
2. **service:** Il servizio applicativo richiesto (es. HTTP per il web, FTP per i file, DNS).
    
3. **state:** Lo stato della connessione (es. connessione avviata, conclusa, interrotta).
    
4. **attack_cat (Attack Category):** La categoria dell'attacco (es. "DoS", "Fuzzers", "Worms", oppure "Normal" se non è un attacco).

Allenare la rete con la quarta colonna sarebbe inutile, le staremmo già fornendo la soluzione.

## Manipolazione tabella

Abbiamo due colonne a "bassa cardinalità" (`service` con 13 e `state` con 11) e una colonna ad "alta cardinalità" (`proto` con ben 133 valori diversi).

Dobbiamo tradurre queste parole in numeri. L'istinto di un programmatore alle prime armi potrebbe suggerire una strada semplice: assegnare a ogni parola un numero progressivo. Esempio per gli stati: "Avviato" = 1, "In corso" = 2, "Concluso" = 3.

Questa tecnica si chiama _Label Encoding_, ma per le Reti Neurali è **veleno puro**. Perché? Perché l'algoritmo è una funzione matematica. Se vede che "Concluso" vale 3 e "Avviato" vale 1, penserà intrinsecamente che "Concluso" sia _matematicamente superiore_ o _tre volte più grande_ di "Avviato". Creeremmo finte relazioni logiche che distruggerebbero il modello.

### La Soluzione: One-Hot Encoding

Per le categorie nominali (dove non c'è un ordine di grandezza, come appunto i nomi dei protocolli o dei servizi), l'Ingegneria dei Dati usa il **One-Hot Encoding**.
Questa tecnica prende una colonna e la "esplode" in tante colonne separate quante sono le parole uniche. Queste nuove colonne contengono solo `0` o `1` (Falso o Vero).
Ad esempio, la colonna `service` scomparirà e al suo posto avremo colonne come `service_HTTP`, `service_FTP`, `service_DNS`. Se in quella riga il servizio usato era HTTP, la colonna `service_HTTP` avrà 1, e tutte le altre avranno 0. È geniale, pulito e matematicamente ineccepibile.

#### L'Ostacolo: La Maledizione della Dimensionalità

Il One-Hot Encoding è perfetto per `service` (crea 13 nuove colonne) e `state` (11 colonne). Ma cosa facciamo con `proto`? Se lo esplodessimo così com'è, aggiungeremmo **133 nuove colonne** alla nostra tabella!

In gergo, questo fenomeno viene definito "Curse of Dimensionality" (La maledizione della dimensionalità). Troppe colonne con troppi zeri rallentano l'addestramento dell'intelligenza artificiale e la rendono confusa (il dataset diventa troppo "sparso").

### La Strategia d'Attacco

Applicheremo una manovra combinata:

1. Per `service` e `state`, applicheremo direttamente il One-Hot Encoding.
    
2. Per `proto`, faremo una "potatura" (Clipping). Individueremo i 10 protocolli più utilizzati (che coprono quasi tutto il traffico di una rete, come TCP, UDP, ICMP ecc.) e li manterremo. Tutti gli altri 123 protocolli rari o insoliti li raggrupperemo sotto una singola etichetta chiamata `"other"`. A quel punto, ridotta la cardinalità a 11, applicheremo il One-Hot Encoding anche a lei.

### Il Codice

Python

```
# Troviamo i 10 protocolli più usati (i primi 10 indici della lista di frequenza)

top_10_proto = df_full['proto'].value_counts().head(10).index

# Sostituiamo i protocolli minori con la parola 'other'
# Usiamo una funzione lambda: analizza ogni riga x. Se x è nei top 10, lascialo com'è, altrimenti scrivi 'other'

df_full['proto'] = df_full['proto'].apply(lambda x: x if x in top_10_proto else 'other')

# Applichiamo il One-Hot Encoding a tutte e tre le colonne
# Il comando pd.get_dummies lo fa automaticamente
# drop_first=True serve per rimuovere una colonna ridondante
# dtype=int forza i True/False a diventare 1 e 0 (utile per le Reti Neurali)

df_full = pd.get_dummies(df_full, columns=['service', 'state', 'proto'], drop_first=True, dtype=int)

print("--- TRADUZIONE E ONE-HOT ENCODING COMPLETATO ---")

print(f"Ora abbiamo una tabella tutta numerica con {df_full.shape[0]} righe e {df_full.shape[1]} colonne.")
```

Esegui questa cella. 

## Normalizzazione e split

Creiamo training set con solo dati di traffico normale, e test set mischiato. Inoltre, normalizziamo i valori numerici tra 0 ed 1.

Ora che i dati sono tutti numerici, dobbiamo preparare il terreno per le reti neurali (Autoencoder) e per l'Isolation Forest. Qui entrano in gioco le due lezioni teoriche più importanti dell'Anomaly Detection.

Python

```
# Importiamo gli strumenti matematici da scikit-learn
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import MinMaxScaler

# 1. Separiamo le caratteristiche (X) dall'obiettivo (y)
y = df_full['label']
X = df_full.drop('label', axis=1)

print("--- INIZIO SPLIT DEI DATI ---")
# 2. Dividiamo l'intero calderone: 80% per addestramento misto, 20% per il test finale
X_train_mixed, X_test, y_train_mixed, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

# 3. IL CUORE DELL'ANOMALY DETECTION: Filtriamo per tenere SOLO i dati normali
# Usiamo una "maschera" booleana: tieni le righe di X_train_mixed solo dove y_train_mixed è uguale a 0
X_train_normal = X_train_mixed[y_train_mixed == 0]

print(f"Dati di Training (SOLO NORMALI): {X_train_normal.shape[0]} righe.")
print(f"Dati di Test (MISTI): {X_test.shape[0]} righe.")

# 4. NORMALIZZAZIONE DEI DATI (Scaling tra 0 e 1)
print("\n--- INIZIO NORMALIZZAZIONE ---")
scaler = MinMaxScaler()

# 'fit_transform' fa due cose: studia il valore max/min e applica lo scaling
X_train_scaled = scaler.fit_transform(X_train_normal)

# 'transform' applica solo lo scaling, non faccio la fit sennò starei adddestrando la rete sul test set
X_test_scaled = scaler.transform(X_test)

print("Dati schiacciati tra 0 e 1 con successo! Pronti per i modelli.")
```

## Addestramento e Scoring

Costruiremo prima una **Baseline** (un punto di riferimento base) utilizzando il modello più leggero richiesto dalla traccia: l'**Isolation Forest**.

Se la nostra futura Rete Neurale farà peggio di questo modello base, sapremo immediatamente di avere un problema architetturale.

### La Teoria: Come ragiona l'Isolation Forest?

L'**Isolation Forest** (iForest) è un algoritmo di Machine Learning non supervisionato basato su logiche _ensemble_. A differenza dei metodi tradizionali che cercano di definire la "normalità" o di calcolare le distanze tra i punti (come il k-NN o i metodi basati su densità, che risultano computazionalmente proibitivi su dataset di milioni di record), iForest si basa su un principio radicalmente opposto: **isolare esplicitamente le anomalie**.

Dal punto di vista dell'ingegneria del software, la sua forza risiede nell'efficienza computazionale: ha una complessità temporale lineare $O(n)$ e richiede un utilizzo di memoria minimo, rendendolo ideale per lo streaming di rete in tempo reale.

### 1. La Struttura Dati: Gli Isolation Trees (iTrees)

L'algoritmo costruisce una foresta di alberi decisionali binari chiamati _Isolation Trees_.

Dato un dataset $X = \{x_1, x_2, ..., x_n\}$ di dimensione $n$, dove ogni istanza ha $d$ feature, un iTree viene costruito attraverso un partizionamento ricorsivo e puramente stocastico.

Per ogni nodo dell'albero:

1. Viene selezionata casualmente una feature $q$ (es. i byte trasferiti) tra le $d$ disponibili.
    
2. Viene calcolato il valore minimo ($min$) e massimo ($max$) di $q$ nel sottoinsieme di dati presente nel nodo corrente.
    
3. Viene generato un valore di split casuale $p$ tale che $min < p < max$.
    
4. Lo spazio dei dati viene diviso ortogonalmente: le istanze con valore $< p$ vanno nel nodo figlio sinistro, quelle con valore $\ge p$ nel nodo figlio destro.
    

La ricorsione si ferma quando si verifica una di queste tre condizioni:

- Il nodo contiene una sola istanza (il dato è stato _isolato_).
    
- Tutti i dati nel nodo hanno valori identici.
    
- Viene raggiunto un limite massimo di profondità preimpostato.
    

### 2. La Metrica Fondamentale: Path Length $h(x)$

In un iTree, la **Path Length** $h(x)$ di un'istanza $x$ è definita come il numero di archi attraversati dal nodo radice fino al nodo foglia in cui $x$ risulta isolata.

Strutturalmente, un iTree è equivalente a un Albero Binario di Ricerca (BST). Le anomalie, avendo valori estremi o combinazioni insolite di feature, si trovano in regioni dello spazio "vuote". Di conseguenza, il partizionamento casuale le separerà dal resto del campione ai primissimi tagli.

- **Anomalie:** $h(x)$ molto piccolo (si trovano vicine alla radice).
    
- **Dati normali:** $h(x)$ molto grande (richiedono molti tagli per essere separati dal cluster denso e finiscono in profondità).
    

### 3. Formulazione Matematica dell'Anomaly Score

Poiché la foresta è composta da decine o centinaia di alberi (spesso costruiti su sotto-campioni del dataset originale), l'algoritmo calcola l'aspettativa della Path Length per ogni istanza su tutti gli alberi, denotata come $E(h(x))$.

Per rendere questo valore confrontabile e indipendente dal numero di record $n$, viene normalizzato utilizzando il costo medio di una ricerca fallita in un BST equivalente, definito come $c(n)$:

$$c(n) = 2H(n-1) - \frac{2(n-1)}{n}$$

Dove $H(i)$ è il numero armonico, stimabile come $\ln(i) + 0.5772156649$ (Costante di Eulero-Mascheroni).

A questo punto, l'**Anomaly Score** finale $s$ per un'istanza $x$ dato il dataset di dimensione $n$ è calcolato con la seguente equazione:

$$s(x, n) = 2^{-\frac{E(h(x))}{c(n)}}$$

### 4. Interpretazione del Risultato (Condizioni di Bordo)

L'equazione dello score restituisce sempre un valore compreso tra $0$ e $1$, ed è qui che l'algoritmo prende la sua decisione:

- Se l'istanza viene isolata molto in fretta, $E(h(x)) \to 0$. Matematicamente, l'esponente tende a $0$, quindi $2^0 = 1$. **Se $s \to 1$, l'istanza è un'anomalia certa.**
    
- Se l'istanza è molto difficile da isolare, si troverà alla profondità massima, quindi $E(h(x)) \to n-1$. L'esponente diventa un numero negativo molto grande. **Se $s \to 0$, l'istanza è un dato normale.**
    
- Se tutte le istanze del dataset restituiscono uno score $s \approx 0.5$, significa che $E(h(x)) \approx c(n)$, ovvero non ci sono anomalie distinte nell'intero campione.
    

Nel nostro codice Scikit-Learn precedente, il parametro `contamination=0.05` ha semplicemente detto all'algoritmo di calcolare tutti gli score $s(x,n)$ del dataset di test, ordinarli dal più alto al più basso, e tracciare una soglia (threshold) fissa al 5esimo percentile superiore, etichettando tutto ciò che superava quella soglia matematica come `-1` (allarme Zero-Day).
### Il Codice: Costruzione e Test della Baseline

Crea una nuova cella su VS Code e incolla questo blocco. 

Python

```
from sklearn.ensemble import IsolationForest
import numpy as np

# COSTRUZIONE DEL MODELLO
# contamination=0.05: Diciamo al modello che ci aspettiamo circa un 5% di traffico "strano"
# (Questo ci aiuta a puntare al KPI: False Alarm Rate < 5%)
# random_state=42: Fissa il seme casuale dei "tagli"
iso_forest = IsolationForest(contamination=0.05, random_state=42)

print("--- ADDESTRAMENTO ISOLATION FOREST ---")
# ADDESTRAMENTO: Il modello studia i "tagli" SOLO sul traffico normale scalato
iso_forest.fit(X_train_scaled)
print("Addestramento completato")

print("\n--- FASE DI TEST (SIMULAZIONE IN PRODUZIONE) ---")
# PREVISIONE: Buttiamo nel modello il traffico misto (test) per vedere cosa blocca.
# SINTASSI DI SCIKIT-LEARN: 
# Restituisce '1' se pensa che il dato sia NORMALE.
# Restituisce '-1' se pensa che il dato sia un'ANOMALIA (Attacco).
previsioni = iso_forest.predict(X_test_scaled)

# CONTEGGIO DEI RISULTATI
allarmi_scattati = np.sum(previsioni == -1)
traffico_lasciato_passare = np.sum(previsioni == 1)

print(f"Pacchetti di rete analizzati nel Test Set: {len(previsioni)}")
print(f"Traffico considerato NORMALE (lasciato passare): {traffico_lasciato_passare}")
print(f"Allarmi Zero-Day SCATTATI (Anomalie isolate): {allarmi_scattati}")
```

Ora, confrontiamo il numero di "Allarmi Scattati" stampati dal terminale con il numero reale di attacchi che sappiamo esserci nel dataset.

La variabile y_test contiene esattamente le etichette reali (0 per normale, 1 per attacco) di tutte le carte presenti nel set di test.

Prima di fare il confronto, però, dobbiamo risolvere un piccolo ostacolo ingegneristico chiamato **Label Mismatch** (Disallineamento delle etichette).

Come hai visto, l'Isolation Forest di Scikit-Learn:

- Usa **-1** per indicare un'Anomalia (Attacco).
    
- Usa **1** per indicare un comportamento Normale.

I nostri dati reali (`y_test`), invece:

- Usano **1** per indicare un Attacco.
    
- Usano **0** per indicare un comportamento Normale.

Una volta allineate le labels, utilizzeremo la **Matrice di Confusione**. È una tabella incrociata che incastra le nostre previsioni con la realtà, restituendoci quattro valori fondamentali:

1. **True Positives (TP):** I successi. Gli attacchi reali che il modello ha correttamente intercettato e bloccato.
    
2. **True Negatives (TN):** La normalità. Il traffico pulito che il modello ha correttamente lasciato passare.
    
3. **False Positives (FP):** I falsi allarmi. Traffico innocente che il modello ha bloccato per sbaglio (e che farà arrabbiare gli utenti).
    
4. **False Negatives (FN):** Le falle di sicurezza. Attacchi reali che il modello non ha visto e ha lasciato entrare (il danno peggiore).

Python

```
from sklearn.metrics import confusion_matrix

attacchi_reali = np.sum(y_test == 1)
normali_reali = np.sum(y_test == 0)

print("--- LA REALTÀ DEI DATI ---")
print(f"Nel Test Set ci sono {attacchi_reali} attacchi REALI e {normali_reali} comportamenti NORMALI.")

# TRADUZIONE DELLE ETICHETTE
# Usiamo np.where: Se la previsione è -1, scrivo 1 (Attacco). Altrimenti, scrivo 0 (Normale).
previsioni_tradotte = np.where(previsioni == -1, 1, 0)

# LA MATRICE DI CONFUSIONE
matrice = confusion_matrix(y_test, previsioni_tradotte)

# La matrice ci restituisce una griglia 2x2. Estraiamo i 4 quadranti.
# Il metodo .ravel() ci restituisce la griglia nell'ordine esatto: TN, FP, FN, TP
TN, FP, FN, TP = matrice.ravel()

print("\n--- RISULTATI DELL'ISOLATION FOREST ---")
print(f"Attacchi bloccati (True Positives): {TP} su {attacchi_reali}")
print(f"Attacchi lasciati passare (False Negatives): {FN}")
print(f"Falsi allarmi scattati (False Positives): {FP} su {normali_reali}")
print(f"Traffico normale lasciato passare (True Negatives): {TN}")
```

#### Risultati iForest


## Autoencoder

Utilizzeremo **TensorFlow** (con la sua interfaccia Keras), che in ambito di Ingegneria del Software è uno standard industriale eccellente per la prototipazione rapida e la messa in produzione.

Bash

```
pip install tensorflow
```

Un Autoencoder non è una rete neurale classica che cerca di indovinare un'etichetta (come fa un classificatore). È una rete neurale addestrata a **copiare il suo input sul suo output**.

Immagina l'architettura come una **clessidra**:

1. **Encoder (Compressione):** I dati in ingresso (le nostre 73 colonne) passano attraverso strati sempre più piccoli di neuroni. La rete è costretta a "comprimere" l'informazione, scartando il rumore e mantenendo solo le caratteristiche essenziali del traffico di rete.
    
2. **Collo di Bottiglia (Latent Space):** È il punto più stretto della clessidra. Qui il dato originale di 73 dimensioni è stato ridotto, ad esempio, a sole 16 dimensioni.
    
3. **Decoder (Ricostruzione):** La rete prende questo dato iper-compresso e cerca di "decomprimerlo", ricostruendo le 73 colonne originali.

Confrontando la versione ricomposta con quella originale, capiremo se la nostra rete è diventata brava a scomporre e ricostruire i dati di training.
**Come scova gli attacchi Zero-Day?** Noi addestreremo questa clessidra _esclusivamente_ sul traffico normale. La rete diventerà bravissima a comprimere e decomprimere il traffico pulito, ma sarà pessima quando lo farà con traffico malevolo.

Quando, in fase di test, le faremo ingoiare un pacchetto di un attacco Zero-Day, la rete andrà in crisi: non avendo mai visto quella struttura dati, la comprimerà male e la ricostruirà malissimo.

Noi misureremo questo **Errore di Ricostruzione**: se l'errore è alto, suona l'allarme.

### Creazione dell'Architettura

Python

```
from tensorflow.keras.models import Model
from tensorflow.keras.layers import Input, Dense

# 1. Quante colonne entrano nella rete (Le nostre 73 feature)
input_dim = X_train_scaled.shape[1]

# 2. IL LIVELLO DI INGRESSO
input_layer = Input(shape=(input_dim,))

# 3. L'ENCODER (La metà superiore della clessidra che si restringe)
# Usiamo la funzione di attivazione 'relu', standard per i layer nascosti
encoder = Dense(32, activation="relu")(input_layer)
encoder = Dense(16, activation="relu")(encoder) # Questo è il collo di bottiglia

# 4. IL DECODER (La metà inferiore che si allarga di nuovo)
decoder = Dense(32, activation="relu")(encoder)
# Il layer finale DEVE avere lo stesso numero di neuroni dell'input (73)
# Usiamo 'sigmoid' perché i nostri dati normalizzati sono tutti tra 0 e 1
decoder = Dense(input_dim, activation="sigmoid")(decoder)

# ASSEMBLAGGIO DEL MODELLO
# Diciamo a Keras: "Il modello inizia da input_layer e finisce in decoder"
autoencoder = Model(inputs=input_layer, outputs=decoder)

# COMPILAZIONE
# optimizer='adam': Il "motore" che aggiorna i pesi della rete
# loss='mse': Mean Squared Error (Errore Quadratico Medio). È la metrica che la rete cercherà di minimizzare.
autoencoder.compile(optimizer='adam', loss='mse')

print("--- ARCHITETTURA AUTOENCODER CREATA ---")
autoencoder.summary()
```

Dovrebbe stamparti una tabella riassuntiva (il `summary`) che mostra come i dati entrano a 73 dimensioni, scendono a 32, poi a 16, risalgono a 32 e tornano a 73.

### Addestramento dell'Autoencoder

In un modello classico, fornisci i dati (`X`) e le etichette per indovinare (`y`). Nell'Autoencoder, l'obiettivo del modello non è indovinare un'etichetta esterna, ma **ricostruire l'input stesso**. Pertanto, nel codice dirai alla rete che l'input (`x`) e la soluzione da copiare (`y`) sono **esattamente la stessa cosa**.

Inoltre, implementeremo un meccanismo di sicurezza chiamato **Early Stopping**. Invece di costringere il computer a fare 100 giri di addestramento (epoche) alla cieca, gli diremo: _"Se vedi che l'errore di ricostruzione smette di migliorare per 5 giri consecutivi, fermati in anticipo e salva la versione migliore della rete"_. Questo previene l'Overfitting (cioè quando la rete impara i dati a memoria invece di capirne la logica).

Python

```
from tensorflow.keras.callbacks import EarlyStopping

import matplotlib.pyplot as plt

  

# Configuriamo l'Early Stopping

# monitor='val_loss': controlla l'errore sul set di validazione

# patience=5: se per 5 epoche l'errore non scende, ferma tutto

  

early_stop = EarlyStopping(

monitor='val_loss',

patience=5,

restore_best_weights=True,

verbose=1

) 

print("--- INIZIO ADDESTRAMENTO AUTOENCODER ---")

# Addestramento del Modello
# x e y sono IDENTICI (il modello deve imparare a ricopiare i dati normali)
# validation_split=0.1: teniamo da parte il 10% del traffico normale per verificare
# che stia imparando bene
# batch_size=256: la rete esaminerà i pacchetti di rete a blocchi di 256 per volta
  

history = autoencoder.fit(

x=X_train_scaled,

y=X_train_scaled,

epochs=50, # Numero massimo di giri completi

batch_size=256,

validation_split=0.1,

callbacks=[early_stop],

verbose=1

)

print("\n--- ADDESTRAMENTO COMPLETATO ---")

# Traccio il grafico dell'apprendimento

plt.figure(figsize=(8, 5))

plt.plot(history.history['loss'], label='Errore di Addestramento')

plt.plot(history.history['val_loss'], label='Errore di Validazione')

plt.title('Curva di Apprendimento dell\'Autoencoder')

plt.ylabel('Errore (MSE)')

plt.xlabel('Epoca')

plt.legend()

plt.grid(True)

plt.show()
```

Addestramento completato.
![[Pasted image 20260608193329.png]]
# Scoring 

Dobbiamo testare questo motore sui dati che non ha mai visto e verificare se rispetta i rigidi parametri del progetto: **AUC > 0.90** , **Detection Rate > 95%** e **False Alarm Rate < 5%**.

#### Logica

Invece di chiedere al modello di classificare direttamente un attacco, gli chiederemo di _ricostruire_ l'intero Test Set (che contiene sia traffico normale che attacchi Zero-Day).

Calcoleremo poi l'**Errore Quadratico Medio (MSE)** tra il pacchetto originale e quello ricostruito.

- Se il pacchetto era traffico normale, la rete lo ricostruirà bene (Errore basso).
    
- Se il pacchetto era un attacco (mai visto prima), la rete fallirà la ricostruzione (Errore alto). Questo Errore è il nostro **Anomaly Score**.

### Il Trucco del Thresholding (La Soglia)

Per rispettare il vincolo del False Alarm Rate $<5\%$, applicheremo un approccio ingegneristico rigido: analizzeremo gli errori generati dal solo traffico normale nel Test Set e fisseremo la nostra soglia di allarme al **95° percentile**. In questo modo, stiamo dicendo matematicamente al sistema: _"Sacrifica al massimo il 5% degli utenti innocenti, ma blocca tutto ciò che genera un errore superiore"_.

#### Il Codice di Validazione

Questo script genererà i numeri definitivi che andranno inseriti nel tuo Report Tecnico.

Python

```
from sklearn.metrics import roc_auc_score, confusion_matrix
import numpy as np

print("--- FASE DI TEST: RICOSTRUZIONE ---")
# Chiediamo al modello di ricostruire l'intero Test Set
# (Richiederà qualche secondo per processare decine di migliaia di record)
ricostruzioni = autoencoder.predict(X_test_scaled)

# Calcolo dell'Anomaly Score (Errore Quadratico Medio - MSE) per ogni pacchetto
# np.mean(..., axis=1) calcola la media riga per riga per le 73 feature
mse_score = np.mean(np.power(X_test_scaled - ricostruzioni, 2), axis=1)

# VERIFICA KPI 1: Area Under the Curve (AUC)
# L'AUC valuta la capacità generale di separare le anomalie (1.0 = perfetto, 0.5 = casuale)
auc_score = roc_auc_score(y_test, mse_score)
print(f"\n KPI AUC Raggiunto: {auc_score:.4f} (Obiettivo traccia: > 0.90)")

# CALCOLO DELLA SOGLIA OTTIMIZZATA
# Isoliamo gli score dei soli pacchetti NORMALI (label 0)
score_normali = mse_score[y_test == 0]
# Fissiamo la soglia al 95° percentile per blindare il False Alarm Rate
soglia = np.percentile(score_normali, 95)
print(f"Soglia di allarme calcolata dinamicamente a: {soglia:.6f}")

# THREAT ALERTING (Generazione Allarmi)
# Generiamo un array binario: 1 se l'errore supera la soglia (Attacco), 0 altrimenti (Normale)
allarmi_autoencoder = (mse_score > soglia).astype(int)

# VERIFICA KPI 2 e 3: Estrazione dalla Matrice di Confusione
tn, fp, fn, tp = confusion_matrix(y_test, allarmi_autoencoder).ravel()

detection_rate = tp / (tp + fn)
false_alarm_rate = fp / (fp + tn)

print("\n--- RISULTATI FINALI AUTOENCODER ---")
print(f"Attacchi reali bloccati (True Positives): {tp}")
print(f"Attacchi sfuggiti (False Negatives): {fn}")
print(f"Falsi allarmi (False Positives): {fp}")
print(f"Traffico pulito lasciato passare (True Negatives): {tn}")

print("\n--- VALIDAZIONE KPI ---")
print(f"KPI Detection Rate: {detection_rate*100:.2f}% (Obiettivo: > 95%)")
print(f"KPI False Alarm Rate: {false_alarm_rate*100:.2f}% (Obiettivo: < 5%)")
```

Esegui questo codice per generare i risultati. Se le percentuali soddisfano i requisiti del progetto , il sistema Zero-Day Detector può considerarsi validato dal punto di vista algoritmico.