<h1 align="center">Oxford English Dictionary ETL Pipeline</h1>

<p align="center">
  A high-throughput Python scraper and data transformation engine for large-scale linguistic datasets.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10+-3776AB" />
  <img src="https://img.shields.io/badge/Database-MySQL-4479A1" />
  <img src="https://img.shields.io/badge/Data_Size-439k_Entries-blue" />
  <img src="https://img.shields.io/badge/Status-Stable-success" />
</p>

**OED Word Parser** is an end-to-end ETL pipeline designed to extract, normalize, and store the vast lexicon of the Oxford English Dictionary. Beyond simple scraping, this project implements robust error-handling, automated retries for network instability, and a flexible data conversion layer to output web-ready JSON and relational SQL data.

<br />

## 🏗️ The ETL Pipeline

The system is architected as a modular three-stage pipeline:

1. **Extraction (`oed-parser.py`)**: A multi-threaded-capable scraper that navigates the OED search index. It manages state to allow resuming from specific page indices.
2. **Transformation (`convert-data.py`)**: A normalization engine that cleans "messy" HTML snippets, removes duplicates via dictionary mapping, and formats data into JSON, CSV, or TXT.
3. **Loading (`load-data-mysql.py`)**: A high-speed database injector utilizing `LOAD DATA LOCAL INFILE` for $O(n)$ performance, significantly faster than standard `INSERT` statements for 400k+ rows.

<br />

## 🧠 Resilience & Error Handling

Scraping 400,000+ entries takes time, making the script vulnerable to network timeouts and 502 Bad Gateway errors.

- **Backoff Strategy**: Implements a customizable `--error-delay` (default 60s) and `--max-retries` to wait out temporary server bans or IP rate-limits.
- **Dynamic Exit Logic**: The parser intelligently detects "Empty Result" states to prevent infinite polling.
- **Quote Sanitization**: Implements a custom delimiter strategy and double-quote escaping to ensure snippets containing complex punctuation don't break the CSV structure.

<br />

## 📊 Data Outputs

The repository includes the result of a full 439,362-word scrape:

- **`english-words.json`**: Pretty-printed JSON for easy inspection.
- **`english-words.min.json`**: Minified version optimized for production web use.
- **`english-words.csv`**: Database-ready format including Part of Speech and Page indexing.

<br />

## 🚦 Getting Started

### Prerequisites

- Python 3.8+
- MySQL (Optional, for database integration)

### Installation

```bash
git clone https://github.com/ronbodnar/oxford-english-dictionary-parser.git

cd oxford-english-dictionary-parser

pip install -r requirements.txt
```

### Execution Example

Extract 100 pages with a safety delay to prevent IP flagging:

```bash
python src/oed-parser.py --request-delay 2 --max-pages 100 --output-file data/output.txt
```

Continue extracting from page 100:

```bash
python src/oed-parser.py --request-delay 2 --starting-page 100 --output-file data/output.txt
```

Convert the results to JSON:

```bash
python src/convert-data.py -i data/output.txt -o data/output.json -f json
```

<br />

## 📫 Connect

**Created by Ron Bodnar**

- LinkedIn: [linkedin.com/in/ronbodnar](https://linkedin.com/in/ronbodnar)
- Portfolio: [ronbodnar.com](https://ronbodnar.com)

<br />

## ⚖️ License

Distributed under the MIT License. See `LICENSE` for more information.
