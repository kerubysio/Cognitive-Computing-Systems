MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 13/03/2026
FONTE: Videolezione (slide screenshottate)
RIASSUNTO: [[Lezione 1 - Riassunto]]

---
![[Pasted image 20260401204112.png|697]]
![[Pasted image 20260401204420.png|695]]
Vogliamo capire se la sagoma è un sopravvissuto a cui mandare dei soccorsi oppure no.
In questo caso si parla di "Problema cognitivo", perchè bisogna prendere delle decisioni sotto incertezza assumendosi le responsabilità delle scelte intraprese.

![[Pasted image 20260401205102.png]]
Questa slide evidenzia la distinzione concettuale tra i processi di elaborazione dati e la vera cognizione, sottolineando che la **percezione** (o la capacità di un modello di identificare pattern) non equivale alla **conoscenza** profonda. Il concetto viene declinato su tre livelli: la capacità di fare previsioni non implica una reale comprensione del fenomeno, l'automazione di un compito non coincide con l'intelligenza e, soprattutto, un'elevata accuratezza statistica non garantisce l'affidabilità del sistema in ogni contesto. In sintesi, viene lanciato un monito critico nel valutare i sistemi AI, ricordando che l'eccellenza nelle prestazioni numeriche non deve essere confusa con una consapevolezza di tipo umano o con una sicurezza assoluta del modello.

![[Pasted image 20260401205348.png]]
Un **Cognitive Computing System (CCS)** è un sistema di intelligenza artificiale progettato per simulare i processi di pensiero umani nel risolvere problemi complessi. A differenza dei software tradizionali, un CCS non si limita a eseguire istruzioni rigide, ma è in grado di **integrare dati provenienti da fonti diverse**, ragionare anche in situazioni di **incertezza** e imparare costantemente dall'esperienza per adattarsi a nuovi scenari.
La sua particolarità risiede nella capacità di interagire in modo naturale con l'uomo e di saper **giustificare le proprie decisioni (explainability)**, rendendo il processo tecnologico più trasparente e collaborativo.
Una differenza fondamentale tra la pipeline ML e un sistema CCS sta proprio nel fatto che il primo agisce in maniera black box, mentre il secondo è sempre capace di spiegare i ragionamenti dietro alle proprie scelte.

![[Pasted image 20260402123659.png]]
L'**Architettura Cognitiva Completa** descrive il flusso logico attraverso cui un sistema intelligente elabora le informazioni.
Partendo dal **Sensing** per l'acquisizione dei dati grezzi, questo percorso evolve attraverso diverse fasi cruciali: la **Percezione** interpreta gli stimoli, la **Rappresentazione** li organizza in modelli concettuali, e il **Ragionamento** (Reasoning) permette di trarre conclusioni logiche. Il processo è alimentato dal continuo **Apprendimento** (Learning), che porta alla fase di **Decisione** finale.
Fondamentale è la presenza di un **Trust Layer**, ossia un componente architetturale che si interpone tra i modelli di intelligenza artificiale e l'applicazione finale rivolta all'utente.
Il suo scopo è mitigare i rischi intrinseci dei modelli probabilistici, garantendo che le interazioni e le decisioni generate dal sistema siano sicure, accurate, etiche e conformi alle normative. Agisce come un filtro bidirezionale, controllando sia i prompt in entrata (ad esempio per impedire che il sistema elabori dati personali immessi dall'utente) che le risposte in uscita (ad esempio per eliminare eventuali bias cognitivi e risposte potenzialmente offensive o che potrebbero mettere in pericolo l'utente finale).

![[Pasted image 20260402123923.png]]
Il **Modello Bayesiano della Cognizione** propone che l'intelligenza funzioni come un meccanismo di **inferenza probabilistica**. Secondo questa visione, la cognizione è un processo dinamico di aggiornamento delle conoscenze: il sistema parte da convinzioni preesistenti (**prior**), analizza nuovi dati o prove (**evidenze**) e integra queste informazioni per ottenere una visione aggiornata della realtà (**posterior**). In sostanza, il modello descrive la mente come una macchina statistica che impara e riduce l'incertezza attraverso il costante raffinamento delle probabilità basato sull'esperienza.

