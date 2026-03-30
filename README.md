# Archelec Political Manifesto Classification

*This project was developed as part of the "Machine Learning for NLP" course at ENSAE (2026), instructed by Christopher Kermorvant.*

This repository contains a reproducible Natural Language Processing (NLP) pipeline designed to extract, clean, and classify historical electoral manifestos from the French legislative and presidential elections (1981, 1988, 1993).

## 1. Repository Architecture

```text
├── README.md
├── data/                             # Hydrated locally (git-ignored)
│   ├── gazetteers/                   # INSEE COG administrative data
│   └── manifestos/                   # OCR text files and metadata.csv
├── notebooks/                        
│   └── EDA.ipynb                     # Exploratory Data Analysis (View only)
├── src/                              # Executable pipeline modules
│   └── data/
│       ├── extract_auxiliary_data.py # Google Drive fetching script
│       └── extract_manifestos.py     # Arkindex SQLite parsing script
├── main.py                           # Central orchestration script
├── pyproject.toml / uv.lock          # Modern package management
└── sciencespo-archelec-*.sqlite      # Raw Arkindex export (git-ignored)

```

## 2. Environment Setup

This project strictly uses `uv` for lightning-fast, reproducible dependency management. To set up your local environment, ensure you have `uv` installed, then run:

```bash
# Creates the virtual environment and installs exact dependencies from uv.lock
uv sync

# Activate the environment
source .venv/bin/activate

```

## 3. Data Ingestion Pipeline

To keep the Git history clean, the raw text corpus and auxiliary datasets are not stored in this repository. You must hydrate your local `data/` directory by executing the following two-step data pipeline.

### Step 3.1: Fetching Auxiliary Data (Gazetteers & Metadata)

The geographical filtering pipeline requires official French administrative data to mask spatial auto-correlation. The gazetteers are sourced directly from the **[INSEE Code Officiel Géographique (COG)](https://www.insee.fr/fr/information/8377162)**.

To download the pre-compiled gazetteers and manifesto metadata from the project's secure Google Drive, run:

```bash
uv run src/data/extract_auxiliary_data.py

```

*This script will automatically download and extract `auxiliary_data.zip` into the `data/` directory, hydrating the `gazetteers/` and `manifestos/metadata.csv` paths.*

### Step 3.2: Extracting the Archelec Transcriptions (Arkindex)

The raw OCR transcriptions of the manifestos are hosted by the CEVIPOF on Arkindex.

1. **Create an Account:** Register on the [Arkindex Demo instance](https://demo.arkindex.org).
2. **Download the Database:** Navigate to the [Archelec corpus](https://www.google.com/search?q=https://demo.arkindex.org/browse/1bc39ca6-399b-47ca-9de1-ab2ef481cabb%3Ftop_level%3Dtrue%26folder%3Dtrue). In the top menu, click **Import/Export -> Manage database exports**, and download the latest SQLite archive.
3. **Place the File:** Move the downloaded `.sqlite` file into the root directory of this repository.
4. **Configure the Script:** Open `src/data/extract_manifestos.py` and ensure the `DB_PATH` variable matches the exact timestamped filename of your downloaded SQLite database (e.g., `sciencespo-archelec-20260217-121320.sqlite`).

Execute the text extraction:

```bash
uv run src/data/extract_manifestos.py

```

*This will parse the SQLite database, apply structural indexing for performance, and generate the raw `.txt` files inside `data/manifestos/<year>/<election_type>/`.*

## 4. Execution

Once the data layer is fully hydrated, the entire preprocessing and classification pipeline can be orchestrated from the root entry point:

```bash
uv run main.py

```