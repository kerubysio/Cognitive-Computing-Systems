MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 27/03/2026
FONTE: L13-IL PROCESSO DI COSTRUZIONE DI UNA APP COGNITIVA.pdf

---

![[Pasted image 20260410173953.png]]

![[Pasted image 20260410174035 1.png]]

Il processo di costruzione di un'applicazione cognitiva si articola in sette fasi sequenziali che trasformano un'idea in un sistema funzionante. Tutto inizia con la definizione dell'obiettivo (cosa deve fare il sistema) e del dominio di riferimento (il perimetro di conoscenza), seguiti dall'analisi dettagliata degli utenti target e delle loro caratteristiche.
Il nucleo operativo consiste nell'individuare le domande chiave a cui il sistema deve rispondere e nel reperire le fonti di dati necessarie per costruire e raffinare il corpora (l'insieme dei documenti e delle informazioni che "istruiscono" l'IA).
Il ciclo si conclude con le fasi di training e testing, fondamentali per addestrare il modello e verificare che le risposte siano accurate e pertinenti rispetto agli obiettivi iniziali.

![[Pasted image 20260410174256.png]]

La definizione dell'obiettivo è la fase cruciale in cui si stabilisce lo scopo primario del sistema cognitivo in ambito sanitario. Gli obiettivi possono variare dal supporto diretto al cittadino, aiutandolo a ottimizzare salute e benessere tramite informazioni e social support, all'incentivazione di un ruolo proattivo nella gestione della propria cura (self-care).
Il sistema può inoltre fungere da strumento decisionale per verificare l'efficacia e la convenienza dei piani terapeutici selezionati per i pazienti, oppure operare come risorsa educativa avanzata per gli studenti di medicina, integrando e approfondendo le conoscenze acquisite durante le loro specializzazioni.

![[Pasted image 20260410174455.png]]

La fase di definizione del dominio stabilisce il perimetro operativo dell'applicazione cognitiva, identificando per ogni settore specifico le fonti dati necessarie e le figure esperte indispensabili per l'addestramento.

![[Pasted image 20260410174610.png]]

La fase di comprensione degli utenti è fondamentale per calibrare il linguaggio e le funzionalità dell'applicazione cognitiva in base al profilo di chi la utilizzerà. È necessario stabilire se il sistema sia rivolto a un esperto di settore o a un utente finale, verificando se quest'ultimo possiede già il vocabolario tecnico specifico (es. chimico o meccanico) o se, al contrario, l'applicazione stessa debba avere una funzione formativa per istruire l'utente in un determinato dominio.
Definire questi attributi permette di progettare un'interazione efficace, evitando barriere terminologiche o eccessive semplificazioni che renderebbero lo strumento poco utile.


![[Pasted image 20260410174723.png]]

La seconda parte della definizione degli attributi utente si concentra sulla gestione della eterogeneità del target, pianificando le diverse tipologie di domande e analisi che utenti con background e livelli di esperienza differenti potrebbero sottoporre al sistema.
È fondamentale mantenere l'ambito dell'applicazione cognitivo sufficientemente ampio per supportare questa varietà di utenti, evitando di essere troppo specifici nella fase di progettazione; un'eccessiva verticalizzazione, infatti, rischierebbe di limitare la costruzione del "corpus" di conoscenze, riducendo drasticamente la flessibilità e l'efficacia del sistema nel rispondere a esigenze informative diversificate.

![[Pasted image 20260410174859.png]]

La fase di definizione delle domande e degli indizi distingue i sistemi cognitivi in due macro-categorie operative:
- **Question-Answer Systems**, progettati per rispondere a interrogativi specifici che emergono direttamente dall'utente
- **Discovery and Exploration Systems**, che invece partono dall'analisi dei dati per identificare pattern, indizi o correlazioni nascoste da esplorare.

Identificare a quale di queste categorie appartiene l'applicazione è fondamentale per strutturare correttamente il flusso logico del sistema e determinare come esso debba elaborare le informazioni per soddisfare il bisogno dell'utente.

![[Pasted image 20260410175041.png]]

