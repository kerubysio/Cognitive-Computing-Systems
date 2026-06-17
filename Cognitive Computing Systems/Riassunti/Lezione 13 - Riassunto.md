Originale: [[Lezione 13]]

---
Per il codice vedere la lezione originale.
# Regressione Lineare Multipla

La studiamo su California Housing Dataset.
Si studiano otto variabili indipendenti per creare una stima sul prezzo di una casa e per capire quali fattori incidono su di esso e in che quantità.
L'estimatore Linear Regression di Scikit-Learn memorizza i parametri calcolati tramite la funzione fit grazie a due parametri, un coefficiente e un intercetta.
Facciamo previsioni tramite la funzione predict e mappa su x ed y la classe Expected e Predict. I punti al di sopra della retta formata tramite coefficiente ed intercetta sono i casi di sovra-stima del prezzo, mentre quelli sotto alla linea sono le sotto-stime.

# Metriche per Modelli di Regressione

Un modello di regressione si valuta tramite il parametro $R^2$, che esprime un valore compreso tra 0.0 e 1.0 che indica quanto il modello è bravo a predire la singola variabile dipendente a partire dalle variabili indipendenti.

# Machine Learning Non Supervisionato

Si lavora su campioni privi di etichette.

## Dimensionality Reduction

La visualizzazione geometrica diventa impossibili per feature superiori a tre, di conseguenza è necessario effettuare un ridimensionamento del dataset.
Grazie alla compressione dello spazio delle feature si possono eliminare del tutto quelle variabili che sono fortemente correlate tra di loro, e che quindi non aggiungono nessuna nuova informazione al sistema.

### t-SNE

Per ridurre la dimensionalità si utilizza l'algoritmo t-SNE, che ci permette di ridurre lo spazio delle feature ad uno bidimensionale o tridimensionale senza perdere informazioni sulla vicinanza spaziale delle feature.
