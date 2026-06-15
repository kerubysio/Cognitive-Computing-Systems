MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 17/03/2026
FONTE: L7 DEEPQA ARCHITECTURE 24-3-2024 -1.pdf
RIASSUNTO: [[Lezione 2 - Riassunto]]

--- 

![[Pasted image 20260402183240.png]]

![[Pasted image 20260402183258.png]]
Vediamo adesso tre elementi che ci hanno portato ad avere l'esigenza di un nuovo paradigma di programmazione, ossia quello cognitivo.

![[Pasted image 20260402183432.png]]
Primo: i big data

![[Pasted image 20260402183502.png]]
Secondo: migliorare l'interazione tra uomo e macchina

![[Pasted image 20260402183559.png]]
Terzo: i sistemi cognitivi apprendono similmente a come fa un essere umano

![[Pasted image 20260402183631.png]]

![[Pasted image 20260402183726.png]]
I sistemi di **Cognitive Computing** rappresentano un'evoluzione fondamentale dell'intelligenza artificiale, focalizzata sulla creazione di macchine capaci di interagire con gli esseri umani in modo naturale e di gestire informazioni non strutturate. Una delle pietre miliari di questo campo è stata la sfida di **Jeopardy!**, un quiz televisivo statunitense basato su domande complesse, giochi di parole e riferimenti culturali.
Nel 2011, IBM ha presentato **Watson**, un sistema progettato per competere a dominio aperto contro i campioni mondiali del gioco. Watson non si limitava a cercare parole chiave in un database, ma doveva comprendere il linguaggio naturale, valutare l'ironia e la sottigliezza delle domande e, soprattutto, fornire una risposta in tempi rapidissimi, battendo infine i migliori giocatori umani.

Il successo di Watson è stato reso possibile dall'architettura **DeepQA**, una pipeline tecnologica avanzata per il **Question-Answering (QA)**. A differenza dei motori di ricerca tradizionali che restituiscono un elenco di documenti, un sistema QA analizza la domanda, formula molteplici ipotesi di risposta in parallelo, raccoglie prove a sostegno di ciascuna e assegna loro un punteggio di confidenza. Questo progresso tecnologico ha aperto la strada ad applicazioni pratiche in settori critici come la sanità, la conformità legale, la sicurezza e la business intelligence. In questi ambiti, l'obiettivo non è solo vincere un gioco, ma supportare i professionisti nella gestione di enormi volumi di conoscenza, permettendo di estrarre informazioni rilevanti per prendere decisioni tempestive e accurate.

![[Pasted image 20260402183854.png]]

![[Pasted image 20260402184017.png]]

![[Pasted image 20260402184538.png]]

![[Pasted image 20260402184506.png]]

![[Pasted image 20260402185447.png]]

![[Pasted image 20260402185503.png]]

![[Pasted image 20260402185551.png]]
![[Pasted image 20260402185620.png]]

![[Pasted image 20260402185733.png]]
> L'architettura **DeepQA** rivoluziona il concetto di risposta automatica trasformandolo da una semplice ricerca lineare in un complesso processo di generazione e valutazione di ipotesi massicciamente parallelizzato. Invece di cercare una singola "parola chiave" che porti a un'unica risposta, il sistema affronta l'incertezza del linguaggio naturale attraverso tre pilastri computazionali.

- In primo luogo, analizza contemporaneamente **molteplici interpretazioni della domanda**, riconoscendo che una frase può nascondere diversi significati o sfumature semantiche.
	
- Sulla base di queste diverse interpretazioni, il sistema procede a **generare un ampio spettro di risposte o ipotesi plausibili**.
  Questo passaggio non è selettivo, ma inclusivo: l'obiettivo è esplorare quante più possibilità il corpus di dati permetta.
	
- Infine, la fase critica consiste nel **raccogliere e valutare prove concorrenti** per ciascuna di esse. Ogni prova viene analizzata per determinare se supporti o confuti l'ipotesi specifica, permettendo al sistema di sviluppare un livello di fiducia differenziato.

In sintesi, DeepQA non "trova" la risposta, ma costruisce statisticamente la credibilità di diverse opzioni per selezionare quella con il supporto statistico più solido.

