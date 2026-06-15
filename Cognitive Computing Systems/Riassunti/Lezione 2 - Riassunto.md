Originale: [[Lezione 2]]

---

# Introduzione al DEEP QA

### Paradigma cognitivo

Il paradigma di programmazione cognitivo nasce per tre motivi:

- Big data, e la necessità di elaborarli in maniera semplice
	
- Migliorare interazione fra uomo e macchina
	
- I sistemi cognitivi apprendono similmente a come fa un essere umano

I sistemi di Cognitive Computing sono un'evoluzione dell'intelligenza artificiale, nascono con lo scopo di semplificare le interazioni uomo-macchina e sono capaci di analizzare informazioni non strutturate, cosa importantissima per i big data.

Nel 2011 IBM presenta IBM Watson, un sistema creato per competere contro i campioni mondiali del gioco televisivo "Jeopardy!".
L'obiettivo era quello di creare un sistema cognitivo in grado di interpretare le sfumature del discorso parlato, cercare una risposta valutando fonti eterogenee e fornire prove a sostegno dei risultati ottenuti, il tutto in tempi ragionevoli.

Watson ebbe successo grazie all'architettura Deep QA. Un sistema di questo tipo analizza la domanda, formula molteplici ipotesi di risposta in parallelo (un'ipotesi per ogni possibile interpretazione della domanda), raccoglie prove a sostegno di ciascuna tesi, e assegna ad ognuna di esse un punteggio di confidenza.
Il DeepQA trova applicazione anche in contesti come la sanità, la conformità legale, la sicurezza e la business intelligence, dove agisce come un assistente virtuale capace di raccogliere informazioni e fornire risposte alle domande poste da un professionista del settore.

Watson non nasce con lo scopo di battere il gioco, il suo obiettivo era quello di fare ricerca sulla comprensione del linguaggio naturale e dell'apprendimento automatico.

## Architettura di DeepQA

L'architettura DeepQA non si limita ad una ricerca per parole chiave. Il sistema affronta il problema dell'ambiguità del linguaggio naturale grazie a tre pilastri computazionali:

- Analizza contemporaneamente più interpretazioni della domanda, questo perchè nel linguaggio parlato esistono diversi significati e sfumature semantiche
	
-  Per ognuna di esse genera risposte o ipotesi plausibili
	
-  Infine raccoglie e valuta prove concorrenti per ogni plausibile risposta e gli associa un punteggio di affidabilità

Un sistema DeepQA deve rispettare questi quattro parametri:

- Risposte Precise: identificare l''oggetto della domanda e rispondere in maniera specifica
	
- Confidenza Accurata: si deve auto valutare per determinare la probabilità che la risposta fornita sia valida
	
- Giustificazione Fruibili: deve saper spiegare il motivo di una risposta, mostrando le prove a supporto di essa
	
- Tempi di Risposta Rapidi: il sistema deve rispondere entro pochi secondi

## Motore di ricerca vs Sistema DeepQA

Ci sono differenze sostanziali tra l'utilizzo di un motore di ricerca tradizionale e un sistema di DeepQA.
In un sistema basato su motore di ricerca, il carico cognitivo grava quasi interamente sull'utente, il quale deve riassumere la domanda in poche parole chiave, analizzare le fonti fornite sotto forma di link, estrarre una risposta e trovare le prove da solo. Il motore di ricerca si limita quindi soltanto a fornire un elenco di documenti, spesso neanche ordinati per rilevanza ma per popolarità.
In un sistema di DeepQA, invece, l'utente deve solo formulare una domanda in linguaggio naturale. Il sistema genera possibili risposte, analizza prove a conferma di ognuna di esse, gli associa un punteggio di confidenza e infine fornisce la risposta.

## Il framework UIMA

Dal punto di vista implementativo, DeepQA si basa sul framework UIMA (Unstructured Information Management Architecture).
UIMA è uno standard aperto che serve per poter combinare diversi strumenti di analisi di dati non strutturati (testi, audio, video, ecc).
Il workflow di DeepQA si articola in varie fasi:

- Question Analysis: identifica tipo di domanda e focus
	
- Decomposition: suddivide la domanda in sotto problemi più semplici
	
- Hypothesis Generation (o Candidate Answer Generation): produce risposte plausibili attingendo a dati strutturati e non strutturati
	
- Hypothesis and Evidence Scoring: cerca prove a supporto e confutazione di ogni tesi. Ogni algoritmo assegna un punteggio basato su varie metriche.
	
- Synthesis: tutti i punteggi vengono combinati e pesati attraverso dei modelli di apprendimento automatico

- Final Confidence Merging & Ranking: il risultato è una lista di risposte classificate, vince la risposta con punteggio maggiore

