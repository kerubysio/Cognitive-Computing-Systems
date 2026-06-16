Originale: [[Lezione 5]]

---

# Processo costruzione applicazione cognitiva

Ci sono 7 fasi principali.

## Definizione dell'obiettivo

Stabilisci lo scopo principale del sistema cognitivo.

## Definire il dominio

Si identifica il dominio di base e le fonti.
Si capiscono gli utenti target per calibrare linguaggio e funzionalità (utente esperto o utente finale).
Dopodichè si cerca di fare in modo che l'applicazione si comporti in 
maniera diversa a seconda dell'utente con cui ha a che fare.

## Definizione delle domande e degli indizi da esplorare

In questa case ci sono due macro categorie di sistemi cognitivi:

- Question Answer Systems: rispondono alle domande degli utenti. consultando un database. In caso di indecisione il sistema sceglie tra le alternative fornite grazie a dei livelli di confidenza.
  Inoltre, il sistema dovrà tenere conto anche del profilo dell'utente e fornire risposte in base alla sua conoscenza.
	
- Discovery and Exploration Systems: analizzano i dati per ricavarne pattern, indizi o correlazioni nascoste da esplorare.
  Spesso si usa un approccio di tipo Anticipatory Analytics in cui il sistema cerca di prevedere le domande future dell'utente. Questo è utile soprattutto quando l'utente non sa come formulare la domanda.

Bisogna capire la nostra applicazione a quale categoria appartenga.

## Acquisizione delle fonti di dati

Fase in cui si scelgono le fonti di dati necessarie per alimentare il sistema.
Si sfruttano sia dati strutturati interni (ossia raccolti direttamente dall'azienda), sia dati interni strutturati e non, come possono essere ad esempio le informazioni appartenenti al dominio dell'azienda.

## Creazione e raffinamento del corpora

Si articola in diverse fasi:

- Preparazione: fa in modo che i dati siano leggibili, ricercabili e comprensibili dal sistema
	
- Ingestion: mantiene il sistema aggiornato
	
- Raffinamento ed espansione del corpora: aumenta il livello di accuratezza delle risposte
	
- Governance: ci si assicura il rispetto delle norme di privacy e sicurezza

## Training e Testing

Fasi per migliorare l'accuratezza dell IA tramite tre parametri fondamentali:

- Recall: è la capacità di reperire informazioni, si migliora aggiungendo nuovi contenuti o revisionando le ground truth, ossia le conoscenze di base
	
- Precision: è l'affidabilità generale del sistema nel fornire risposte, per migliorarla si devono gestire al meglio  le varianti delle domande integrando nuove fonti
	
- Accuracy: è l'accuratezza delle risposte, ossia quanto sono distanti dalla risposta ideale. Si migliora colmando le lacune concettuali del sistema, facendo in modo che il sistema interpreti correttamente le informazioni che le gli vengono fornite