![[Pasted image 20260402124123.png]]
Il **Modello Bayesiano** ridefinisce la cognizione non come una classificazione statica (assegnazione di etichette), ma come un **aggiornamento continuo di credenze** espresse in termini probabilistici. In questo paradigma, l'incertezza non è un errore da eliminare, ma una variabile gestita esplicitamente: il sistema modula la propria fiducia in un'ipotesi man mano che riceve nuove informazioni.
Questo approccio costituisce il fondamento dei sistemi cognitivi moderni, poiché permette loro di operare in ambienti reali complessi dove i dati sono spesso ambigui o incompleti, garantendo una flessibilità decisionale simile a quella umana.

![[Pasted image 20260402124224.png]]
Il **Teorema di Bayes** fornisce la base matematica per il ragionamento sotto incertezza nei sistemi cognitivi, permettendo di calcolare la probabilità di un'ipotesi ($H$) alla luce di un nuovo dato osservato ($D$). La formula

$$P(H|D) = \frac{P(D|H)\cdot P(H)}{P(D)}$$

esprime come la credenza aggiornata (**Posterior**) derivi dal prodotto tra la probabilità iniziale (**Prior**) e la verosimiglianza del dato osservato rispetto all'ipotesi (**Likelihood**), normalizzato per la probabilità totale del dato stesso. Questo meccanismo di **aggiornamento delle credenze** trasforma il calcolo statistico in un vero e proprio processo di apprendimento continuo, dove ogni nuova evidenza raffina o corregge la comprensione del mondo da parte del sistema.

![[Pasted image 20260402124601.png]]
L'equazione **Cognizione = Stato di Credenze** esprime l'idea che un sistema cognitivo non abbia mai un accesso diretto e assoluto alla realtà oggettiva, ma operi esclusivamente attraverso **distribuzioni di probabilità**. In questo modello, il sistema osserva solo segnali rumorosi o parziali e deve **inferire lo stato più plausibile** del mondo esterno.
Di conseguenza, ogni scelta non avviene sulla realtà stessa, ma in uno **spazio delle credenze** (_belief space_): il sistema prende decisioni basandosi sulla configurazione di probabilità che ritiene più verosimile in quel momento, accettando intrinsecamente il margine di incertezza tipico di ogni processo cognitivo avanzato.

![[Pasted image 20260402124736.png]]
L'**aggiornamento della credenza** è il cuore operativo del ciclo cognitivo, dove il valore calcolato come **Posterior** smette di essere un semplice risultato numerico e diventa il **nuovo stato cognitivo** del sistema. Attraverso la formula che combina la conoscenza pregressa (**Prior**) con la verosimiglianza dei nuovi dati (**Likelihood**) normalizzati rispetto all'**Evidenza**, il sistema evolve la propria percezione della realtà. Questo processo garantisce la continuità dell'apprendimento: ciò che era una conclusione in un determinato momento (il _posterior_ attuale) fungerà da punto di partenza (la _prior_ futura) per l'osservazione successiva, permettendo al sistema di affinare costantemente la propria interpretazione del mondo.

![[Pasted image 20260402124843.png]]
La **visualizzazione dell'aggiornamento bayesiano** mostra graficamente la relazione tra la verosimiglianza del dato osservato (**Likelihood**) e la nuova credenza generata (**Posterior**). La curva illustra come, all'aumentare della probabilità che un certo dato $D$ sia spiegato dall'ipotesi $H$, cresca proporzionalmente la fiducia del sistema in quell'ipotesi stessa. In un sistema cognitivo, questo grafico rappresenta la sensibilità del modello all'evidenza: una pendenza più o meno ripida indica quanto velocemente il sistema è disposto a cambiare idea o a consolidare la propria "certezza" in risposta a ciò che osserva nell'ambiente.

![[Pasted image 20260402125015.png]]
Un sistema intelligente produce credenze, non risposte.

