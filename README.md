# Primjene generativne umjetne inteligencije u kreativnom pisanju i generiranju sadržaja

Ovaj repozitorij sadrži materijale i kod korištene u završnom radu **„Primjene generativne umjetne inteligencije u kreativnom pisanju i generiranju sadržaja”**.

Rad istražuje mogućnost **domenske prilagodbe modela YugoGPT za generiranje hrvatske književne proze** primjenom metode **QLoRA**. Model je fino podešen na kuriranom korpusu hrvatskih književnih tekstova, a učinak prilagodbe procijenjen je kombinacijom automatskih metrika, AI evaluacije, kvalitativne analize i korisničke studije.

## Cilj rada

Cilj je ispitati može li se postojeći jezični model prilagoditi hrvatskom književnom kontekstu tako da generira tekstove koji pokazuju poboljšanja u:

- koherentnosti
- prirodnosti jezika
- književnom stilu
- ukupnoj kvaliteti
- zanimljivosti generiranog sadržaja

Poseban naglasak stavljen je na usporedbu **baznog YugoGPT modela** i modela nakon QLoRA prilagodbe.

## Korišteni model i podaci

- **Bazni model:** `gordicaleksa/YugoGPT`
- **Metoda prilagodbe:** QLoRA
- **Korpus:** hrvatska književna proza
- **Trening djela:** 35
- **Validacijska djela:** 3
- **Trening sekcije:** 149
- **Validacijske sekcije:** 15
- **Trening blokovi:** 3.393
- **Validacijski blokovi:** 349
- **Duljina sekvence:** 256 tokena

Korpus obuhvaća četiri skupine književne proze:

1. moderna i simbolistička proza
2. realistička i društvena proza
3. bajkovita i fantastična proza
4. psihološka i eksperimentalna proza

## QLoRA konfiguracija

Za učinkovito treniranje na ograničenim GPU resursima korištena je QLoRA konfiguracija:

| Parametar | Vrijednost |
|---|---:|
| Quantization | 4-bit NF4 |
| Double quantization | Da |
| LoRA rank | 8 |
| LoRA alpha | 16 |
| LoRA dropout | 0,05 |
| Learning rate | 5e-5 |
| Batch size | 1 |
| Gradient accumulation | 8 |
| Effective batch size | 8 |
| Epochs | 10 |
| Optimizer | paged AdamW 8-bit |
| Precision | FP16 |
| Gradient checkpointing | Da |
| Seed | 42 |

Ukupno model ima približno **7,25 milijardi parametara**, dok je trenirano samo **3.407.872 parametara**, odnosno približno **0,047 %** ukupnog broja parametara.

Treniranje je provedeno u **Google Colabu** na NVIDIA Tesla T4 GPU-u s 16 GB memorije.

## Odabir checkpointa

Model je treniran kroz 10 epoha, uz evaluaciju i spremanje checkpointa nakon svake epohe.

Perpleksitet baznog modela iznosio je **12,06**, dok je za odabrani checkpoint nakon 7. epohe iznosio **10,55**, što predstavlja smanjenje od približno **12,5 %**.

Za završnu evaluaciju odabran je **checkpoint nakon 7. epohe**, na temelju stabilizacije rezultata te preliminarne kvalitativne analize generiranih tekstova.

## Evaluacija

Modeli su uspoređeni kroz više komplementarnih metoda.

### 1. Gubitak i perpleksitet

Praćen je training loss, validation loss i perpleksitet kroz svih 10 epoha.

### 2. Pravila i analiza tekstualnih obilježja

Korištene su metrike poput:

- ukupnog broja riječi
- broja jedinstvenih riječi
- gustoće vokabulara / TTR-a
- prosječnog broja riječi po rečenici

### 3. AI evaluator

Generirani tekstovi ocijenjeni su na ljestvici 1–5 prema kriterijima:

- koherentnost
- podudaranje stila
- jezična ispravnost
- zanimljivost

### 4. Kvalitativna analiza

Ručno su uspoređivani generirani tekstovi s naglaskom na pripovjedni oblik, književni ton, atmosferu, koherentnost i pojavu jezičnih ili narativnih artefakata.

### 5. Korisnička studija

U studiji je sudjelovao **61 ispitanik**, koji su uspoređivali tekstove baznog i fino podešenog modela prema četiri kriterija:

- koherentnost
- prirodnost jezika
- književni stil
- ukupna kvaliteta

Za statističku analizu uparenih ocjena korišten je **Wilcoxonov test predznačenih rangova**.

## Rezultati

Prosječne ocjene korisničke studije:

| Kriterij | Bazni model | QLoRA model |
|---|---:|---:|
| Koherentnost | 2,81 | **3,76** |
| Prirodnost jezika | 2,92 | **3,67** |
| Književni stil | 2,87 | **3,70** |
| Ukupna kvaliteta | 2,82 | **3,72** |
| **Ukupni prosjek** | **2,86** | **3,71** |

Wilcoxonov test pokazao je statistički značajne razlike za sva četiri kriterija (**p < 0,001**).

Kod izravnog odabira boljeg teksta ispitanici su u ukupno **293 od 366 usporedbi (80,1 %)** odabrali tekst fino podešenog modela.

Rezultati upućuju na poboljšanje više aspekata kvalitete generiranog teksta nakon domenske prilagodbe, uz preostale jezične i narativne nedostatke.

## Reprodukcija

Glavni eksperiment nalazi se u Jupyter notebooku:

`Zavrsni_3_GPU_ready_RUN2_odvojeni_preliminarni_promptovi.ipynb`

Notebook obuhvaća:

1. učitavanje modela i tokenizatora
2. 4-bitnu kvantizaciju
3. konfiguraciju QLoRA adaptera
4. učitavanje i pripremu korpusa
5. tokenizaciju i segmentaciju
6. treniranje modela
7. spremanje i učitavanje checkpointova
8. izračun perpleksiteta
9. generiranje tekstova
10. završnu usporedbu modela

Za pokretanje eksperimenta preporučuje se GPU okruženje zbog memorijskih zahtjeva modela.

## Struktura projekta

```text
.
├── Zavrsni_3_GPU_ready_RUN2_odvojeni_preliminarni_promptovi.ipynb
├── README.md
├── podaci/
├── rezultati/
└── checkpointi/
```

Stvarna struktura direktorija može ovisiti o tome koje su datoteke izdvojene iz priloga završnog rada.

## Ograničenja

Rezultate treba promatrati u kontekstu korištenog korpusa, šest završnih promptova, ograničenja generiranja na 300 novih tokena i relativno malog korisničkog uzorka. Rezultati također ne znače da je fino podešavanje poboljšalo svaki pojedinačni generirani tekst; pojedini primjeri pokazuju preostale jezične i narativne probleme.

## Tehnologije

**Python · PyTorch · Hugging Face Transformers · PEFT/QLoRA · bitsandbytes · Pandas · NumPy · Google Colab**

## Autor

**Jakov Kordić**

Fakultet organizacije i informatike, Sveučilište u Zagrebu  
Studij informacijskih sustava
