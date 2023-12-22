# Segmentation Task - CHAOS Dataset 
Biomedical Computer Vision course

## Report
### riorganizzazione del dataset all'interno del file system
il dataset è nel file system è stato riorganizzato in una directory a due livelli:
1. (directory): numero_paziente
2. (filename): mode_codice.formato

quindi il contenuto di una cartella di un generico paziente, se visualizzato in ordine alfabetico, apparirà nel seguente modo, in seguito ad un'appropriata rinominazione:

T1DUAL-codice_dispari.dcm (outphase)
T1DUAL-codice_pari.dcm (inphase)
T1DUAL-codicepari.png (mask, avente lo stesso codice della inphase)
(...)
T2SPIR-codice.dcm
T2SPIR-codice.png
(...)

questo permette di semplificare il codice che ha il compito di importare immagini e maschere, e di accoppiarle appropriamente

### struttura codice e progettazione modello
- caricamento di tutto il dataset in memoria, attraverso classe imgs_collection, cui unico attributo è un array di img_struct
- la classe img_struct contiene metodi e informazioni utili per gestire un singolo sample, e include metodi che consentono di realizzare coppie immagini-maschere, estrarre informazioni utili su pazienti e metodi di acquisizione, e realizzare il pre-processing, inclusivo di resize e reshape, per ciascuna immagine
- sono presenti funzioni che permettono di visualizzare in dati in modalità rilevanti alla realizzazione del task
- utilizzo di k-fold per cross-validation
  - nella pratica non è stato sfruttato al meglio, per via di carenza di tempo e risorse computazionali
- utilizzo di data augmentation
  - in particolare è stata utilizzata una trasformazione affine
    - la probabilità di applicazione di ciascuna componente della trasformazione (rotazione, traslazione, scaling) è estratta casualmente: ciascuna sotto-trasformazione è applicata con una probabilità indipendentemente dalle altre, se e solo se la probabilità estratta supera il valore di soglia associato
    - il valore dei parametri utilizzati nella trasformazione è anch'esso estratto casualmente
- le migliori loss per ciascuna fold sono salvate su file, cosi come i pesi dei modelli che forniscono la migliore loss per ciascuna fold
- *scelta architettura*: resnet101 dotata di due porzione fully connected (un classificatore principale e un classificatore ausiliario), preaddestrata sul dataset ImageNet
  - questa scelta è motivata dalle limitate risorse computazionali disponibili e simultaneamente dall'aleatorità legata all'accesso ad esse
  - inoltre il riuso di un modello già addestrato su un dataset cosi ampio garantisce una base di partenza in grado di estrarre feature generali comuni a una varietà di immagini, senza necessitare del training associatovi
  - l'ultimo layer di ciascun classificatore è stato appositamente modificato per ottenere in output un numero di canali pari a 5 -- come previsto dal task di segmentazione 
  - per i motivi sopracitati, la fase di training è stata costituita unicamente da un finetuning dei layer densi

### roadmap
- [] *è in piano la realizzazione di training su architetture non pre-addestrate*
  - il codice del loop di training è stato progettato per essere estendibile e riutilizzabile per modelli che non sono la resnet101 utilizzata in partenza
- [] rendere sensibile all'update dei parametri non solo i classificatori ma anche il layer backbone numero 4, il che permetterebbe un adattamento del modello ad estrarre migliori feature di alto livello maggiormente utili alla fase di segmentazione vera e propria
- [] introduzione di preprocessing, differenziato in base all'efficacia nei confronti delle 3 modalità di acquisizione (T1in, T1out, T2)
- [] aggiunta trasformazione in data augmentation: aggiunta rumore gaussiano
- [] rendere l'output di classificazione per ciascuna delle classi binario. ovverosia: un pixel o assume valore True o assume un valore False, anziché avere un dominio costituito da un range di 256 valori
  - idealmente ciò provvede non solo a rendere l'output della segmentazione più chiaro, ma anche a fornire feedback piu netto nel momento del calcolo della loss