![[Pasted image 20260402125146.png]]
Perchè è cruciale questo ragionamento ora?
Perchè quantifica l'incertezza.

La capacità di produrre **credenze probabilistiche** anziché risposte rigide è fondamentale nei sistemi **safety-critical** (come la guida autonoma o la diagnostica medica) perché permette di **quantificare l'incertezza**. In questi contesti, sapere quanto il sistema sia "insicuro" della propria percezione è vitale per supportare decisioni basate sull'**utilità attesa**, dove si bilanciano i rischi e i benefici di ogni azione. Questa trasparenza statistica costituisce la base per la **certificazione** e la costruzione della fiducia (_trust_): un sistema che ammette il proprio grado di dubbio è intrinsecamente più sicuro di uno che fornisce risposte binarie potenzialmente errate, permettendo interventi umani o procedure di sicurezza quando la fiducia nell'ipotesi scende sotto una soglia critica.

![[Pasted image 20260402125432.png]]
Il modello bayesiano è considerato un vero **modello di cognizione** perché non si limita a catalogare i dati, ma implementa una **dinamica epistemica**, ovvero una gestione attiva della conoscenza. A differenza della classificazione statica, questo approccio introduce l'**incertezza esplicita** come parte integrante del sistema, permettendo una **rappresentazione delle credenze** che evolve nel tempo. Attraverso l'**apprendimento incrementale** e la costante **revisione delle convinzioni** alla luce di nuove prove, il sistema simula il modo in cui una mente intelligente aggiorna la propria comprensione del mondo, rendendo il pensiero un processo fluido di adattamento continuo anziché una serie di risposte pre-programmate.

![[Pasted image 20260402125638.png]]
Il prof non ha detto molto su questa slide, non approfondire.

