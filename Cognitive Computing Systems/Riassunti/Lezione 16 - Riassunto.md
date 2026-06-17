Originale: [[Lezione 16]]

---

Per il codice, vedere lezione originale

---

# CNN - Gestione dei Dati

Due fasi sequenziali:

- Acquisizione delle fonti di dati: bilanciamento tra fonti interne ed esterne
	
- Creazione e raffinamento del corpora: viene creato e migliorata la base di dati

Layer dopo layer, i vettori prodotti non rappresentano più le features iniziali ma delle astrazioni operate dalla rete.

## Lo Scaler

Le reti di deep learning funzionano meglio quando i dati variano su intervalli piccoli, come ad esempio tra 0 ed 1.
In questo caso entra in gioco lo Scaler, che si occupa appunto di fare in modo che i valori numerici rientrino in un intervallo ben preciso tramite varie tecniche matematiche.

## One-Hot Encoding

Serve a convertire le variabili categoriche in variabili numeriche.
Per farlo, prende ogni valore che la variabile può assumere e crea una nuova colonna che ha come nome il valore stesso. Questa nuova colonna diventa quindi una feature a tutti gli effetti che può assumere solo due valori: 0 se non è presente o 1 se lo è.

# Creating the Neural Network

Per creare una rete neurale si usa il modello Sequential di Keras (modulo di tensorflow). Dopodichè si aggiungono i layers.

### Layer Convoluzionale

È il primo livello della rete.
Sfrutta la vicinanza spaziale tra gli elementi per apprendere caratteristiche.
Nel caso del dataset Digits, analizza le immagini partendo dai singoli pixel e calcolando le distanze tra di essi per estrarne nuove informazioni.
Vediamo come funziona la convoluzione su un'area quadrata di 6x6 pixel:

- Viene applicato un kernel, ossia un'area quadrata di 3x3 pixel. Consiste in un filtro che si sovrappone alla matrice originale e condensa i 9 pixel al suo interno in un unico pixel tramite dei calcoli matematici
	
- Scorre sull'intera immagine da sinistra a destra per ogni riga, e genera una nuova feature map finale 4x4

Abbiamo quindi ridotto le feature di due unità, da 6x6 a 4x4.
Di solito possiamo applicare più filtri in sequenza.

L'insieme delle risposte prodotte dai singoli filtri prende il nome di feature map.
Gli strati convoluzionali successivi combinano le feature estratte dagli strati precedenti e riescono piano piano ad estrarre features via via sempre più complesse.

# Overfitting

Si verifica quando il modello è troppo complesso e quindi impara a memoria l'input.

### Pooling Layer

Serve a mitigare il rischio di overfitting e ridurre i tempi di calcolo.
Di solito si inseriscono uno o più pooling layer dopo uno strato convoluzionale.
Il Pooling Layer comprime il volume informativo scartando le caratteristiche che reputa essere meno rilevanti, migliorando così le prestazioni su dati mai visti prima.
La tecnica più diffusa si chiama max pooling e consiste nell'esaminare le feature map con un filtro di dimensione 2x2 che si muove come il kernel visto prima, dopodichè analizza i valori numerici al suo interno e li comprime in un unico corrispondente al valore massimo.
La compressione totale è del 75%.

### Flattening

Se vogliamo che l'output definitivo sia un vettore di probabilità, dobbiamo appiattire l'output finale in un singolo array monodimensionale. Per farlo si usa la funzione flatten di keras.

### Dense Layer

Sono dei Layer completamente connessi che servono a mettere insieme i risultati prodotti per ottenere un singolo valore di probabilità.
Di solito vengono inseriti alla fine della rete, e l'ultimo layer è spesso di tipo dense.
Per produrre il risultato finale, nel layer viene implementata la funzione di softmax che  combina i valori in uscita della rete in un'unica distribuzione di probabilità che segue solo due regole basilari:

- Ciascun valore è compreso tra 0.0 e 1.0
	
- La somma algebrica dei valori dei 10 neuroni è sempre pari ad 1.0
