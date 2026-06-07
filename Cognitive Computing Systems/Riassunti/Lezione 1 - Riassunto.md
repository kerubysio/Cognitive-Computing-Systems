Originale: [[Lezione 1]]
Utile: https://www.ibm.com/think/topics/cognitive-computing-vs-ai

---
## Introduzione

Un Cognitive Computing System (CCS) è un sistema di intelligenza artificiale il cui obiettivo è quello di imitare il cervello umano.
È capace di integrare fonti eterogenee, ragionare sotto incertezza e imparare dall'esperienza.
Riesce ad interagire in maniera naturale con l'uomo, ed è capace di spiegare i suoi processi di ragionamento (explainability vs black-box delle AI tradizionali).
Pertanto ogni risposta è accompagnata da un valore di affidabilità che indica quanto il sistema è sicuro della risposta data.

Una pipeline cognitiva è costituita da questi passaggi:
- **Sensing**: raccolta di dati grezzi
- **Perception**: interpretazione dei dati
- **Representation**: incapsulo i dati in modelli
- **Reasoning**: ragionamento sui dati
- **Learning**: apprendimento
- **Decision**: decisione finale
- **Trust Layer**: controlla la sicurezza dei prompt in input e delle risposte in output

## Il modello Bayesiano della Cognizione

Il modello Bayesiano della cognizione propone che l'**intelligenza** funzioni come un processo di **inferenza probabilistica**: si parte da alcune convinzioni di base, dette **prior**, dopodiché si raccolgono nuovi **dati o prove**, grazie ai quali viene effettuato un processo di inferenza che ci permette di giungere ad una nuova conoscenza, detta **posterior**.

>La **cognizione** è **aggiornamento di credenze** (e delle relative probabilità)

Grazie a questo nuovo approccio un CCS può operare in ambienti reali, dove non esiste una soluzione o risposta univoca e i dati sono spesso ambigui.
#### Teorema di Bayes

Il Teorema di Bayes ci permette di calcolare la probabilità di un'ipotesi H alla luce di un nuovo dato osservato D:

> $$
P(H|D) = \frac{P(D|H)\cdot P(H)}{P(D)}
$$

