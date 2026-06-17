Originale: [[Lezione 11]]

---

Codice non incluso, da vedere nella lezione originale.
# Classification

Il modello deve assegnare una categoria ad ogni elemento di un dataset.
Un esempio di multi-classification la possiamo vedere con Digits.
Partiamo dall'algoritmo k-Nearest Neighbors, anche detto k-NN.

## k-Nearest Neighbors

Per prevedere la classe di un campione analizza i k campioni ad esso più vicini in termini di distanza metrica.
L'algoritmo si basa su un principio di votazione: ognuno dei k vicini propone la propria classe come previsione per il nuovo campione, la classe che riceve più voti vince. Per evitare situazioni di parità, si sceglie quasi sempre un valore di k dispari.

## Flattening

Gli stimatori di scikit-learn lavorano con dati numerici organizzati al massimo in strutture matriciali. Se il nostro dataset, come Digits ad esempio, presenta immagini o valori categorici, è necessario convertirli in valori numerici.
Questo processo prende in nome di flattening, e trasforma le immagini in array unidimensionali.
Le variabili categoriche vanno sottoposte a one-hot encoding che vedremo più avanti.

## Splitting the data

Bisogna dividere il dataset tra training e testing.
Questa operazione inizia con uno shuffling, ossia mescolamento dei dati, che serve per fare in modo che entrambi i sottoinsiemi abbiano caratteristiche simili e non siano influenzati dall'ordine.

## La funzione fit

La funzione fit serve a caricare i dati all'interno di un modello e ad effettuare tutti i calcoli matematici necessari per il suo addestramento.

## La funzione predict

La funzione predict restituisce una tupla di previsioni
