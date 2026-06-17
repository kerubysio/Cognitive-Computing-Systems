Originale: [[Lezione 14]]

---

# Unsupervised Machine Learning - k-Means Clustering

Il k-Means Clustering è una tecnica di Machine Learning non supervisionato in cui si esamina un insieme di campioni non etichettati e li si raggruppa autonomamente in  clusters.
La variabile k è l'iperparametro fondamentale che definisce il numero di cluster che l'algoritmo deve creare. Per farlo, l'algoritmo si avvale del k-NN attraverso i centroidi per minimizzare la varianza interna ad ogni singolo cluster.

## Descrizione dell'algoritmo

- Inizializzazione casuale: l'algoritmo sceglie k elementi a caso che fanno da centroidi iniziali
	
- Assegnazione dei punti: i rimanenti campioni vengono assegnati al cluster del centroide a loro più vicino
	
- Aggiornamento iterativo: i centroidi vengono ricalcolati considerando la media della coordinate di tutti i punti assegnati al singolo cluster, e i campioni 
  vengono quindi redistribuiti nei nuovi cluster

Questo processo è un loop che si ripete finchè non si minimizzano le distanze tra i centroidi e i rispettivi punti del cluster.
L'algoritmo restituisce due strutture dati principali:

- Un array multidimensionale di etichette che indica l'indice del cluster di appartenenza di ciascun campione
	
- Un array bidimensionale di centroidi, contenente le coordinate spaziali finali di ogni centroide per ogni cluster

## Dataset Iris

Volendo possiamo applicare quanto detto al dataset Iris.

## Dimensionality Reduction with PCA

Grazie all'estimatore PCA possiamo ridurre il dataset Iris da 4 a 2 dimensioni.
In generale per scegliere il miglior estimatore vanno provati tutti quanti.

# Introduzione al Deep Learning

È un potente sottoinsieme del machine learning.
Per funzionare servono:

- Big Data
	
- Potenza di calcolo dei processori
	
- Velocità di rete internet più elevate
	
- Avanzamenti nell'hardware e nel software per il calcolo in parallelo

Per addestrare modelli di Machine Learning si ricorre a:

- GPU: riescono a eseguire in maniera simultanea calcoli su matrici

- TPU: chip sviluppati appositamente per lavorare sui tensori