- $P(H|D)$ prende il nome di posterior.
- $P(H)$ si chiama prior.
- P(D|H) si chiama Likelihood (o verosimiglianza del dato rispetto all'ipotesi).
- P(D) si chiama evidenza.

Questa formula è il cuore operativo di un CCS, perchè mostra come il sistema aggiorna le proprie credenze nel tempo. Questo processo garantisce la continuità dell'apprendimento.
#### Belief state

La cognizione così descritta implica che un sistema cognitivo non possegga mai la verità dei fatti, ma basa il suo intero processo su un insieme di probabilità.

>Un sistema cognitivo non sa nulla, sa solo ciò che crede

Di conseguenza ogni scelta non avviene sulla realtà stessa, ma su quello che il sistema crede che sia lo stato di realtà.
Lo spazio delle credenze di un CCS prende il nome di **belief space**.

Proprio per questo, i CCS trovano ampio spazio nei sistemi safety-critical, dove è indispensabile che ogni risposta sia accompagnata da un grado di incertezza così da poter richiedere intervento umano se scende sotto una certa soglia.

Proprio il belief state costituisce la principale differenza tra CCS e Deep Learning. Un sistema DL, infatti, è più vicino ad una funzione matematica che mappa ad ogni input un output (ad ogni elemento del dataset associa un'etichetta) cercando di massimizzare l'accuratezza delle risposte, mentre un sistema cognitivo integra e rende parte fondamentale di sè proprio quell'incertezza che un sistema DL cerca di minimizzare (anche rischiando di fornire risposte sbagliate con convinzione).
### Explainable AI

>Un sistema intelligente non produce risposte.
>Produce credenze aggiornate.

Nel paradigma dell'Explainable AI (XAI), l'intelligenza è vista come la capacità di fornire una spiegazione per il proprio output.
Questo permette all'utente di riconoscere bias e allucinazioni, rendendo gli errori gestibili, in contrapposizione ai sistemi black box dove possono passare inosservati.
# Architettura cognitiva

- Data Layer: raccolta dei dati
- Perceptual Layer: analisi dei dati
- Cognitive Layer: ragionamento
- Decision Layer: decisione tramite Reinforcement Learning e modelli MDP
- Trust Layer: gestione della sicurezza (explainability e controlli input/output)
## Decisione ottimale - Teoria dell'Utilità

Ma come viene effettuata la decisione nel Decision Layer?
Per rispondere a questa domanda, si utilizza la Teoria dell'Utilità.

>Secondo la Teoria dell'Utilità, l'azione ottima a* è quella che massimizza il guadagno atteso.

Nei sistemi safety critical, ad esempio, un CCS agisce come un co-pilot cognitivo che si occupa di effettuare un continuo trade-off tra rischi e benefici cercando di raggiungere l'obiettivo senza intraprendere rischi eccessivi. Ogni scelta tiene conto delle conseguenze.

#### Calcolo dell'azione ottimale

Per calcolare l'azione ottima si applicano le equazioni di Bellman:

> $a^* = arg\underset{a}{max} \sum_s P(s|a) \cdot U(s)$

- $arg max$ indica il valore di a per il quale la funzione raggiunge il valore massimo
- a è una generica azione
- $a^*$ è l'azione ottima
- s è uno stato generico
- P(s|a) è la probabilità di s condizionato a
- U(s) è l'utilità associata al valore

>Non scelgo l'azione più probabile ma quella che massimizza il valore atteso.

Un sistema ML sceglierebbe sempre l'azione più probabile, ossia quella con più chance di farci raggiungere l'obiettivo, ignorando eventuali rischi.

### Markov Decision Process (MDP)

Tuttavia un sistema cognitivo non può prendere decisioni basandosi solo su quale sia l'azione ottima ad un certo istante, ma ogni azione deve fare parte di un piano d'azione. Detto in altre parole, un sistema di intelligenza artificiale deve prendere decisioni sequenziali in ambienti dinamici e incerti.

Per fare ciò, ci si appoggia al Markov Decision Process, il quale si basa su:
- Stati (S): situazione attuale del mondo
- Azioni (A): insieme delle possibili scelte
- Transizioni (P): **probabilità** che un'azione ci porti in un certo stato
- Reward (R): premio o penalità successivo ad un'azione
- Discount ($\gamma$): coefficiente che pesa l'importanza delle ricompense future rispetto a quelle attuali

Questo modello trasforma il processo di scelta in un calcolo di ottimizzazione.
L'MDP si basa sulle equazioni di Bellman:

>$$V(s) = max_a \left[R(s,a) + \gamma \sum P(s'|s,a)V(s')\right]$$

In cui il valore di uno stato V(s) viene espresso dalla somma del guadagno attuale con la sommatoria, pesata da gamma, dei prodotti tra la funzione di transizione e il valore atteso di tutti i futuri stati raggiungibili.
P(s'|s, a) indica la probabilità che arrivi nello stato s' se intraprendo l'azione a partendo da s (si legge "s' condizionato da s, dato a").
#### Partially Observable Markov Decision Process (POMDP)

In un MDP classico si assume di conoscere la realtà al 100%.
Nei sistemi incerti, si usa il Partially Observable Markov Decision Process, in cui si parte dall'assunto che i dati siano incompleti o disturbati (sensori mancanti, rotti o imperfetti). Viene utilizzato per i sistemi che si basano sul belief space, come i CCS.

Il cuore del POMDP è il Belief Space, il quale contiene tutta la storia delle azioni passate intraprese e delle osservazioni ricevute. Dopo ogni azione, se il sistema riceve un nuovo dato, esegue un aggiornamento bayesiano che gli permette di aggiornare il belief space.
## Il Continual Learning

Il Continual Learning cerca di fare in modo che un sistema cognitivo apprenda nuove task e informazioni senza però dimenticare quelle precedenti, fenomeno noto come Catastrophic Forgetting. Quello che accade è che l'aumento dei pesi di una rete neurale causa la sovrascrizione dei pesi preesistenti, distruggendo così le connessioni necessarie a svolgere i compiti precedenti.

Per ovviare a questo problema si applica l'Elastic Weight Consolidation (EWC), che consiste nel penalizzare il cambiamento di parametri ritenuti fondamentali durante la fase di addestramento del sistema ad una nuova task.

## Dati Out-Of-Distribution (OOD)

Il problema di dati Out-Of-Distribution si verifica quando la distribuzione statistica dei dati di addestramento differisce significativamente dai dati su cui il sistema opera in applicazioni reali.
### Explainability Formale

L'Explainability Formale raggruppa le tecniche che consentono ad un sistema di 
agire in maniera trasparente, le più diffuse sono:

- SHAP (Shapely Additive Explanations): assegna ad ogni feature un valore che rappresenta il suo contributo all'output finale;
- LIME (Local Interpretable Model-Agnostic Explanations): funziona isolando una scelta specifica e linearizzando il modello attorno ad essa, ossia creando un modello semplificato che ci aiuti a capire come funziona quello vero.

Bisogna fare però una distinzione cruciale tra Modello Interpretabile e Spiegazione Post-Hoc. Un modello è intrinsecamente interpretabile quando la sua struttura è fatta in modo tale da essere comprensibile per un uomo, mentre si dice che è un modello post-hoc se inizialmente è black-box o opaco, e solo in un secondo momento viene prodotta una spiegazione dell'output generato.