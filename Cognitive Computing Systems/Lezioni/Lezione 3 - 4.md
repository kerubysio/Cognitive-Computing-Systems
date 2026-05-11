MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 17/03/2026 e 20/03/2026
FONTE: L8 EVOLUTION FROM DEEPQA TO WATSON DEVELOPER CLOUD 24-3-2024 -1.pdf (slide 45 e successive)

---

![[Pasted image 20260403185033.png]]

![[Pasted image 20260403185051.png]]

![[Pasted image 20260403185209.png]]

![[Pasted image 20260403185243.png]]
Iniziamo con un esempio pratico di interazione uomo-macchina. Un utente esprime un problema in linguaggio naturale: _'Sono frustrato, non sono riuscito ad accedere al sistema di fatturazione online'_.

Per un sistema di **Cognitive Computing**, questo non è solo testo, ma un insieme di dati da decodificare su più livelli:

- **Intent (Intento)**: Il sistema deve capire cosa vuole fare l'utente. In questo caso, l'intento viene classificato come 'Password Reset'.
    
- **Entities (Entità)**: Bisogna isolare gli oggetti del discorso. Qui l'entità è il 'Online Billing System'.
    
- **Emotional Tone (Tono Emotivo)**: A differenza di un software tradizionale, analizza lo stato d'animo. La parola 'frustrato' attiva un'etichetta di 'Anger' (Rabbia).
    
- **Context (Contesto)**: Infine, il sistema recupera i dati del profilo. Sappiamo che l'utente è Bill Smith, ha 47 anni, è un cliente 'High Value' (Gold Member) e sta scrivendo da un dispositivo mobile.

Tutte queste informazioni servono a personalizzare la risposta: non daremo la stessa soluzione a un cliente arrabbiato di alto valore e a uno che sta solo chiedendo un'informazione generica.

![[Pasted image 20260403185316.png]]

![[Pasted image 20260403185340.png]]
Una volta analizzato l'input, il sistema deve decidere quale azione intraprendere. Non esiste un'unica strada, ma diverse strategie a seconda della domanda:

1. **Dialog (Dialogo)**: Se l'utente chiede come resettare la password, il sistema lo guida passo dopo passo.
    
2. **App Navigation (Navigazione)**: Se l'utente deve pagare una fattura, l'app lo porta direttamente alla schermata corretta.
    
3. **Retrieval (Recupero)**: Se viene chiesta un'informazione specifica, il sistema cerca e restituisce la risposta.
    
4. **Action/External APIs**: Se l'utente cerca un negozio, il sistema lancia una mappa con le direzioni.
    
5. **Human Agent**: Se il problema è critico (es. furto della carta di credito), il sistema 'effettua l'escalation' trasferendo la conversazione a un operatore umano.

![[Pasted image 20260403190838.png]]

L'architettura tecnica evoluta da DeepQA permette di gestire la domanda attraverso una pipeline modulare che separa l'analisi semantica dalla generazione della risposta. Il processo si articola in tre macro-fasi principali:

**Analisi della Domanda (Question Analysis)**: La domanda in ingresso viene processata simultaneamente da tre componenti specializzate per estrarne il significato profondo:

- **Natural Language Classifier**: identifica l'intento dell'utente (cosa vuole fare).
    
- **Natural Language Understanding**: estrae le entità rilevanti e comprende la struttura logica del testo.
    
- **Tone Analyzer**: rileva lo stato emotivo dell'utente per calibrare lo stile della risposta.
    

**Percorsi di Risposta** Una volta analizzata la domanda, il sistema può intraprendere due percorsi differenti in base alla complessità del quesito:

- **Risposte Predefinite (Dialog)**: se la domanda rientra in una casistica standard, il sistema attinge a un database di risposte pre-configurate attraverso un modulo di dialogo.
    
- **Generazione di Ipotesi (Hypothesis Generation)**: per domande aperte o complesse, il sistema genera molteplici risposte candidate basate sulla conoscenza disponibile.
    

**Valutazione e Selezione Finale**: Nel percorso di generazione delle ipotesi, il sistema esegue un filtraggio rigoroso prima di rispondere:

- **Hypothesis and Evidence Scoring**: ogni ipotesi viene valutata rispetto alle prove (evidenze) trovate nel corpus di dati.
    
- **Merging & Ranking**: i risultati vengono combinati e classificati in base alla loro probabilità di correttezza.
    
