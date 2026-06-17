Originale: [[Lezione 12]]

---
Per il codice, vedere la lezione originale.
# Valutazione di un classificatore
## Il metodo score

Per valutare l'accuratezza di un classificatore si usa i metodo score.

## La matrice di confusione

La matrice di confusione mostra sia l'accuratezza generale che le classi che più spesso vengono confuse tra di loro.
Ogni roga rappresenta la classe reale, ed ogni colonna quella predetta.
Di conseguenza in ogni casella possiamo vedere quante volte la classe i-esima è stata prevista come classe j-esima.
Sulla diagonale principale, dove i = j, abbiamo le previsioni corrette.

# Validazione

## k-Fold Cross-Validation

La k-Fold Cross Validation è una tecnica avanzata di validazione che ci 
permette di usare tutto il dataset sia per il training che per il testing.
Si suddivide il dataset in k sottoinsiemi detti appunto folds e a turni ogni fold farà sia da testing set che training set. Un set alla volta si usa per il testing e gli altri per il training.

## Hyperparameter tuning

Fase cruciale in cui si ottimizza il modello modificando alcuni parametri interni ad esso. Ad esempio nel k-fold si cerca di trovare il k migliore.

# Linear Regression

Date le feature di addestramento X e le previsioni y, cerca di individuare la retta di regressione che meglio approssima i dati regolando iterativamente pendenza e intercetta della retta.

# Underfitting e Overfitting

L'underfitting si verifica quando un modello è troppo semplice per riuscire a fare astrazioni e quindi non impara nulla.
L'algoritmo invece si verifica quando il modello è troppo potente e memorizza il dataset, anche il rumore. In questo caso il modello performa benissimo sui dati noti e malissimo su quelli nuovi.