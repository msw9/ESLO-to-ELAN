# Traitement de corpus oral

Traitement automatique et alignement de données issues de l'ESLO (Enquêtes Sociolinguistiques à Orléans).

## Description

Ce projet met en œuvre un pipeline complet de traitement de corpus oral, de la conversion des fichiers audio bruts jusqu'à l'alignement et l'annotation dans ELAN.

## Corpus

**ESLO — Enquêtes Sociolinguistiques à Orléans**
Corpus de français parlé produit et hébergé par le LLL (Ortolang). Enregistrements de locuteurs orléanais dans des situations de communication variées (entretiens, interactions spontanées).

- Source : [https://eslo.huma-num.fr](https://eslo.huma-num.fr)

## Structure du projet

```
corpus/
├── raw/              # fichiers audio originaux (MP4, intouchés)
├── processed/        # fichiers convertis (WAV mono 16kHz)
├── transcripts/      # sorties Whisper (TSV)
├── aligned/          # fichiers d'annotation ELAN (.eaf)
├── metadata/         # métadonnées des fichiers traités (XML)
├── docs/             # documentation et protocole
└── README.md
```



## Étapes 

### 1. Prétraitement audio

Conversion des fichiers MP4 en WAV mono 16kHz, format attendu par Whisper :

```batch
FOR %%f IN (raw\*.mp4) DO ffmpeg -i "%%f" -ac 1 -ar 16000 -c:a pcm_s16le "processed\%%~nf.wav"
```

### 2. Transcription automatique

Transcription par le modèle Whisper (`medium`), avec horodatage mot à mot :

```batch
FOR %%f IN (processed\*.wav) DO whisper "processed\%%~nf.wav" --language French --model medium --output_dir transcripts
```


### 3. Alignement et annotation dans ELAN

Une correction manuelle a été appliquée sur une sélection de fichiers afin de rectifier les erreurs de transcription automatique (disfluences, chevauchements, termes non reconnus) et aussi différencier les différents acteurs dans le cadre d'interviews

## Limites identifiées

- **Disfluences** : Whisper tend à lisser les hésitations (`euh`, `ben`, répétitions)
- **Chevauchements** : les passages à plusieurs voix simultanées sont mal transcrits ou ignorés.
- **Termes régionaux et propres** : le modèle `medium` produit davantage d'erreurs sur les noms propres orléanais


## Références

- Whisper (OpenAI) : [https://github.com/openai/whisper](https://github.com/openai/whisper)
- ELAN (MPI) : [https://archive.mpi.nl/tla/elan](https://archive.mpi.nl/tla/elan)
- ESLO  : [https://eslo.huma-num.fr](https://eslo.huma-num.fr)
