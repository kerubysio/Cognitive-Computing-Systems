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

