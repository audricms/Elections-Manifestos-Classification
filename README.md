Here is the clean, copy-pasteable README section for your repository. It integrates your teacher's instructions with the technical specifics of your script, ensuring any user can reproduce your local environment.

---

# Data Extraction: Arkindex Archelec Corpus

This project relies on historical electoral manifestos digitized by the CEVIPOF and hosted on Arkindex. Because the raw text corpus is large, the text files are not stored directly in this version-controlled repository. Instead, you must generate them locally from an Arkindex SQLite database export.

### 1. Prerequisites

To access the database exports, you must first create a registered account on the [Arkindex Demo instance](https://demo.arkindex.org).

### 2. Environment Setup

Install the official Arkindex export client and required libraries within a Python 3.11 virtual environment:

```bash
virtualenv -p python3.11 venv
source venv/bin/activate
pip install arkindex-export tqdm

```

### 3. Downloading the Database Export

1. Navigate to the Archelec corpus folder: [Archelec on Arkindex](https://www.google.com/search?q=https://demo.arkindex.org/browse/1bc39ca6-399b-47ca-9de1-ab2ef481cabb%3Ftop_level%3Dtrue%26folder%3Dtrue)
2. In the top menu, click **Import/Export** -> **Manage database exports**.
3. Download the latest SQLite archive and place it in the root directory of this project.

### 4. Running the Extraction Script

**Important:** The database filename changes based on when you download it. Before running the script, open `extract_text.py` and update the `DB_PATH` variable to match your downloaded file (e.g., `DB_PATH = Path("sciencespo-archelec-YYYYMMDD-HHMMSS.sqlite")`).

Once the path is updated, execute the extraction pipeline:

```bash
python extract_text.py

```

**How `extract_text.py` works:**

* **Performance Indexing:** The script injects custom SQL indexes into the SQLite database. Because Arkindex structures documents hierarchically (Folder -> Document -> Page -> Transcription), building `element_path` indexes drastically reduces recursive query time.
* **Data Parsing:** It iterates through specific folder UUIDs corresponding to the 1981, 1988, and 1993 legislative and presidential elections.
* **File Generation:** It extracts the raw OCR text page-by-page, concatenates them by document, and writes them into a structured local directory format: `data/manifestos/<year>/<election_type>/<document_name>.txt`.