# Features

Il dataset presenta 49 features.
Packet-based features are extracted from the packet header and its payload (also called packet data). In contrast, flow-based features are generated using the sequencing of packets, from a source to a destination, traveling in the network.

Attacks are categorized as Analysis, Backdoor, DoS, Exploits, Fuzzers, Generic, Reconnaissance, Shellcode and Worms.

there is considerable lack of balance for the dataset as 87% of the dataset comprises Normal records whereas only 0.007% of the dataset consists of Worms records. 

[[UNSW-NB15-Analysis-ImbalanceAndOverlap-CRITICAL.pdf]]
### Feature categoriche:
- proto
- service
- state
[[algorithms-17-00064-highlighted.pdf]]
### Feature correlate:
- sbytes e dbytes (poichè vengono sommate per calcolare network_bytes)
- loss
- ct_srv_dst
- ct_src_dport_ltm
- dpkts
- ltime
- dloss
- ct_dst_src_ltm
[[algorithms-17-00064-highlighted.pdf]]

# Features superflue

Since some features are specific to the computing infrastructure such as source IP address, source port number, destination IP address, and destination port number, they do not possess relevant information for intrusion detection purposes. Accordingly, we eliminate:

- record_start_time
- record_last_time
- source IP address
- source port number
- destination IP address
- destination IP address 

[[UNSW-NB15-Analysis-ImbalanceAndOverlap-CRITICAL.pdf]]
## Feature engineering

- **Exploratory Data Analysis**: A heatmap was used to identify and remove highly correlated values.
- **Feature Engineering and Data Preparation**: New features were added, and the data was standardized using the standard scalar and Onehotencode functions.
- **Train and Test Data Split**: A training set (70%) and a testing set (30%) were generated from the dataset.
- **Training and Testing of the Models**: On the training set, methods for logistic regression, decision trees, random forests, and linear SVM were applied; on the testing set, their efficacy was assessed.
[[algorithms-17-00064-highlighted.pdf]]
## Data Pre-Processing

- Rimozione valori nulli da ct_flw_http_mthd, is_ftp_login, attack_cat
-  the ‘ct_ftp_cmd’ column contiene spazi vuoti, vanno rimpiazzate con degli zero per migliorare il processo di apprendimento. Modifichiamo anche il tipo da object ad int per lo stesso motivo
- attack_cat rimossa completamente dal training test

[[algorithms-17-00064-highlighted.pdf]]
## Calcolo della correlazione

Esistono due modi:
- Pearson's Correlation Coefficient - due paper lo usano ([[algorithms-17-00064-highlighted.pdf]] e [[Exploratory Analysis of UNSW-NB15 Dataset-Medium-highlighted.pdf]])
- Spearman's Rank Correlation - MIGLIORE
### Pearson's Correlation Coefficient (PCC)
Calcoliamo la correlazione tra le features tramite Pearson's Correlation Coefficient (PCC) e Gain Ration.
In particolare: 
"Using Pearson’s Correlation Coefficient (PCC) and a second correlation between two tables with class labels using the Gain Ration method. Table 3 presents the correlation value between the two variables." [[algorithms-17-00064-highlighted.pdf]].

The value of coefficient ranges between -1 to +1, where +1 means that they are highly correlated and -1 means that there is a negative correlation and 0 means no correlation. 
[[Exploratory Analysis of UNSW-NB15 Dataset-Medium-highlighted.pdf]]

Di conseguenza eliminiamo le features altamente correlate, ossia:
- loss
- ct_srv_dst
- ct_src_dport_ltm
- dpkts
- ltime
- dloss
- ct_dst_src_ltm

Il documento [[Exploratory Analysis of UNSW-NB15 Dataset-Medium-highlighted.pdf]] propone di misurare il coefficiente e generare una scatter plot:

![[Pasted image 20260610191003.png]]

Tabella delle correlazioi:

![[Pasted image 20260610183830.png]]

[[algorithms-17-00064-highlighted.pdf]]
### Spearman's Rank Correlation
To remove redundancy, Spearman’s rank correlation was used with a threshold of 0.85.
Unlike the Pearson correlation, Spearman’s method can capture nonlinear relationships that are common in network traffic, for example, between Flow Bytes and Total Packets.
Features with correlations lower than the threshold were used for training, whereas those with high correlations were excluded from the training dataset.
[[NCAA_20251-highlithed.pdf]]
# Feature Engineering and Data Preparing

## Data Splitting

To mimic real-world deployment, the dataset is chronologically divided into 
- Training (70%): Benign traffic 
- Validation (15%): Benign traffic 
- Testing (15%): Remaining traffic, including zero-day attacks.

[[NCAA_20251-highlithed.pdf]]
### Onehot Encoding
Usiamo il one hot encoding per processare dati categorici.
Applichiamo Onehot encoding alle colonne:
- proto
- service
- state
[[algorithms-17-00064-highlighted.pdf]]
# AUC e ROC

The Receiver Operating Characteristic (ROC) and Area Under the Curve (AUC) are common assessment metrics for binary classification problems.
The performance of a binary classifier at various classification thresholds is shown graphically via the ROC curve. Plotting the true positive rate (TPR) vs the false positive rate (FPR) at different threshold levels is how it is made.
A helpful tool for assessing the effectiveness of various classifiers is the ROC curve. Higher AUC scores indicate better separation of the two classes, making them superior classifiers. The area under the ROC curve is represented by a single-value statistic called AUC, on the other hand. Higher values correspond to greater classifier performance; the range is 0 to 1.
An overall indicator of the classifier’s capacity to distinguish between positive and negative classes across all potential thresholds is provided by the AUC. Both AUC and ROC are useful metrics for evaluating the performance of a binary classifier, especially when the two classes have imbalanced data distribution.
[[algorithms-17-00064-highlighted.pdf]]

# Autoencoder

Quello proposto ha una struttura di questo tipo:

**Input (84) → 64 (ReLU) → 32 (ReLU) → 16 (ReLU) → 8 (lineare) → 16 (ReLU) → 32 (ReLU) → 64 (ReLU) → 84 (Sigmoid)**. Tieni la Sigmoide solo se usi MinMaxScaler.
- Learning Rate pari a 0.001 e minimum validation reconstruction error
- Batch size pari a 128.

Hyperparameters, including the layers, hidden neurons, epochs, and learning rate, were optimized using the random search method. The 8 M. Rehman et al. random search focused on a common range of hyperparameters that also characterized the previous settings: depth of the encoder-decoder (2-4 layers), neurons per layer (16-128), learning rates (0.0001–0.005), and batch size (64-256). It was selected because it performs better than grid searching high-dimensional parameter spaces. It has a faster convergence than grid search methods in highdimensional parameter spaces, and it can avoid overfitting by considering several parameter setups.

[[NCAA_20251-highlithed.pdf]]

## Loss Function

Huber Loss (δ = 1.0) was used instead of MSE because it dealt with outliers more effectively and fine-grained anomalies more efficiently. The autoencoder was trained using the selected architecture for n epochs, where n was determined based on early stopping (patience = 10 epochs). The training progress was monitored using loss and accuracy curves to check for convergence of the model.

- threshold-based anomaly scoring scheme ensures sensitivity to rare deviations. A schematic of the model is shown in Fig. 4. The end-to-end training procedure is described in Algorithm 1.

![[Pasted image 20260610190401.png]]

![[Pasted image 20260610190440 1.png]]

![[Pasted image 20260610190512 1.png]]
