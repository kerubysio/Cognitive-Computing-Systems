MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 29/05/2026
FONTE: 

---

# Modelli Pre-addestrati, Evoluzione dei Dati e Paradigmi di Machine Learning

## 1. L'Utilizzo di Modelli Pre-addestrati nel Deep Learning

Nell'ambito del Deep Learning, l'approccio classico per la creazione di un'intelligenza artificiale prevede di costruire reti neurali _ex novo_, facendole apprendere da zero per poi passare alle fasi di validazione e test.

Tuttavia, si preferisce sempre di più un approccio diverso: l'utilizzo di **modelli di pre-apprendimento**. Si tratta di modelli di reti neurali che hanno già subito un processo di addestramento su vasti dataset. Invece di concentrarsi sull'addestramento di ogni singola rete partendo da zero, si sfruttano queste reti già "istruite" per risparmiare tempo e risorse computazionali, adattandole poi a compiti specifici.

## 2. L'Evoluzione dell'Analisi dei Dati nell'Industria

Nel corso del tempo, l'approccio delle industrie (come ad esempio le società di telecomunicazioni) all'analisi dei clienti è profondamente cambiato.

- **Dal Data Mining strutturato ai Dati Non Strutturati:** Inizialmente l'attenzione era rivolta esclusivamente al _data mining_ di informazioni strutturate. Oggi, l'analisi include anche **informazioni storiche non strutturate**, come i commenti lasciati nei sondaggi o le note trascritte durante le interazioni con i call center.
    
- **Il Profilo Completo del Cliente:** L'approccio analitico avanzato moderno consiste nel riunire informazioni strutturate, non strutturate e dati aggiornati provenienti dai **social media**. Questo mix permette di sviluppare un profilo utente estremamente completo.
    
- **Prevenzione dell'abbandono (Churn Prevention) e Fidelizzazione:** Le tecnologie di apprendimento automatico vengono impiegate per analizzare questi profili e identificare rapidamente i clienti che l'azienda è maggiormente a rischio di perdere. Questo permette al business di sviluppare tempestivamente strategie di miglioramento e fidelizzazione.

## 3. Applicazioni e Categorie del Machine Learning

L'apprendimento automatico trova applicazione in innumerevoli settori: sanità, robotica, telecomunicazioni, vendita al dettaglio (retail) e produzione manifatturiera.

La scelta dello specifico algoritmo da utilizzare non è casuale, ma dipende strettamente da tre fattori: il **tipo di problema** da risolvere, il **volume dei dati** a disposizione e la **tipologia dei dati** stessi. Gli algoritmi si dividono principalmente in due grandi macro-categorie:

### 3.1 Apprendimento Supervisionato (Supervised Learning)

Questa tecnica utilizza **dati etichettati (labeled data)** per addestrare un modello.

- **Cosa sono i dati etichettati?** Sono dati a cui è stato associato un _tag_ o un identificatore
    
- **Come funziona:** Il modello si allena su questi dati già catalogati per imparare a riconoscere i pattern. Una volta addestrato, il modello sarà in grado di prendere dati nuovi ed effettuare categorizzazione

### 3.2 Apprendimento Non Supervisionato (Unsupervised Learning)

Al contrario del precedente, questo approccio utilizza nel suo processo di formazione esclusivamente **dati senza etichetta**.

- **Cosa sono i dati senza etichetta?** Sono dati grezzi, totalmente privi di tag, identificatori, metadati o giudizi umani preconcetti sul loro contenuto