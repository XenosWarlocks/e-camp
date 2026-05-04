# Email Metrics Dashboard

A Streamlit web application for analyzing email campaign performance. Upload a CSV of your email engagement data to get interactive charts, per-company and per-role breakdowns, a campaign simulator, and optional AI-generated insights powered by OpenAI or Google Gemini.

---

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Running the App](#running-the-app)
- [CSV Data Format](#csv-data-format)
- [Dashboard Walkthrough](#dashboard-walkthrough)
  - [Sidebar Controls](#sidebar-controls)
  - [Summary Metrics](#summary-metrics)
  - [Performance Analysis](#performance-analysis)
  - [Insights & Recommendations](#insights--recommendations)
  - [Advanced Analytics](#advanced-analytics)
- [AI Insights](#ai-insights)
  - [OpenAI Setup](#openai-setup)
  - [Google Gemini Setup](#google-gemini-setup)
  - [How It Works](#how-it-works)
- [Project Structure](#project-structure)
- [Troubleshooting](#troubleshooting)

---

## Features

| Feature | Description |
|---|---|
| CSV upload | Drag-and-drop your engagement data or load built-in sample data |
| Binary data handling | Accepts `yes/no`, `true/false`, `1/0`, or `y/n` in email columns |
| Filters | Filter by company, job role, and metric type |
| Summary cards | Open rate, click rate, and engagement score at a glance |
| Bar charts | Per-company and per-role open/click rate visualisations |
| Data table | Full company × job role breakdown with people-level detail |
| CSV export | Download the engagement table as a CSV |
| Bubble chart | Open rate vs click rate scatter analysis across job roles |
| Campaign simulator | Project future opens, clicks, and ROI under a chosen improvement scenario |
| AI insights | One-click narrative analysis via OpenAI (GPT-4o) or Google Gemini (2.0 / 2.5) |
| Industry benchmarks | Compare your rates against overall industry averages in the sidebar |

---

## Requirements

- Python 3.10 or later (the code uses the `X | Y` union type syntax)
- The packages listed below

### Core packages

```
streamlit
pandas
numpy
plotly
```

### Optional packages (only needed for AI insights)

```
openai                 # for OpenAI GPT-4o / GPT-4o-mini
google-generativeai    # for Google Gemini 2.0 Flash / 2.5 Pro
```

---

## Installation

1. **Clone or download** this repository.

2. **Create a virtual environment** (recommended):

   ```bash
   python -m venv venv
   source venv/bin/activate       # macOS / Linux
   venv\Scripts\activate          # Windows
   ```

3. **Install core dependencies**:

   ```bash
   pip install streamlit pandas numpy plotly
   ```

4. **Install AI packages** if you plan to use the AI insights feature:

   ```bash
   pip install openai                # OpenAI
   pip install google-generativeai   # Google Gemini
   ```

---

## Running the App

```bash
streamlit run streamlit-app.py
```

Streamlit will open the dashboard automatically in your default browser at `http://localhost:8501`.

---

## CSV Data Format

Your CSV file must contain the following six columns (column names are case-sensitive):

| Column | Type | Accepted Values |
|---|---|---|
| `Company` | Text | Any company name |
| `JobRole` | Text | Any job title or role |
| `PersonName` | Text | Recipient full name |
| `EmailsSent` | Binary | `yes` / `no`, `true` / `false`, `1` / `0`, `y` / `n` |
| `EmailsOpened` | Binary | Same as above |
| `EmailsClicked` | Binary | Same as above |

**Example rows:**

```csv
Company,JobRole,PersonName,EmailsSent,EmailsOpened,EmailsClicked
Datadog,Senior Product Marketing Manager,Bridgitte Kwong,yes,yes,no
Datadog,Director Partner Marketing,Manuela Rojas,yes,no,no
Acme Corp,CEO,John Smith,yes,yes,yes
```

> If any required column is missing the app will show an error listing the missing columns and will not attempt to process the file.

---

## Dashboard Walkthrough

### Sidebar Controls

| Control | Purpose |
|---|---|
| **Upload CSV File** | Upload your own engagement data |
| **Load Sample Data** | Instantly populate the dashboard with 19 synthetic records |
| **AI Provider** | Choose `None`, `OpenAI`, or `Google Gemini` |
| **API Key** | Password-masked field — only appears when a provider is selected |
| **Model** | Choose between available models for the selected provider |
| **Industry Benchmarks** | Reference table showing overall average open rate, click rate, and CTOR |

### Summary Metrics

Four cards displayed across the top of the main area:

- **Total Emails Sent** — count of records where `EmailsSent = 1` in the current filter
- **Open Rate** — `(emails opened / emails sent) × 100`, shown with the raw fraction
- **Click Rate** — `(emails clicked / emails sent) × 100`, shown with the raw fraction
- **Engagement Score** — weighted score: `(0.4 × open rate) + (0.6 × click rate)`

### Performance Analysis

Two tabs:

#### Bar Charts tab

- **All Companies view** — side-by-side bar charts for open rate and click rate by company, plus a collapsible detail table
- **All Roles view** — same charts broken down by job role
- **Single company view** — when a company filter is active, shows the role breakdown within that company, plus a table of individual openers and clickers

The **Metric Type** filter in the header controls which charts are shown:

| Selection | Charts visible |
|---|---|
| Open Rate | Open rate bar chart only |
| Click Rate | Click rate bar chart only |
| Click-to-Open Rate | Summary card updates; no dedicated chart |
| All Metrics | Both bar charts |

#### Data Table tab

A full Company × Job Role breakdown table with:

- Total people contacted per segment
- Open rate and click count detail
- Names of people who opened (comma-separated)
- Names of people who clicked (comma-separated)
- **Download CSV Report** button to export the table

### Insights & Recommendations

An expandable section that always shows rule-based insights:

- **Overall Engagement** — compares your open and click rates to the industry average (20.2% open, 8.6% click)
- **Recommendations** — conditional tips for improving open rate or click rate depending on whether you are above or below the benchmark
- **Top Segments** — highlights the highest-click-rate company and job role from the current data

If an AI provider is configured, an additional AI panel appears at the top of this section (see [AI Insights](#ai-insights)).

### Advanced Analytics

Two tabs:

#### Engagement Correlation tab

A bubble chart plotting **open rate (x-axis)** vs **click rate (y-axis)** for each job role. Bubble size represents the number of emails sent to that role.

- Upper-right quadrant → high open + high click (most engaged)
- Lower-left quadrant → low open + low click (least engaged)
- High open / low click → audience reads but does not act; content or CTA may need work

#### Time Series Simulation tab

Simulate a future campaign by adjusting four parameters:

| Parameter | Options |
|---|---|
| Number of emails | 100 – 10,000 |
| Estimated improvement | −20% to +50% vs current rates |
| Time period | Next week / Next month / Next quarter |
| Target segment | All recipients / High engagement / Low engagement |

Click **Run Simulation** to generate:

- A two-panel time series chart (daily metrics + cumulative performance)
- Projected opens and clicks with delta vs current
- Estimated ROI assuming $50 value per click

---

## AI Insights

When an API key is provided, a **"Generate AI Insights"** button appears inside the Insights expander. Clicking it sends your campaign metrics to the selected model and streams the response back as formatted Markdown.

The AI is prompted to return:
1. A 2-3 sentence overall engagement summary
2. Observations on open/click rates vs industry benchmarks
3. 3-5 concrete, actionable recommendations
4. Top company and job-role segments to prioritise

The response is stored in `st.session_state` so it persists across Streamlit reruns until you click **Clear AI Insights** or change the provider.

### OpenAI Setup

1. Sign up or log in at [platform.openai.com](https://platform.openai.com).
2. Go to **API Keys** and create a new secret key.
3. In the sidebar select **OpenAI**, paste your key, and choose a model:

| Model | Best for |
|---|---|
| `gpt-4o` | Highest quality, slightly slower |
| `gpt-4o-mini` | Faster and cheaper, still very capable |

Install the package if you have not already:

```bash
pip install openai
```

### Google Gemini Setup

1. Go to [aistudio.google.com](https://aistudio.google.com) and sign in.
2. Click **Get API key** to generate a key for the Gemini API.
3. In the sidebar select **Google Gemini**, paste your key, and choose a model:

| Model | Best for |
|---|---|
| `gemini-2.0-flash` | Fast responses, low latency |
| `gemini-2.5-pro` | Most capable Gemini model, deeper analysis |

Install the package if you have not already:

```bash
pip install google-generativeai
```

### How It Works

```
User data + filters
        │
        ▼
build_insights_prompt()
  ├── Campaign KPIs (sent, opened, clicked, rates)
  ├── Industry benchmark comparison
  ├── Company breakdown table (sorted by click rate)
  ├── Job role breakdown table (sorted by click rate)
  └── Active filter context
        │
        ▼
get_ai_insights_openai()   ← if OpenAI selected
get_ai_insights_gemini()   ← if Gemini selected
        │
        ▼
Markdown response displayed in the Insights expander
```

Both functions follow the same `(result, error)` return pattern. If the API call fails for any reason (wrong key, quota exceeded, network error, missing package) the error message is shown in a red box and no partial result is stored.

---

## Project Structure

```
e-camp/
├── streamlit-app.py   # entire application — single-file Streamlit app
└── README.md          # this file
```

### Key functions inside `streamlit-app.py`

| Function | Purpose |
|---|---|
| `convert_binary_data(df)` | Normalises yes/no/true/false/1/0 columns to integer 0 or 1 |
| `calculate_metrics(df)` | Adds `OpenRate`, `ClickRate`, `CTOR`, and `EngagementScore` columns |
| `generate_industry_benchmarks()` | Returns the reference benchmark dictionary |
| `build_insights_prompt(metrics)` | Builds the structured prompt sent to the AI model |
| `get_ai_insights_openai(prompt, key, model)` | Calls the OpenAI Chat Completions API |
| `get_ai_insights_gemini(prompt, key, model)` | Calls the Google Gemini API |
| `create_sample_data()` | Returns a cached 19-row sample DataFrame |

---

## Troubleshooting

**"Missing required columns" error**
Ensure your CSV has all six columns with names spelled exactly as shown in [CSV Data Format](#csv-data-format).

**Charts are empty after filtering**
The selected company or job role filter may have returned zero rows. Try widening the filter or selecting "All Companies" / "All Roles".

**"Generate AI Insights" button is greyed out**
The API key field in the sidebar is empty. Paste your key there — the button enables automatically.

**`openai` or `google-generativeai` import error in the app**
The relevant package is not installed in your current Python environment. Run the install command shown in [Installation](#installation) and restart the app.

**AI error: incorrect API key**
Double-check that you copied the full key without leading or trailing spaces. OpenAI keys start with `sk-`; Gemini keys start with `AIza`.

**Streamlit reruns and AI response disappears**
This should not happen — the response is stored in `st.session_state.ai_insights`. If it does, click **Generate AI Insights** again or check that your Streamlit version supports session state (any version ≥ 0.84).
