MATERIA: [[Cognitive Computing Systems (6 CFU)]]
DATA: 24/04/2026

---

Prima della pausa ci ha fatto vedere dove si trovano i capitoli del libro che usa per spiegare python. [In questa cartella](https://communitystudentiunina.sharepoint.com/:f:/r/sites/COGNITIVECOMPUTINGSISTEMSFG23-24/Materiale%20del%20corso/INTRO%20TO%20PHYTON%20FOR%20COMPUTER%20SCIENCE%20AND%20DATA%20SCIENCE/Deitel%20Python%20Slides/Slides/IntroToPythonSlides/) trovi i vari capitoli.
Se il link non funziona, vai in questo teams:
[General | COGNITIVE COMPUTING SISTEMS SG 24-25](https://teams.microsoft.com/l/team/19%3A6xFyplE90KLyT6xka8sVhE0BaI54eLzhiYHy9addyHI1%40thread.tacv2/conversations?groupId=badc11a0-b44d-4cf7-b166-2a4ca209563d&tenantId=2fcfe26a-bb62-46b0-b1e3-28f9da0c45fd)

Nel materiale condiviso ci sta una cartella verde "INTRO TO PHYTON FOR COMPUTER SCIENCE AND DATA SCIENCE", la apri e vai su "Deitel Python slides", poi "Slides" fino a trovare dei file che si chiamano "ch".
Per visualizzare i capitoli devi (a quanto ho capito) scaricare tutta quanta la cartella e aprirla in anaconda.

Oggi ha letto i capitoli su media, mediana e moda in python, tuple e dizionari. Ossia da 4 a 9.
Qualcuno lo ha saltato, non ricordo quale. In ogni caso serve solo come ripasso, non sono cose che chiederà al corso. Servono solo per sviluppare programmi CCS in futuro.
La seconda parte della lezione è un'introduzione al Machine Learning.

---

![[Pasted image 20260525190826 1.png]]
Alla base del funzionamento del machine learning risiede un quesito fondamentale: è possibile fare in modo che i computer apprendano? La componente chiave che rende realizzabile questo processo è la disponibilità dei **dati**, preferibilmente in grandi volumi.

Il cambio di paradigma rispetto allo sviluppo software classico si articola su principi precisi:

- **Inversione della logica di programmazione:** Invece di codificare manualmente regole rigide basate sull'esperienza e sulla competenza umana all'interno del software, si istruisce l'algoritmo affinché estragga autonomamente tali regole a partire dall'analisi dei dati storici.
    
- **Costruzione dei modelli:** Il workflow si concentra sullo sviluppo di modelli di machine learning che, una volta addestrati sui campioni di riferimento, possono essere impiegati per generare predizioni future dotate di un elevato livello di accuratezza.

![[Pasted image 20260525191105.png]]
![[Pasted image 20260525191154.png]]
Questi sono alcuni dei principali casi d'uso.

![[Pasted image 20260525191308.png]]
La libreria **Scikit-learn** (nota anche nel codice come **sklearn**) organizza e distribuisce i principali algoritmi di machine learning sotto forma di oggetti standardizzati chiamati **estimatori** (_estimators_).

L'interfaccia si basa su una filosofia di programmazione ben definita:

- **Incapsulamento logico:** Ogni algoritmo è isolato all'interno di un'architettura ad alto livello. Questo design nasconde i dettagli implementativi e la complessa struttura matematica sottostante, permettendo allo sviluppatore di concentrarsi sul flusso dei dati anziché sui calcoli algebrici interni.
    
- **Efficienza del codice:** Utilizzando scikit-learn e una quantità ridotta di codice Python, è possibile strutturare modelli robusti per analizzare i dati, estrarre metriche informative e generare predizioni accurate.

Il workflow operativo standard prevede la segmentazione delle fasi di sviluppo in passaggi sequenziali:

- **Addestramento (_Training_):** Il modello viene inizialmente allenato su un sottoinsieme controllato del dataset (il _training set_) per permettere all'algoritmo di estrarre le relazioni statistiche tra le feature.
    
- **Verifica (_Testing_):** Le prestazioni del modello vengono valutate sulla parte rimanente dei dati (il _test set_) per quantificarne la precisione matematica.
    
- **Predizione e inferenza:** Una volta convalidati, i modelli vengono distribuiti in produzione per elaborare flussi di dati mai visti prima, consentendo al computer di assumere comportamenti intelligenti e predittivi automatizzando task che un tempo richiedevano procedure meccaniche e ripetitive.

![[Pasted image 20260525191519.png]]
La selezione dell'estimatore ottimale all'interno dell'ecosistema Scikit-Learn segue un approccio prettamente empirico. Poiché le astrazioni di `sklearn` nascondono i dettagli algebrici complessi, la scelta del modello si affina con l'esperienza, sviluppando una familiarità legata alle caratteristiche intrinseche del problema e alla dimensionalità del dataset.

Tuttavia, anche accumulando un'elevata competenza sul campo, è statisticamente improbabile riuscire a intuire a priori quale sia l'algoritmo perfetto per ogni nuovo set di dati non ancora analizzato. Per superare questo limite, Scikit-Learn adotta una filosofia orientata alla sperimentazione di massa (_"try 'em all"_):

- **Interfaccia unificata:** Grazie alla standardizzazione dei metodi (come l'adozione universale di `.fit()` per l'addestramento e `.predict()` per l'inferenza), l'architettura permette di sostituire o iterare rapidamente tra decine di algoritmi diversi scrivendo pochissime linee di codice supplementari.
    
- **Valutazione comparativa:** Ciascun modello restituisce metriche oggettive e standardizzate sulle proprie prestazioni. Questo comportamento trasparente consente di confrontare i risultati analitici dei vari candidati in modo sistematico, isolando l'algoritmo (o l'insieme di modelli) che dimostra la capacità di generalizzazione più elevata sul problema specifico.

![[Pasted image 20260525191704.png]]
Nel machine learning, i problemi di natura predittiva si dividono in due macro-categorie fondamentali, distinte in base alla tipologia della variabile target che si desidera stimare: Supervised Machine Learning e Unsupervised Machine Learning.

Nell'ambito del Supervised Machine Learning parliamo principalmente di:
- **Regressione (_Regression_):** Si applica quando l'obiettivo è prevedere un valore numerico continuo. In questo scenario, le risposte fornite dal modello non appartengono a categorie fisse, ma si muovono lungo una scala quantificativa infinita (es. stimare il prezzo di un'abitazione in base alla superficie, o calcolare il valore esatto della temperatura di domani).
    
- **Classificazione (_Classification_):** Si applica quando la variabile target è di tipo categoriale o discreto. Il modello assegna i campioni di input a una o più classi specifiche (es. determinare se un'email è "Spam" o "Not Spam", oppure riconoscere se un'immagine ritrae un cane o un gatto).

Il **clustering** rappresenta la terza macro-categoria fondamentale del machine learning e si colloca nell'ambito dell'Unsupervised Machine Learning.

A differenza della regressione e della classificazione, nel clustering il dataset non contiene una variabile target $y$: i dati non sono etichettati e non esiste una "risposta corretta" da prevedere. L'obiettivo dell'algoritmo è analizzare la struttura intrinseca dei dati per identificare pattern nascosti e raggruppare i campioni in sottoinsiemi omogenei, detti appunto **cluster**.

Per trovare dataset da usare durante l'addestramento, il professore consiglia il sito https://www.openml.org/.

---

![[Pasted image 20260525192841.png]]
La quantità di informazioni oggi disponibile ha raggiunto dimensioni enormi e continua a espandersi secondo una traiettoria esponenziale. 

**Il nuovo paradigma con il machine learning** consiste nell'analizzare enormi quantità di dati per estrarne informazioni utili e addestrare modelli.

Tutto questo permette di reimpostare da zero le strategie di risoluzione dei problemi software. Non si tratta più di strutturare complessi algoritmi deterministici, ma di programmare i computer affinché apprendano direttamente dai dati (disponibili in grandi quantità), focalizzando l'intero processo ingegneristico sull'obiettivo di generare predizioni accurate a partire dalle informazioni storiche accumulate.

![[Pasted image 20260525193256.png]]
Questi sono i dataset presenti all'interno di Scikit-Learn. I "toy dataset" sono piccoli ed estremamente limitati.

![[Pasted image 20260525193456.png]]
Lo sviluppo e l'esecuzione di uno studio tipico di Data Science basato sul machine learning seguono una pipeline strutturata in passaggi sequenziali:

- **Loading the dataset:** Importazione dei dati grezzi all'interno dell'ambiente di sviluppo da file locali o sorgenti remote.
    
- **Exploring the data:** Analisi preliminare effettuata tramite l'utilizzo della libreria `pandas` e di strumenti di visualizzazione grafica per comprendere la struttura, le distribuzioni e le relazioni interne tra le variabili.
    
- **Transforming your data:** Conversione dei dati non numerici (come le variabili categoriali o testuali) in valori numerici. Questa operazione di pre-processing è un requisito fondamentale poiché gli stimatori di Scikit-Learn elaborano esclusivamente matrici numeriche.
    
- **Splitting the data:** Partizionamento dei dati in sottoinsiemi distinti da destinare rispettivamente alla fase di addestramento e alla fase di verifica.
    
- **Creating the model:** Selezione e istanziazione dell'algoritmo o dell'estimatore specifico più adatto al problema.
    
- **Training and testing the model:** Fase di calcolo in cui il modello apprende dai dati di training e viene successivamente valutato sui dati di test per verificarne la robustezza.
    
- **Tuning the model and evaluating its accuracy:** Calibrazione degli iperparametri del modello per massimizzarne le performance e analisi quantitativa dei parametri di accuratezza raggiunti.
    
- **Making predictions:** Distribuzione del modello per eseguire l'inferenza e generare predizioni su dati reali in produzione mai visti in precedenza dall'algoritmo.

Tutte le prime fasi di questa pipeline (loading, exploring e transforming) costituiscono le operazioni essenziali per la pulizia dei dati, un processo obbligatorio prima di poter alimentare qualsiasi algoritmo di machine learning.