- **Answer & Confidence**: il sistema restituisce la risposta finale accompagnata da un punteggio di confidenza, che indica il grado di sicurezza del sistema sulla validità della soluzione proposta.

![[Pasted image 20260403191124.png]]

Per affrontare domande complesse e specifiche che non possono essere risolte con risposte predefinite, il sistema adotta una strategia definita **long-tail solution**. Questo approccio permette di estrarre informazioni da grandi volumi di dati non strutturati per rispondere a problematiche articolate, come ad esempio la segnalazione di un errore sporadico post-reset della password che si verifica solo su un'applicazione desktop specifica.

Il processo si articola nelle seguenti fasi operative:

- **Corpus**: il sistema attinge a una base di conoscenza vasta che include ticket del service desk, manuali di prodotto e Knowledge Base (KBs).
    
- **Ingestion**: i documenti presenti nel corpus vengono convertiti e frammentati in unità di risposta rilevanti (**answer units**) per facilitare la ricerca.
    
- **Search and Score**: il sistema indicizza queste unità e assegna loro un punteggio di pertinenza confrontandole direttamente con la query inserita dall'utente.
    
- **Rank**: viene utilizzato l'apprendimento automatico (**machine learning**) per riordinare le risposte candidate, garantendo che le soluzioni più probabili e supportate dalle evidenze appaiano in cima alla classifica.

![[Pasted image 20260403191311.png]]

L'architettura mostrata dettaglia il flusso di elaborazione completo per la generazione di risposte basate sulla conoscenza, integrando la gestione del dialogo con il recupero di informazioni da documenti non strutturati. Il processo si sviluppa attraverso le seguenti macro-aree:

**Question Analysis**: La domanda viene analizzata per estrarre l'intento e il contesto emotivo dell'utente.

- **Dialog**: gestisce il flusso della conversazione basandosi sull'analisi effettuata.

**Hypothesis Generation and Scoring**: In parallelo all'analisi, il sistema prepara e interroga la propria base di conoscenza.

- **Document Conversion**: i documenti grezzi (Docs) vengono convertiti in un formato digeribile per il sistema.
    
- **Corpus**: rappresenta l'archivio centralizzato dei documenti elaborati e pronti per la ricerca.
    
- **Retrieve**: il sistema interroga il corpus per estrarre i segmenti di testo (answer units) più pertinenti alla domanda.
    
**Final Merger and Ranking**: Le informazioni recuperate vengono raffinate per produrre il risultato ottimale.

- **Rank**: utilizza modelli di apprendimento automatico per riordinare le risposte candidate in base alla loro rilevanza effettiva.
    
- **Pre-defined Responses**: se la domanda corrisponde a una casistica standard, il sistema può attingere direttamente a risposte già memorizzate.
    
**Answer & Confidence** Il risultato finale viene consegnato all'utente insieme a un punteggio di confidenza. Questo valore indica quanto il sistema sia sicuro della correttezza della risposta in base alle evidenze raccolte e analizzate durante l'intero processo.

![[Pasted image 20260403191711.png]]

Oltre alla comprensione del testo, l'evoluzione del sistema ha integrato capacità di percezione multimodale per gestire input di diversa natura provenienti dall'utente.

![[Pasted image 20260403191850.png]]

L'architettura completa integra le capacità di elaborazione profonda con un livello superiore di **Machine Perception**, che funge da interfaccia multimodale per il sistema. Questo livello permette di gestire input e output non testuali attraverso componenti dedicate:

- **Speech to Text e Text to Speech**: abilitano l'interazione vocale, convertendo il parlato in dati processabili e viceversa.
    
- **Visual Recognition**: permette al sistema di interpretare contenuti visivi (immagini o screenshot), integrando queste informazioni nell'analisi della domanda.
    
- **Language Translator**: gestisce la traduzione automatica per operare in ambienti multilingua.
    
A livello di pipeline interna, il sistema mantiene la distinzione tra il percorso rapido delle **Pre-defined Responses** (risposte pre-configurate) e il percorso analitico di **Hypothesis Generation and Scoring**. Quest'ultimo utilizza il processo di **Document Conversion** per alimentare costantemente il **Corpus** con nuovi documenti (Docs), garantendo che la fase di **Retrieve** possa attingere a una base di conoscenza sempre aggiornata. Il modulo di **Final Merger and Ranking** assicura infine che la risposta erogata sia quella statisticamente più solida tra tutte le ipotesi generate.

---

