Originale: [[Lezione 15]]

---

Ci sono vari usi.
Codice, anaconda environement (ma puoi anche usare vscode normale) e ripasso di reti neurali da vedere nella lezione originale.

---

# Tensori

I framework di deep learning elaborano i dati sotto forma di tensori, ossia array multidimensionali.

Varie tipologie:

- Tensore 0D: contiene un solo numero
	
- Tensore 1D: array ad una dimensione, è un vettore. Si usa per dati sequenziali ordinati
	
- Tensore 2D: array a due dimensioni, è una matrice. Si usa per le immagini in scala di grigi
	
- Tensori 3D: array tridimensionali. Si usa per immagini a colori o raccolte di immagini in scala di grigi
	
- Tensore 4D: array tetra-dimensionale. Si usa per batch di immagini a colori o per video
	
- Tensore 5D: array penta-dimensionale. Si usa per raccolte di video

# Convolutional Neural Network

Utili per computer vision, natural language processing e recommender systems (ossia i sistemi di raccomandazione).
Una CNN affronta i problemi eseguendo una classificazione probabilistica multiclasse, in cui:

- Output probabilistico: il risultato finale è un vettore in cui ogni elemento rappresenta la probabilità che il campione di input appartenga ad una certa classe
	
- Criterio di predizione:  la classe predetta è quella con la probabilità più alta

## Riproducibilità

I chip eseguono numerosi calcoli in virgola mobile che risentono quindi dell'ordine 
in cui vengono eseguiti. Per questo spesso i risultati mancano di riproducibilità.

## Come costruire una rete neurale

Ci sono tre componenti principali:

- Network (o Modello): è la sequenza di layers ciascuno contenente neuroni artificiali che estraggono le caratteristiche dei vettori di input.
  Ogni livello riceve un vettore di input da quello precedente e fornisce in output al livello successivo un vettore costituito dalla combinazione lineare dell'input tramite pesi e bias che viene poi elaborato da una funzione di attivazione.
  Maggiore è il numero di layers e maggiore è profonda la rete, per questo si dice deep learning
	
- Loss Function (Funzione di Perdita): funzione matematica che quantifica di quanto l'output si distacca dai valori target reali del dataset
	
- Optimizer (Ottimizzatore): algoritmo che aggiorna i pesi sinaptici e i bias della rete basandosi sulla discesa del gradiente della loss function

