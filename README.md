# Multilingual Support Translator — AI-Powered Language Translation System

---

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)
![NLP](https://img.shields.io/badge/NLP-Machine_Translation-orange?style=for-the-badge)
![PyTorch](https://img.shields.io/badge/PyTorch-Seq2Seq_%7C_Transformer-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white)
![TensorFlow](https://img.shields.io/badge/TensorFlow-Deep_Learning-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-Web_API-005571?style=for-the-badge&logo=fastapi&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?style=for-the-badge&logo=docker&logoColor=white)

<p align="center">
  <b>An end-to-end Neural Machine Translation (NMT) system designed to preprocess, train, evaluate, and translate text across multiple languages using deep sequence models and a web backend interface.</b>
</p>

---

## Overview

**Multilingual Support Translator** is an intelligent, full-stack language translation system designed to bridge communication gaps with high contextual accuracy. Built around Meta's pretrained **`facebook/nllb-200-distilled-600M`** model, it handles automatic source language detection and seamlessly translates text across multiple global and regional languages.

Unlike standard translation utilities, this system features a **built-in term protection pipeline** that ensures critical non-translatable tokens—such as error codes (`ERR-500`), product IDs (`SKU-99A`), URLs, and email addresses—remain completely untouched during translation.

The backend is served via a **Flask REST API**, driving a clean, responsive single-page vanilla HTML/CSS/JS frontend.

---

## Project Objective

The primary objective is to build a robust, privacy-first translation application that can:

* Automatically detect source languages or allow manual selection
* Translate text across 10 out-of-the-box languages (extendable to NLLB's full 200-language support)
* Protect technical tokens, error codes, and identifiers from unwanted localization
* Provide a responsive single-page web interface with real-time character counting and keyboard shortcuts
* Run locally to ensure absolute data privacy and security

---

## Problem Statement

Standard online translation tools often struggle with technical documents, logs, or customer support tickets because they blindly translate structured strings, breaking critical identifiers.

Traditional translation workflows often suffer from:

* Unwanted translation of error codes, hashes, and part numbers
* Privacy concerns when sending sensitive enterprise or user data to third-party cloud APIs
* Clunky interfaces lacking quick language-swapping or shortcut features
* Heavy latency or high deployment costs

### Proposed Solution

This project introduces an end-to-end local translation pipeline that automatically executes:

**Detect → Protect → Translate → Restore**

For every translation request, the system:

1. Identifies the source language.
2. Isolates and masks protected entities (URLs, emails, alphanumeric codes).
3. Executes transformer-based neural translation via Meta NLLB.
4. Restores protected tokens precisely back into their original positions.

---

## How It Works

```text
            USER INPUT
                │
                ▼
      ┌───────────────────┐
      │  Frontend (JS)    │
      └────────┬──────────┘
               │
               ▼
      ┌───────────────────┐
      │    Flask API      │
      └────────┬──────────┘
               │
               ▼
      ┌───────────────────┐
      │   translator.py   │
      │ (Detect & Protect)│
      └────────┬──────────┘
               │
               ▼
      ┌───────────────────┐
      │   Meta NLLB-200   │
      │     Transformer   │
      └────────┬──────────┘
               │
               ▼
      ┌───────────────────┐
      │  Token Restoration│
      └────────┬──────────┘
               │
               ▼
      ┌───────────────────┐
      │  JSON Response /  │
      │    UI Rendering   │
      └───────────────────┘

```

---

## Supported Languages

Out of the box, the system supports 10 languages (easily expandable via `translator.py`):

1. English (`en`)
2. French (`fr`)
3. Spanish (`es`)
4. German (`de`)
5. Hindi (`hi`)
6. Tamil (`ta`)
7. Telugu (`te`)
8. Arabic (`ar`)
9. Chinese (`zh`)
10. Portuguese (`pt`)

---

## API Reference

The Flask backend exposes the following JSON endpoints:

| Method | Endpoint | Body / Notes |
| --- | --- | --- |
| `GET` | `/api/languages` | List of `{iso, name}` supported languages |
| `GET` | `/api/health` | `{status, model_loaded}` |
| `POST` | `/api/translate` | `{ "text": "...", "target": "hi", "source": "auto" }` |

### Example Request

```bash
curl -X POST http://127.0.0.1:5000/api/translate \
  -H "Content-Type: application/json" \
  -d '{"text": "My payment failed with error ERR-500.", "target": "hi"}'

```

### Example Response

```json
{
  "translation": "मेरा भुगतान ERR-500 त्रुटि के साथ विफल हो गया।",
  "src_iso": "en",
  "src_name": "English",
  "tgt_iso": "hi",
  "tgt_name": "Hindi",
  "confidence": 1.0,
  "latency_ms": 812.4
}

```

---

## Project Structure

```text
translation_app/
├── app.py              # Flask server + JSON API routes
├── translator.py       # Core pipeline: detect -> protect -> translate -> restore
├── requirements.txt    # Python dependencies
├── templates/
│   └── index.html      # Frontend markup
└── static/
    ├── style.css       # Clean, modern UI styling
    └── app.js          # Frontend event handling & API communication

```

---

## Setup & Run

Requires **Python 3.9+**. A GPU is optional; CPU inference is fully supported (though initial loading is slower).

### 1. Clone the Repository

```bash
git clone https://github.com/Abdulla-B-Official/Language_Translation_System_Project.git
cd Language_Translation_System_Project

```

### 2. Create and Activate a Virtual Environment

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

```

### 3. Install Dependencies

```bash
pip install -r requirements.txt

```

### 4. Run the Application

```bash
python app.py

```

Then open **[http://127.0.0.1:5000](http://127.0.0.1:5000)** in your browser.

> **Note on First Run:** The transformer model (~2.5 GB) will automatically download from the Hugging Face Hub and load into memory on your first `/api/translate` call. Subsequent requests run smoothly and quickly.

---

## Privacy & Production Notes

* **Local Execution:** The model runs entirely on your machine—no user data ever leaves your hardware.
* **Production Deployment:** The built-in Flask development server is intended for local and demo use. For production environments, wrap the application behind a robust WSGI server (e.g., `gunicorn` or `waitress`) and implement rate-limiting.

---

## Technology Stack

| Technology | Purpose |
| --- | --- |
| **Python** | Core backend logic |
| **Flask** | Lightweight web framework & REST API |
| **PyTorch & Transformers** | Neural machine translation (`facebook/nllb-200-distilled-600M`) |
| **HTML / CSS / JavaScript** | Responsive, modern single-page frontend UI |
| **Git & GitHub** | Version control and repository hosting |

---

### Translate Smarter. Preserve Context. Connect Globally.

⭐ If you found this project useful, consider giving the repository a star!