![[Pasted image 20260403192005.png]]
L'evoluzione dall'architettura monolitica **DeepQA** verso i sistemi di **Cognitive Computing** moderni ha portato alla creazione di una serie di **microservizi** e strumenti robusti. Questa nuova struttura modulare separa nettamente le capacità di percezione dalle funzioni decisionali.

![[Pasted image 20260403192102.png]]

Il livello della percezione raggruppa servizi specializzati nell'analisi di diverse tipologie di input:

- **NLP (Natural Language Processing)**
- **Speech Recognition**
- **Computer Vision**    
- **Data Ingestion**: servizi come _Document Conversion_ e _Retrieve and Rank_ permettono di trasformare documenti grezzi in unità di conoscenza indicizzate e ricercabili

Il livello superiore, ossia quello di Decision Making, utilizza i dati elaborati dalla percezione per guidare l'azione e l'interazione:

- **Orchestration**: coordina il flusso tra i vari microservizi per fornire una risposta coerente.
- **Action**: il sistema interagisce con l'esterno attraverso la logica di business (_Business Logic_), il richiamo di API esterne (_External APIs_), l'accesso a dati aziendali (_Enterprise Data_) e l'utilizzo di analisi predittive (_Predictive Analytics_).

![[Pasted image 20260403192402.png]]
Il **Watson Conversation Service** rappresenta l'evoluzione commerciale dei componenti di dialogo e analisi dell'intento nati con DeepQA. Questo servizio si focalizza sull'interazione e sulla capacità di gestire una conversazione fluida con l'utente.

In sintesi, il Conversation Service non si limita a rispondere, ma "orchestra" una serie di strumenti cognitivi per guidare l'utente verso una risoluzione, integrando conoscenze statiche (manuali, ticket) e dinamiche (dati aziendali).

![[Pasted image 20260403192425.png]]

L'architettura dei sistemi di **Cognitive Computing** si è evoluta in un ecosistema di microservizi specializzati, dove le capacità di percezione alimentano processi decisionali complessi.

--- 

Dalla slide 17 alla slide 25, il professore approfondisce l'architettura tecnica, le funzionalità e l'evoluzione dei servizi di **Cognitive Computing** di IBM, con un focus particolare su **Watson Conversation** e **Watson Discovery**.

### Watson Conversation Service (Slide 17-19)

- **Configurazione del Bot**: La slide 17 mostra l'interfaccia di gestione di un chatbot (es. per una plancia di comando auto), dove vengono definiti **Intents** (es. saluti, localizzazione servizi), **Entities** e il flusso del **Dialog**.
    
- **Architettura di Rete**: Viene illustrata l'integrazione tra rete pubblica, cloud e infrastruttura enterprise. Il flusso include l'orchestrazione dei servizi, lo stoccaggio delle risposte e l'utilizzo di componenti come "Speech to Text" e "Retrieve & Rank".
    
- **Casi d'Uso della Conversazione**:
    
    - **Virtual Assistant**: Fornisce risposte in linguaggio naturale su argomenti ricorrenti.
        
    - **Virtual Agent**: Esegue operazioni complesse o estrae risposte implicite.
        
    - **Expert Advisor**: Genera suggerimenti basandosi su una base di conoscenza, sintetizzando risposte dalle evidenze trovate.
        

### Watson Discovery Service (Slide 20-22)

- **Analisi dei Dati**: Questo servizio è progettato per interrogare grandi volumi di dati (es. recensioni Airbnb) per estrarre insight, come l'analisi del sentiment negativo.
    
- **Runtime Flow**: Viene mostrato come i dati vengano raccolti da fonti pubbliche o aziendali, trasformati, arricchiti (tramite "Knowledge Studio") e normalizzati per essere pronti all'interrogazione da parte delle applicazioni.
    

### Sintesi dell'Evoluzione (Slide 23-25)

![[Pasted image 20260403193054.png]]

- **Riepilogo delle Funzionalità**: L'ultima parte del documento sintetizza l'ecosistema Watson, che si divide in:
    
    - **Machine Perception**: Capacità di vedere (Visual Recognition), ascoltare/parlare (Speech to Text, Text to Speech) e comprendere diverse lingue.
        
    - **Learning & Analysis**: Strumenti per la classificazione del linguaggio naturale, analisi del tono, comprensione della personalità (Personality Insights) e conversione dei documenti.
        
    - **Servizi Chiave**: Il consolidamento delle tecnologie DeepQA nei servizi "Conversation" e "Discovery".