![[Pasted image 20260402185846.png]]
Qui il prof non si dilunga molto.

Il compito dell'**Automatic Open-Domain Question Answering** rappresenta una delle sfide storiche dell'intelligenza artificiale, poiché mira a emulare l'esperienza umana nel rispondere a domande formulate in un linguaggio naturale ricco e complesso, spaziando su un dominio di conoscenza potenzialmente infinito. A differenza dei sistemi di ricerca tradizionali che si limitano a trovare documenti pertinenti, un sistema di questo tipo deve essere in grado di elaborare la domanda, comprendere l'intento dell'interlocutore e restituire non un elenco di link, ma una risposta puntuale e giustificata.

Per essere considerato efficace, il sistema deve soddisfare quattro requisiti operativi fondamentali:

- **Risposte Precise**: Deve identificare con esattezza l'oggetto della domanda e fornire una risposta specifica, non vaga.
    
- **Confidenza Accurata**: Deve essere in grado di auto-valutarsi, determinando la probabilità che la risposta fornita sia corretta (likelihood).
    
- **Giustificazioni Fruibili**: Deve saper spiegare il "perché" di quella risposta, offrendo all'utente le prove che la supportano.
    
- **Tempi di Risposta Rapidi**: In contesti come il gioco di Jeopardy!, ma anche nel supporto decisionale professionale, la precisione e la confidenza devono essere prodotte in tempi brevissimi (spesso meno di 3 secondi).

> La filosofia alla base di DeepQA è che la vera intelligenza non derivi da un singolo algoritmo perfetto, ma dall'integrazione di molti algoritmi diversi che analizzano i dati da prospettive differenti, collaborando per costruire un consenso basato sulle evidenze disponibili.

![[Pasted image 20260402185958.png]]
Il confronto tra l'utilizzo di un **Motore di Ricerca (Search Engine)** e un **Sistema di Risposta Esperto (Expert Q&A)** evidenzia un cambio di paradigma nel modo in cui un decisore umano ottiene informazioni per compiere scelte consapevoli.
In un sistema basato sulla ricerca tradizionale, infatti, il carico cognitivo grava quasi interamente sull'utente che deve formulare la propria domanda in poche parole chiave, leggere manualmente una lista di documenti (spesso ordinati per popolarità e non per pertinenza specifica), estrarre le risposte e analizzare le prove da solo. Il motore di ricerca è quindi uno strumento passivo che si limita a trovare documenti contenenti i termini richiesti.

Al contrario, un **sistema cognitivo esperto** (come Watson) inverte questa dinamica agendo come un collaboratore attivo che comprende il linguaggio naturale. Il decisore pone la domanda in modo completo e il sistema si occupa di comprenderne il contesto, generare autonomamente possibili risposte, analizzare le prove a sostegno di ciascuna e calcolare un grado di fiducia (confidence). L'utente riceve non una lista di documenti, ma una risposta diretta accompagnata dalle prove e dal livello di affidabilità calcolato. Questo approccio riduce drasticamente il tempo di analisi e permette all'esperto umano di concentrarsi sulla valutazione finale del suggerimento, spostando l'intelligenza dal semplice "recupero dati" al "ragionamento basato sulle evidenze".

![[Pasted image 20260402190135.png]]

---

![[Pasted image 20260402190153.png]]
Dal punto di vista implementativo, DeepQA si basa sul framework **UIMA (Unstructured Information Management Architecture)**. UIMA è uno standard aperto progettato specificamente per gestire l'interoperabilità tra diversi strumenti di analisi di contenuti non strutturati (testi, audio, video). Grazie a questo framework, DeepQA può scalare orizzontalmente, permettendo di analizzare enormi quantità di dati in frazioni di secondo. La struttura a pipeline di UIMA consente a diversi algoritmi di "annotare" il testo in successione, arricchendo progressivamente la comprensione del sistema fino alla formulazione della risposta finale con il relativo grado di confidenza.

