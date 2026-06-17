Originale: [[Lezione 10]]

---

# Machine Learning

Invece di scrivere regole tramite codice, si cerca di fare in modo che la macchina estragga automaticamente queste regole partendo dall'analisi di dati storici.
Si possono creare modelli per la previsione di dati o categorie, oltre a vari casi d'uso.

## Scikit-Learn

Libreria di Python che ci permette di usare i principali modelli di Machine Learning tramite incapsulamento ed efficienza del codice.

### Workflow

- Training
	
- Testing
	
- Predizione e inferenza

Per scegliere l'estimatore più adatto tramite scikit learn si adotta spesso la filosofia del try 'em all, ossia provare tutti i modelli e confrontarli.
Questo perchè anche con grande esperienza è difficile capire modello si adatta a quale scopo. Inoltre ogni classe in scikit-learn usa gli stessi metodi, quindi è facile riutilizzare il codice, ed ogni modello restituisce metriche per essere valutato e confrontato con gli altri.

## Tipi di Machine Learning

Nel supervised machine learning abbiamo due categorie:

- Regressione: la variabile da prevedere è un numero continuo
	
- Classificazione: la variabile da prevedere è una categoria o è discreta

Nell'ambito dell'unsupervised machine learning abbiamo una sola categoria:

- Il Clustering: non esiste una variabile da prevedere perchè il dataset non presenta delle etichette. L'obiettivo dell'algoritmo è quell di analizzare la struttura intrinseca dei dati e raggrupparli in cluster

# Step in uno studio di Data Science

- Loading the dataset
	
- Exploring the data: tramite pandas e visualizzazione grafica osservo il dataset e ne capisco le caratteristiche
	
- Transforming your data: trasformo i dati non numerici in dati numerici
	
- Splitting the data: in sottoinsiemi di training e testing
	
- Tuning the model and evaluating its accuracy: calibrazione degli iperparametri del modello
	
- Making predictions: uso il modello su dati nuovi