# Biomedical-computer-vision
CHAOS Dataset - Segmentation Task


## Report
### organizzazione dataset in file system
NOTA: il dataset è nel file system è stato riorganizzato in una directory a due livelli:
1. (directory): numero_paziente
2. (filename): mode_codice.formato

quindi il contenuto di una cartella di un generico paziente, se visualizzato in ordine alfabetico, apparirà nel seguente modo:

T1DUAL-codice_dispari.dcm (outphase)
T1DUAL-codice_pari.dcm (inphase)
T1DUAL-codicepari.png (mask, avente lo stesso codice della inphase)
(...)
T2SPIR-codice.dcm
T2SPIR-codice.png
(...)

### 