![[Pasted image 20260402190230.png]]
L'architettura **DeepQA** opera come un sistema massicciamente parallelo e probabilistico basato sulle evidenze. Il suo funzionamento si articola in una serie di fasi sequenziali che trasformano una domanda in una risposta motivata. Il processo inizia con la **Question Analysis**, dove il sistema identifica il tipo di domanda e il focus, seguita dalla **Decomposition**, che spezza quesiti complessi in sottoproblemi più semplici. Successivamente, la fase di **Hypothesis Generation** (o Candidate Answer Generation) produce centinaia di possibili risposte candidate attingendo a fonti eterogenee, sia strutturate (database, ontologie) che non strutturate (testi enciclopedici, articoli).
Il cuore del sistema risiede nella **Hypothesis and Evidence Scoring**. Per ogni risposta candidata, vengono attivati numerosi algoritmi indipendenti che cercano "prove" a supporto o a confutazione. Questo processo non è binario: ogni algoritmo assegna un punteggio basato su diverse prospettive (semantica, temporale, geografica, ecc.). Nella fase finale di **Synthesis**, tutti i punteggi vengono combinati e pesati attraverso modelli di apprendimento automatico precedentemente addestrati (**Learned Models**). Il risultato è una lista di risposte classificate (**Final Confidence Merging & Ranking**), dove la risposta vincente è quella che ha accumulato il supporto più forte e coerente, espressa insieme a un preciso valore di confidenza.

È un'architettura molto complessa, analizziamo le sue parti partendo dai dati.

![[Pasted image 20260402190328.png]]

![[Pasted image 20260402190835.png]]
L'architettura **DeepQA** trae la sua forza dalla capacità di integrare ed elaborare vastissime quantità di informazioni provenienti da fonti estremamente eterogenee. Per affrontare sfide complesse come quelle di Jeopardy!, il sistema attinge a **dati non strutturati**, che costituiscono la maggior parte della conoscenza umana disponibile: enciclopedie (come Wikipedia), dizionari, opere letterarie ecc. Parallelamente, DeepQA sfrutta **dati strutturati**, organizzati in database, che forniscono relazioni logiche e fatti precisi già categorizzati.

Il processo decisionale avviene in due fasi principali di reperimento e analisi:

1. **Candidate Answer Generation**: In questa fase, il sistema esegue una ricerca primaria (Primary Search) per generare centinaia di possibili risposte candidate, interrogando le "Answer Sources".
    
2. **Hypothesis and Evidence Scoring**: Una volta ottenute le risposte candidate, il sistema non si ferma, ma cerca ulteriori "prove" (Evidence Retrieval) nelle "Evidence Sources" per confermare o smentire ogni singola ipotesi.

Il passaggio finale è affidato a modelli di **apprendimento automatico (Learned Models)**, che hanno il compito di pesare l'impatto di ciascuna fonte di prova. Poiché non tutte le fonti hanno la stessa affidabilità per ogni tipo di domanda, questi modelli imparano a combinare i vari punteggi parziali per produrre una valutazione sintetica finale. Il risultato è una risposta fornita con un grado di fiducia calcolato matematicamente, basato sulla solidità delle evidenze trovate sia nei testi liberi che nei database strutturati.

![[Pasted image 20260402191008.png]]

![[Pasted image 20260402191113.png]]
La **Minimum DeepQA Pipeline** riassume i passaggi essenziali che il sistema deve compiere per trasformare un input in linguaggio naturale in una risposta ponderata. Il flusso si articola in cinque fasi logiche:

1. **Analisi delle domande**: È il momento della comprensione semantica in cui il sistema identifica cosa viene chiesto, il tipo di risposta attesa e i vincoli logici presenti nel testo.
    
2. **Ricerca primaria**: Il sistema interroga le proprie basi di conoscenza (strutturate e non) per isolare i documenti e i dati che hanno un'alta probabilità di contenere la risposta.
    
3. **Generazione di ipotesi**: Sulla base della ricerca primaria, vengono formulate centinaia di possibili risposte candidate, senza ancora stabilire quale sia quella corretta.
    
4. **Ipotesi e punteggio dell'evidenza**: Per ogni risposta candidata, vengono cercate prove di supporto. Gli algoritmi assegnano punteggi basati sulla coerenza tra le prove trovate e l'ipotesi specifica.
    
5. **Fusione e classifica finale**: Tutti i punteggi parziali vengono combinati e pesati. Il sistema produce una classifica finale delle risposte, selezionando quella con il grado di confidenza più elevato.
