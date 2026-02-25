# PhishGuard — Phishing Awareness Website
### WiCYS Club | Cyber Awareness Hackathon

---

## 📋 Table of Contents
1. [Project Overview](#project-overview)
2. [Features](#features)
3. [Tech Stack](#tech-stack)
4. [Project Structure](#project-structure)
5. [File Creation Steps (Step-by-Step)](#file-creation-steps)
6. [How the ML Detection Works](#how-the-ml-detection-works)
7. [Running the Project](#running-the-project)
8. [API Reference](#api-reference)
9. [Future Improvements](#future-improvements)

---

## Project Overview

**PhishGuard** is an educational, cybersecurity-themed website that helps users:
- Detect phishing emails/messages using a feature-based ML pipeline
- Learn about different types of phishing attacks
- Test their knowledge through an interactive quiz

Built for the WiCYS Club Cyber Awareness Hackathon, it demonstrates how machine learning can be applied to phishing detection without requiring large datasets or external libraries.

---

## Features

| Feature | Description |
|---|---|
| 🔍 ML Analyzer | Paste any message and get a phishing probability score |
| 📊 Feature Breakdown | Shows exactly which ML features flagged the text |
| 📖 Learn Page | Comprehensive phishing education guide |
| 🧪 Quiz | 8-question interactive quiz with real vs fake examples |
| 💡 Real vs Fake | Side-by-side comparison of phishing vs legitimate emails |

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python 3 + Flask |
| ML Engine | Custom feature-based classifier (no external ML libs needed) |
| Frontend | HTML5 + CSS3 + Vanilla JavaScript |
| Fonts | Orbitron + Share Tech Mono + Inter (Google Fonts) |
| Theme | Cyber-noir dark terminal aesthetic |

---

## Project Structure

```
phishing-awareness/
│
├── app.py                    ← Flask backend + ML detector
├── requirements.txt          ← Python dependencies
│
├── templates/
│   ├── index.html            ← Main detector page
│   ├── learn.html            ← Education/learn page
│   └── quiz.html             ← Interactive quiz page
│
└── static/
    ├── css/
    │   └── style.css         ← All CSS styles (cyberpunk theme)
    └── js/
        ├── main.js           ← Detector page logic
        └── quiz.js           ← Quiz logic + questions
```

---

## File Creation Steps

Follow these steps exactly to recreate the project from scratch:

### Step 1 — Set Up Your Environment

```bash
# Create project folder
mkdir phishing-awareness
cd phishing-awareness

# Create subdirectories
mkdir -p static/css static/js templates

# Create a Python virtual environment (recommended)
python -m venv venv

# Activate it
# On Windows:
venv\Scripts\activate
# On Mac/Linux:
source venv/bin/activate

# Install Flask
pip install flask
```

### Step 2 — Create the Backend (`app.py`)

Create `app.py` in the root folder. This file contains:
- The `PhishingDetector` class (ML engine)
- Feature extraction functions
- Flask routes: `/`, `/analyze`, `/quiz`, `/learn`

Key concept: The `PhishingDetector.extract_features()` method converts raw text into numerical features. The `predict()` method applies weighted scoring to those features.

### Step 3 — Create HTML Templates

In the `templates/` folder, create:
- `index.html` — Main page with the analyzer UI
- `learn.html` — Education content page
- `quiz.html` — Quiz shell (data loaded by JavaScript)

All templates share the same navbar and CSS file.

### Step 4 — Create the CSS (`static/css/style.css`)

Create `style.css` with the cyberpunk/terminal theme including:
- CSS custom properties (variables) for colors
- Animated grid background
- Scanline effect
- Glitch animation for the hero title
- Card components, buttons, result panels
- Responsive grid layouts

### Step 5 — Create JavaScript Files

**`static/js/main.js`** handles:
- Word count live update
- Sample message loading
- `fetch('/analyze', ...)` API call to Flask
- Rendering the result panel (verdict, probability bar, red flags, features)

**`static/js/quiz.js`** handles:
- 8 quiz questions with options, correct answers, and explanations
- Score tracking and progress bar
- Results screen with performance feedback

### Step 6 — Create `requirements.txt`

```
flask>=3.0.0
```

---

## How the ML Detection Works

PhishGuard uses a **feature engineering + weighted scoring** approach — the same core concept used in real ML phishing detectors, but without needing a trained model or dataset.

### Feature Extraction

| Feature | What It Measures | Weight |
|---|---|---|
| `urgency_count` | Count of urgency/pressure words ("immediately", "suspended", etc.) | ×12 |
| `suspicious_url_count` | URLs with IP addresses, many hyphens, or domain spoofing | ×20 |
| `sensitive_count` | Requests for passwords, SSN, credit cards, bank info | ×18 |
| `exclamation_count` | Number of `!` characters | ×4 |
| `caps_ratio` | Proportion of uppercase characters | ×30 |
| `domain_mismatch` | Multiple different email domains in one message | ×15 |
| `entropy` | Shannon entropy of character distribution | conditional |

### Scoring Formula

```python
score = (urgency_count × 12) + (url_score × 20) + (sensitive × 18)
        + (exclamations × 4) + (caps_ratio × 30) + (domain_mismatch × 15)

phishing_probability = min(100, score)
verdict = "PHISHING" if probability >= 40 else "LEGITIMATE"
```

### Why This Works

Real production ML phishing detectors (like those using Random Forest or Naive Bayes) are trained to weigh exactly these same types of features. This implementation demonstrates the core concept without needing labeled training data.

**To upgrade to a true ML model:**
1. Collect labeled dataset (e.g., UCI Phishing Dataset, SpamAssassin)
2. Extract the same features using this code
3. Train using `scikit-learn`: `RandomForestClassifier` or `LogisticRegression`
4. Replace `predict()` with `model.predict_proba()`

---

## Running the Project

```bash
# Make sure you're in the project root with venv active
python app.py
```

Open your browser and go to: **http://localhost:5000**

### Pages
| URL | Page |
|---|---|
| `http://localhost:5000/` | ML Phishing Analyzer |
| `http://localhost:5000/learn` | Education Guide |
| `http://localhost:5000/quiz` | Interactive Quiz |

---

## API Reference

### `POST /analyze`

Analyzes text for phishing indicators.

**Request Body:**
```json
{
  "text": "Your suspicious email text here..."
}
```

**Response:**
```json
{
  "is_phishing": true,
  "phishing_probability": 85,
  "verdict": "PHISHING",
  "confidence": "High",
  "red_flags": [
    "⚠️ Contains 4 urgency/pressure phrases",
    "🔗 Found 2 suspicious URL pattern(s)",
    "🔑 Requests sensitive info (3 pattern(s))"
  ],
  "safe_signs": [],
  "features": {
    "urgency_count": 4,
    "suspicious_url_count": 2,
    "sensitive_count": 3,
    "exclamation_count": 5,
    "caps_ratio": 0.18,
    "domain_mismatch": 0,
    "entropy": 4.2,
    "url_count": 2,
    "word_count": 124,
    "long_sentences": 1
  }
}
```

---

## Future Improvements

| Idea | Description |
|---|---|
| Real ML Model | Train a scikit-learn model on labeled phishing datasets |
| URL Scanner | Integrate VirusTotal API to check URLs in real time |
| Browser Extension | Let users scan emails directly in Gmail/Outlook |
| Reporting | Allow users to submit suspected phishing for community review |
| Email Header Analysis | Parse raw email headers (SPF, DKIM, DMARC) |
| BERT/NLP | Use transformer models for higher accuracy |

---

## Team & Credits

Built by the **WiCYS Club** for the **NASSCOM Cyber Awareness Hackathon 2026**.

> "Awareness is the first line of defense."