La definizione delle domande e degli indizi si approfondisce in due approcci operativi:
- **Per Question-Answer Systems si predilige un approccio basato su coppie Domanda-Risposta (Question-Answer pairs):** il sistema attinge a fonti di dati per risolvere quesiti specifici. Nel caso di eventuali risposte conflittuali presenti nel database, il sistema cognitivo sceglierà quella corretta tramite l'analisi delle alternative e l'assegnazione di livelli di confidenza.
- **Per Discovery and Exploration Systems, invece, l' approccio riguarda l'analisi anticipatoria (Anticipatory analytics):** l'applicazione risponde all'utente e, utilizzando modelli predittivi, è in grado di intuire le informazioni di cui ha bisogno e di anticipare le domande successive, rendendo l'interazione più fluida e proattiva.

![[Pasted image 20260410175249.png]]

La slide illustra l'importanza di adattare le coppie Domanda-Risposta (Question-Answer pairs) al profilo specifico dell'utente all'interno di un sistema cognitivo. La risposta per un consumatore finale deve essere di tipo descrittivo e divulgativo, spiegando cos'è lo strumento e come funziona in termini semplici. Al contrario, la risposta per un esperto entra nel merito clinico di rischi e benefici, utilizzando un linguaggio tecnico appropriato.
Questo dimostra che la costruzione dell'applicazione deve prevedere flussi informativi differenziati che tengano conto del background e delle necessità operative dell'interlocutore per garantire l'efficacia del sistema.

![[Pasted image 20260410175446.png]]

La Anticipatory Analytics rappresenta un'evoluzione dei sistemi cognitivi che va oltre la semplice risposta a una domanda esplicita. Questa funzionalità interviene quando l'utente non riesce o non sa come formulare correttamente un quesito; il sistema analizza i dati disponibili per comprendere le esigenze latenti e anticipare i bisogni della persona.

![[Pasted image 20260410175616.png]]

La fase di **Acquisizione delle fonti di dati** costituisce il momento fondamentale in cui si raccolgono e integrano le informazioni necessarie per alimentare il sistema. Questa operazione richiede un equilibrio tra diverse tipologie di dati: è essenziale combinare dati strutturati interni (come le cartelle cliniche EMR nel settore sanitario) con l'analisi di dati "nascosti", ovvero informazioni già archiviate ma mai ancora analizzate.
Inoltre, il processo prevede il bilanciamento di fonti esterne, strutturate e non, appartenenti al dominio applicativo ma non raccolti direttamente dall'azienda. Ciò garantisce che l'intelligenza artificiale possa sviluppare una comprensione profonda, organizzata e contestualizzata dell'ambito in cui è chiamata ad operare.

![[Pasted image 20260410175722.png]]

La fase di creazione e raffinamento del corpora rappresenta il cuore della gestione dati per un'applicazione cognitiva e si sviluppa in quattro passaggi chiave.
La preparazione garantisce che tutti i dati siano leggibili, ricercabili e comprensibili dal sistema, mentre l'ingestion è descritta come un processo continuo che deve avvenire quasi in tempo reale per mantenere il sistema aggiornato. Segue il raffinamento ed espansione del corpora, operazione ciclica necessaria per elevare costantemente il livello di accuratezza delle risposte. Infine, la governance assicura che l'intero patrimonio informativo rispetti rigorosamente i requisiti di privacy e sicurezza, proteggendo l'integrità dei dati trattati dall'IA.

![[Pasted image 20260410175905.png]]

La fase finale di Training e Testing è volta al miglioramento dell'accuratezza dei modelli attraverso l'ottimizzazione di tre metriche fondamentali: Recall, Precision e Accuracy.
Per migliorare la Recall (capacità di recupero), si interviene aggiungendo nuovi contenuti se vi è una carenza di dati o revisionando la "ground truth", ossia le 
conoscenze di base del sistema cognitivo, e gli artefatti di interazione, ossia la cronologia delle interazioni tra il sistema cognitivo e l'utente.
Per la Precision (precisione delle risposte), è necessario gestire le varianti delle domande integrando ontologie e glossari specifici.
Infine, per elevare l'Accuracy generale, si colmano le lacune concettuali del sistema, garantendo che l'applicazione cognitiva non solo trovi le informazioni, ma le interpreti correttamente nel contesto del dominio scelto.




