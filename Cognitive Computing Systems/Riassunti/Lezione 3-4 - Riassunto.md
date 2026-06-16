Originale: [[Lezione 3-4]]

---
# Evoluzione di DeepQA
## Analisi del prompt

Un sistema cognitivo deve sempre analizzare un prompt per capire:

- Intent: volontà dell'utente
	
- Entities Involved: entità coinvolte
	
- Emotional Tone: tono emotivo
	
- Context: contesto operativo (dettagli utente)

### Tipi di risposta

Tutto questo serve per personalizzare la risposta.

Varie possibilità:

- Dialog: il sistema descrive passi da seguire
	
- App Navigation: il sistema porta l'utente alla sezione giusta
	
- Retrieval: recupero informazione richiesta
	
- Action/External APIs: il sistema chiama APIs esterne per esaudire richieste estranee all'app corrente (aprire una mappa)
	
- Human Agent: per problemi critici viene contattato un umano

Le risposte si suddividono in:

- Risposte Predefinite: se la domanda è standard
	
- Generazione di Ipotesi: genera varie possibili risposte

Ogni ipotesi generata viene valutata in base alle prove presenti nel corpus, i risultati vengono combinati e classificati e infine viene fornita la risposta finale con valore di affidabilità.
## Analisi della domanda

L'evoluzione di DeepQA separa l'analisi della domanda dalla generazione della risposta.
Per analizzare la domanda:
- Natural Language Classifier: identifica intento
	
- Natural Language Understanding: capisce entità rilevanti e struttura logica del testo
	
- Tone Analyzer: rileva lo stato emotivo

## Hypotesis Generation - Long Tail Solution

La long tail solution è una tecnica usata per rispondere a quesiti complessi.
Serve per estrarre informazioni dal corpus.
Fasi operative:

- Corpus: si cerca nella base di conoscenza
	
- Ingestion: i documenti del corpus vengono suddivisi in blocchi più semplici dette unità di risposta, o answer units
	
- Search and Score: il sistema indicizza le unità e gli da un punteggio
	
- Rank: tramite machine learning viene fatta una classifica mettendo in ordine le prove raccolte

# Microservizi

I sistemi cognitivi evoluti da DeepQA implementano microservizi.
### Machine Perception

Nell'aria della Machine Perception, ossia quella parte dell'architettura cognitiva che serve ad interpretare diverse tipologie di fonte, troviamo i seguenti microservizi:

- Speech to Text e Text to Speech: analisi vocale bidirezionale
	
- Visual Recognition: interpretazione contenuti visivi
	
- Language Translator: traduzione automatica

### Decision Making

Il livello di Decision Making elabora i dati del layer Percezione e prende una decisione tramite questi microservizi:

- Orchestration: coordina il flusso dei vari microservizi per fornire un'unica risposta coerente
	
- Action: interazione con l'esterno tramite APIs e altro

# Watson Conversation Service

Evoluzione commerciale del DeepQA visto fino ad ora.
Il Watson Conversation Service orchestra vari strumenti per guidare l'utente verso la soluzione finale.

Casi d'uso:

- Virtual Assistant: fornisce risposte in linguaggio naturale
	
- Virtual Agent: esegue operazioni complesse o estrae risposte implicite
	
- Expert Advisor: genera suggerimenti formulandoli a partire da una base di conoscenza  

# Watson Discovery Service

Ha due componenti principali:

- Analisi dei dati: estrae insights da enormi volumi di dati
	
- Runtime Flow: mostra tutto il processo a cui vengono sottoposti i dati