![[Pasted image 20260402125719.png]]
La differenza fondamentale tra il **Deep Learning (DL) classico** e la **Cognizione Bayesiana** risiede nella gestione della conoscenza interna. Il DL puro opera generalmente come una funzione matematica che mappa direttamente un input a un output (ad esempio, un'immagine alla sua etichetta), focalizzandosi sulla precisione del risultato finale.
Al contrario, la cognizione bayesiana mantiene uno **stato interno di credenze** espresso tramite **distribuzioni di probabilità** che vengono costantemente aggiornate.
Sebbene una rete neurale possa stimare una probabilità $P(H|D)$, essa spesso manca di un **modello probabilistico completo** ed esplicito; l'approccio bayesiano, invece, integra nativamente l'**incertezza**, permettendo al sistema non solo di dare una risposta, ma di "sapere di non sapere" e di motivare il proprio grado di fiducia in ogni specifica situazione.

![[Pasted image 20260402130026.png]]

L'affermazione **"Un sistema intelligente non produce risposte, produce credenze aggiornate"** rappresenta il superamento dell'AI come semplice calcolatore deterministico verso una **Explainable AI (XAI)** basata sulla fiducia. In questo paradigma, l'intelligenza non è definita dall'assenza di errore, ma dalla capacità di fornire una **giustificazione comprensibile** (credenza) del proprio output. Mentre un modello "black box" fornisce una risposta secca che può risultare inaffidabile o generare allucinazioni (risposte false ma convincenti), un sistema intelligente collabora con l'uomo contestualizzando le proprie conclusioni. Questo permette all'utente di identificare eventuali **bias** o limiti logici del modello, rendendo l'errore un evento gestibile e comprensibile invece di un fallimento inspiegabile del sistema.
Se l'errore è trasparente e comprensibile, l'IA non agisce come un oracolo sempre corretto, ma come un assistente.

"Perchè è cruciale avere sistemi con un approccio cognitivo moderno?"
![[Pasted image 20260402130337.png]]

![[Pasted image 20260402130504.png]]
L'**Architettura di un Sistema Cognitivo Moderno** è organizzata in cinque livelli gerarchici che trasformano i dati grezzi in decisioni affidabili.
Si parte dal **Data Layer** (sensori e streaming multimodale) e dal **Perceptual Layer**, dove tecnologie come CNN e LLM interpretano i segnali.
Il cuore del sistema è il **Cognitive Layer**, che utilizza Knowledge Graphs e Reti Bayesiane per il ragionamento simbolico e neurale (Hybrid AI).
Segue il **Decision Layer**, che pianifica le azioni tramite Reinforcement Learning e modelli MDP (lo vedremo più avanti).
Infine, il **Trust Layer** garantisce la sicurezza attraverso l'**Explainability** (SHAP, LIME), la robustezza agli attacchi e il rispetto dei vincoli etici, rendendo l'intero sistema trasparente e certificabile.

![[Pasted image 20260402130716.png]]

Facciamo un breve cenno a ciascuna di queste nelle prossime slides.

![[Pasted image 20260402130857.png]]
(Formula spiegata meglio nelle slides successive)
La **Teoria dell'Utilità** rappresenta il passaggio dalla pura conoscenza all'azione: Bayes risponde alla domanda "Cosa credo sia vero?", mentre la decisione ottimale risponde a "**Cosa mi conviene fare?**".
Formalmente, l'azione ottima $a^*$ è quella che massimizza l'utilità attesa. Questo approccio è cruciale in ambiti come quello aerospaziale o nel _mission planning_, dove non si massimizza la semplice probabilità di successo, ma la **sopravvivenza** (safety > efficienza).
Attraverso il trade-off tra rischi e benefici e l'applicazione delle equazioni di Bellman, il sistema agisce come un co-pilot cognitivo capace di scegliere la rotta che garantisce il miglior equilibrio tra il raggiungimento dell'obiettivo e la mitigazione dei pericoli.

![[Pasted image 20260402174351.png]]
![[Pasted image 20260402174408.png]]
L'operatore **$\text{arg max}$** (abbreviazione di _argument of the maximum_) indica il valore dell'argomento $a$ per il quale la funzione raggiunge il suo valore massimo. Mentre il "max" di una funzione restituirebbe il valore dell'utilità più alta possibile, l'**$\text{arg max}$** restituisce l'**azione specifica** ($a^*$) che permette di ottenere quel risultato.
In termini pratici, dopo aver calcolato l'utilità attesa per ogni possibile azione disponibile, il sistema seleziona e mette in atto quella che garantisce il valore statistico migliore, trasformando il calcolo probabilistico in un comportamento concreto e razionale.

![[Pasted image 20260402174545.png]]

L'**interpretazione della decisione ottima** chiarisce che il sistema non sceglie l'azione semplicemente più probabile, ma quella che massimizza il valore atteso delle conseguenze. Nel calcolo dell'**utilità attesa**, la probabilità $P(s|a)$ rappresenta l'incertezza sul mondo, mentre $U(s)$ quantifica il valore (o il costo) di ciò che accadrebbe in quello stato.

![[Pasted image 20260402174722.png]]

L'esempio del drone da soccorso è emblematico: anche con una probabilità del **30%** di un falso positivo (un errore), l'utilità attesa di inviare la squadra resta elevata (**67**) perché il valore di salvare una vita (+100) sovrasta di gran lunga il costo del falso allarme (-10). Questo **criterio razionale** permette al sistema di agire in modo etico e sicuro, bilanciando matematicamente i rischi di un errore con i benefici di un successo critico.

![[Pasted image 20260402174755.png]]

![[Pasted image 20260402174817.png]]

![[Pasted image 20260402174846.png]]
La **differenza tra Machine Learning (ML) classico e decisione ottimale** segna l'evoluzione del sistema da un semplice strumento di previsione a un'entità con **decision authority**.
Mentre un classificatore standard punta unicamente a minimizzare l'errore medio (accuratezza statistica), la decisione ottimale mira a **massimizzare l'utilità attesa** (o minimizzare il costo atteso), tenendo conto dell'**asimmetria del rischio**.
In contesti reali, non tutti gli errori hanno lo stesso peso: ignorare un ostacolo sulla strada è infinitamente più grave che frenare inutilmente. Questo approccio permette al sistema di non essere solo "preciso", ma "saggio", poiché valuta le conseguenze delle proprie azioni in base al contesto e alla sicurezza, trasformando la pura _prediction_ in una scelta strategica e responsabile.

![[Pasted image 20260402175022.png]]

![[Pasted image 20260402175132.png]]
Questa slide illustra l'applicazione dell'**Utilità Attesa in uno scenario di emergenza** (guasto motore in volo), mettendo a confronto due strategie decisionali. Il grafico 3D mostra come l'**Utilità Attesa (asse Z)** vari in base alla **Probabilità di guasto (asse X)** e alla scala di valori assegnati alle conseguenze (**asse Y**). La linea blu rappresenta l'atterraggio immediato, che mantiene un'utilità stabile e sicura, mentre la linea arancione rappresenta il continuare il volo, la cui utilità crolla drasticamente all'aumentare della probabilità di guasto.
Il punto in cui le due linee si incrociano definisce la **soglia decisionale**: oltre quel livello di incertezza, il sistema razionale "scatta" e impone l'atterraggio, dimostrando come la decisione non dipenda solo dal rischio calcolato, ma dal peso catastrofico assegnato a un possibile fallimento del motore.

![[Pasted image 20260402175252.png]]

---

![[Pasted image 20260402175603.png]]

Il **Markov Decision Process (MDP)** è lo schema matematico fondamentale che permette a un'intelligenza artificiale di prendere **decisioni sequenziali** in ambienti dinamici e incerti. Un MDP è definito da cinque elementi chiave: gli **Stati (S)**, che rappresentano la situazione attuale del mondo; le **Azioni (A)**, ovvero le scelte a disposizione del sistema; le **Transizioni (P)**, che definiscono la probabilità di passare da uno stato all'altro in seguito a un'azione; il **Reward (R)**, il premio o la penalità immediata ottenuta; e infine il **Discount ($\gamma$)**, un coefficiente che stabilisce quanta importanza dare alle ricompense future rispetto a quelle presenti. Questo modello trasforma il problema della scelta in un calcolo di ottimizzazione, dove l'obiettivo non è solo ottenere un vantaggio immediato, ma massimizzare il valore complessivo nel lungo periodo.

Il cuore operativo di un sistema basato su MDP è l'**Equazione di Bellman**:
$$V(s) = max_a \left[R(s,a) + \gamma \sum P(s'|s,a)V(s')\right]$$
N.B.: in questa formula $P(s'|s,a)$ indica la funzione di transizione, ossia la probabilità di spostarsi in $s'$ tramite l'azione $a$ partendo dallo stato s (s' condizionato s dato a).
Questa formula ci permette di calcolare il **Valore di uno stato $V(s)$** come la somma tra la ricompensa immediata e il valore atteso delle ricompense di tutti gli stati futuri raggiungibili, opportunamente scontati dal fattore $\gamma$.
In altre parole, sistema non valuta l'azione in isolamento, ma ne proietta le conseguenze nel tempo per determinare la **Politica Ottimale $\pi^*$**, ovvero la strategia migliore da seguire in ogni possibile situazione.

Nel **Reinforcement Learning**, il sistema apprende questa politica interagendo con l'ambiente e correggendo i propri errori. In ambiti critici come l'assistenza al volo (_Pilot Assistance_), questo approccio è vitale: permette all'IA di scartare manovre che sembrano sicure nel presente ma che potrebbero portare a situazioni di pericolo estremo in fasi successive della missione, garantendo una pianificazione strategica orientata alla sopravvivenza globale.

![[Pasted image 20260402180106.png]]

Il **POMDP (Partially Observable Markov Decision Process)** rappresenta l'estensione realistica dei modelli decisionali classici, applicata a contesti in cui il sistema non ha mai una conoscenza perfetta e completa dello stato del mondo. A differenza di un MDP standard, dove si assume di conoscere esattamente la situazione attuale, in un POMDP l'agente riceve solo osservazioni parziali o rumorose (ad esempio, sensori disturbati o visibilità ridotta). Questo costringe il sistema a non operare sulla realtà diretta, ma all'interno dello **spazio delle credenze (_Belief Space_)**.
Invece di decidere basandosi su uno "stato certo", il sistema decide basandosi su una distribuzione di probabilità che descrive quanto sia verosimile trovarsi in ciascuno dei possibili stati del mondo.

Il cuore del POMDP è il **Belief Space $b(s)$**, che sintetizza l'intera storia delle azioni passate e delle osservazioni ricevute. Ogni volta che il sistema compie un'azione e ottiene un nuovo dato, esegue un **aggiornamento bayesiano** per calcolare la nuova credenza $b'(s')$. 
Formalmente, questa operazione combina la probabilità di transizione (dove il sistema pensa di essere finito dopo l'azione) con la probabilità di osservazione (quanto il nuovo dato sia coerente con la posizione presunta).
La formula risultante permette alla "mente" artificiale di affinare costantemente la propria mappa interna delle probabilità. In questo modo, la cognizione diventa un processo dinamico dove ogni decisione viene presa pesando non solo l'utilità dell'azione, ma anche quanto quell'azione possa aiutare a ridurre l'incertezza futura sul mondo.
Il sistema non sa dove si trova, sa solo cosa crede.

![[Pasted image 20260402180615.png]]

Il **Deep Learning (DL) puro** presenta limiti strutturali critici per applicazioni ad alta responsabilità. Essendo basato principalmente sul riconoscimento di pattern e correlazioni tra dati, il DL opera come una **"scatola nera" (black-box)** opaca: è fragile quando si trova di fronte a situazioni mai viste prima (_out-of-distribution_) e manca di una reale capacità di ragionamento logico. In sostanza, un modello neurale classico può identificare un oggetto con estrema precisione, ma non "capisce" le regole fisiche o semantiche che governano quell'oggetto, rendendo difficile spiegare il perché di una determinata decisione.

Per superare queste fragilità, i moderni sistemi cognitivi adottano un **approccio neuro-simbolico**, che fonde la potenza percettiva delle reti neurali con il rigore della logica simbolica. Questa architettura si articola tipicamente su tre livelli:

1. **Reti Neurali**: dedicate alla percezione e all'estrazione di caratteristiche dai dati grezzi (immagini, suoni, testo).
    
2. **Knowledge Graphs**: utilizzati per rappresentare la struttura semantica del mondo e le relazioni tra i concetti, fornendo un "vocabolario" comprensibile.
    
3. **Reasoner Simbolico**: un motore logico che applica regole esplicite, vincoli e inferenze per guidare il processo decisionale.
    

L'introduzione della componente simbolica permette di passare dalla semplice individuazione di pattern alla **comprensione della struttura e della causalità**. In ambiti _safety-critical_, come l'aerospazio o la medicina, questo passaggio è fondamentale: garantisce che il sistema non sia solo performante, ma anche **spiegabile, controllabile e certificabile**, poiché ogni azione può essere ricondotta a una regola logica esplicita anziché a una mera correlazione statistica.

---

![[Pasted image 20260402180922.png]]

Il **Continual Learning** affronta la sfida di far apprendere a un sistema nuovi compiti nel tempo senza che questo perda le conoscenze acquisite in precedenza. Il problema principale è il cosiddetto **Catastrophic Forgetting** (oblio catastrofico), un fenomeno per cui l'aggiornamento dei pesi di una rete neurale per un nuovo obiettivo distrugge letteralmente le configurazioni necessarie a risolvere i compiti passati. Questo crea una tensione matematica tra **plasticità**, ovvero la capacità di integrare nuove informazioni, e **stabilità**, ovvero la capacità di conservare la memoria storica.
In contesti reali, questa sfida è aggravata dal **Distribution Shift**: il mondo non segue una distribuzione statistica fissa e immutabile, il che significa che i dati usati per l'addestramento iniziale potrebbero non riflettere più la realtà futura.

Per gestire questo equilibrio, si utilizzano tecniche come l'**Elastic Weight Consolidation (EWC)**. L'idea è quella di, quando si addestra la rete neurale ad una nuova task, penalizzare i cambiamenti ai parametri (pesi) identificati come "importanti" per i compiti precedenti, basandosi sulla **Fisher Information**.
In pratica, il sistema diventa "elastico": permette di modificare liberamente i pesi irrilevanti per imparare il nuovo compito, ma oppone resistenza quando si tenta di variare quelli fondamentali per la memoria storica. Questo approccio riconosce che "i dataset mentono" perché sono solo istantanee parziali di un mondo in continua evoluzione, e permette al sistema cognitivo di adattarsi incrementalmente senza dover ricominciare ogni volta da zero.

---

![[Pasted image 20260402181206.png]]

Il rischio legato ai dati **Out-of-Distribution (OOD)** nasce quando la distribuzione statistica su cui un modello è stato addestrato ($p_{train}(x)$) differisce significativamente dalla distribuzione dei dati che il modello incontra durante l'effettivo esercizio operativo ($p_{real}(x)$). Questo fenomeno, noto come **Distribution Shift**, invalida le stime di accuratezza effettuate durante la fase di test: un sistema può mostrare un'accuratezza media elevatissima in laboratorio, ma fallire catastroficamente nel mondo reale perché si trova ad operare in una regione dello "spazio delle feature" che non ha mai esplorato. In contesti critici, questo dimostra che l'accuratezza statistica non è sinonimo di sicurezza operativa.

Traslando questo concetto al contesto aeronautico, si può immaginare un sistema di assistenza all'atterraggio addestrato esclusivamente su piste asciutte. Quando il velivolo si trova ad operare su una pista bagnata o ghiacciata, le leggi fisiche (coefficienti di attrito, spazi di frenata) cambiano. Per il sistema, questi dati sono "fuori distribuzione": non sono semplici errori medi, ma situazioni strutturalmente diverse che il modello non è in grado di interpretare correttamente.

Il problema non è quanto il sistema sbagli in media, ma il fatto che non "sa di non sapere", continuando a fornire risposte basate su una realtà ($p_{train}$) che non corrisponde più a quella attuale ($p_{real}$).

![[Pasted image 20260402181451.png]]

L'**Explainability Formale** raggruppa le metodologie matematiche utilizzate per rendere comprensibili le decisioni di modelli di intelligenza artificiale complessi, trasformandoli da "black box" a sistemi trasparenti. Due delle tecniche più diffuse sono SHAP e LIME.

- **SHAP (Shapley Additive Explanations)** si basa sulla teoria dei giochi e assegna a ogni variabile (feature) un valore che rappresenta il suo contributo marginale specifico alla predizione finale; in sostanza, quantifica quanto il valore di quella variabile abbia spostato l'output rispetto alla media.

- **LIME (Local Interpretable Model-Agnostic Explanations)**, invece, funziona isolando una singola decisione specifica. Poiché il modello globale è troppo complicato da spiegare tutto insieme, LIME analizza solo quello che succede "vicino" al dato scelto, costruendo lì accanto un modello molto più semplice e leggibile che imita il comportamento di quello complesso solo per quel caso particolare.

Entrambe rientrano nella categoria della **Feature Attribution**, ovvero tecniche che isolano e misurano l'impatto di ogni variabile sull'output del sistema.

Esiste tuttavia una distinzione cruciale tra **Modello Interpretabile** e **Spiegazione Post-Hoc**. Un modello è intrinsecamente interpretabile quando la sua struttura stessa è trasparente e comprensibile per l'uomo (come una regressione lineare o un albero di decisione semplice). Al contrario, la spiegazione post-hoc interviene su modelli opachi e complessi (come le reti neurali profonde) cercando di estrarre una giustificazione logica dopo che l'output è stato generato. Nei sistemi **safety-critical**, questa differenza è vitale: affidarsi a una spiegazione post-hoc significa analizzare un'approssimazione del ragionamento del sistema, mentre un modello interpretabile offre una garanzia di trasparenza totale sulla logica decisionale applicata.

