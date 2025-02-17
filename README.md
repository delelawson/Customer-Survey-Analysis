# Customer Satisfaction Dashboard for Grandview Energy (Dummy company)

## Overview
This dashboard is designed to help **Grandview Energy** understand customer sentiment and address concerns based on a comprehensive survey with over 30 questions. The survey covers key metrics such as **Net Promoter Score (NPS)**, **Customer Satisfaction (CSAT)**, and detailed feedback on various aspects of the company's services.

### Key Metrics
- **Net Promoter Score (NPS)**: Measures customer loyalty and likelihood to recommend Grandview Energy.
- **Customer Satisfaction (CSAT)**: Assesses satisfaction with specific aspects of the business.
- **Survey Categories**:
  - Professionalism of the Customer Service Team
  - Quality of Reporting and Analysis
  - System Performance
  - Cost Savings and Billing Satisfaction
  - Operations and Maintenance

### Target Audience
This dashboard is intended for **Grandview Energy management and stakeholders** to make data-driven decisions that address customer concerns.

---

## Key Features
The dashboard is built using **Power BI** and includes the following features:

### Main Components
- **Interactive Visuals**: Charts, tables, and word clouds.
- **Filters**: Dropdowns, slicers, and bookmarks for dynamic data exploration.
- **Unique Features**:
  - Slicers limited to their respective canvas views.
  - Word clouds generated from customer comments using a Python backend script.
  - Radar charts for comparative analysis.

### Screenshots
Here are some highlights from the dashboard:

1. **Highlights**:
   <img width="700" alt="Image" src="https://github.com/user-attachments/assets/653ab062-248f-4de5-9c27-f53cebe8c062" />

2. **Demographics and NPS Insights**:
   <img width="700" alt="Image" src="https://github.com/user-attachments/assets/5ce84d4e-2e24-4e13-bc56-f7ea05359a4f" />

3. **Question Categories and Insights**:
   <img width="700" alt="Image" src="https://github.com/user-attachments/assets/a0d77926-d587-42f4-9ae0-cd9518bbfdf6" />

4. **Energy Cost Savings and Customer Suggestions**:
   <img width="700" alt="Image" src="https://github.com/user-attachments/assets/72bddcee-a723-4891-9a46-a2c693da0647" />

---

## Data Sources
The data used in this dashboard comes from an **Excel file** named `survey dummy data.xlsx`, which is included in the repository. The data is divided into three main tables:
1. **General Table**: Contains survey responses and basic customer information.
2. **Feedback Experience Table**: Stores ratings for various service characteristics.
3. **Feedback Comments Table**: Houses customer comments and sentiment analysis results.

### Data Transformations
- The data was cleaned and transformed using **Power Query** in Power BI.
- Python scripts were used for **sentiment analysis** and **keyword extraction** from customer comments.

---

## Technology Stack
The dashboard was built using the following tools:
- **Power BI**: For data visualization and dashboard creation.
- **Python**: For backend sentiment analysis and keyword extraction.

### A view of the python script for sentiments
```bash
import pandas as pd
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
from rake_nltk import Rake
import nltk
from nltk.corpus import stopwords

# Downloading stopwords (run once)
nltk.download("stopwords")

# To load dataset from Power BI
df = dataset

# Initializing sentiment analyzer
analyzer = SentimentIntensityAnalyzer()

# Custom stopwords: Retain words like "is", "to", "of", etc.
custom_stopwords = set(stopwords.words("english")) - {"is", "to", "of","great","satisfactory", "your","for", "also", "good", "high"}

# Initializing RAKE
rake = Rake(
    min_length=1,
    max_length=4,
    stopwords=custom_stopwords
)

# Define experience related attributes
experience_attributes = [
    "Service quality",
    "Professionalism and responsiveness",
    "Performance reporting and insights",
    "Technical performance",
    "Solar system reliability",
    "Energy cost reduction",
    "Maintenance service timeliness and quality",
    "Fault resolution",
    "Customer support accessibility",
    "Billing statement clarity",
    "Pricing and payment transparency",
    "Solar solutions' sustainability impact"
]

# Function to categorize Attribute
def _categorize_attribute(attribute):
    if pd.isna(attribute):
        return "General"

    attribute = str(attribute).lower()

    # Check for experience attributes
    if attribute.title() in experience_attributes:
        return "Experience"

    # Special case: Handle "Reason for Recommendations"
    if attribute.startswith("reason for recommendations"):
        return "Normal"  # Will be processed as a general comment

    key_value_triggers = ["value most", "key value", "primary reason", "key strength"]
    importance_triggers = ["how important", "critical", "crucial", "significance"]

    if any(trigger in attribute for trigger in key_value_triggers):
        return "KeyValue"
    if any(trigger in attribute for trigger in importance_triggers):
        return "Importance"
    if any(word in attribute for word in ["improve", "suggest","what improvement", "wish"]):
        return "Suggestion"

    return "General"

# Function to analyze sentiment with Attribute context
def _analyze_sentiment(comment, attribute_type):
    if pd.isna(comment) or comment.strip().lower() in ["test", "n/a"]:
        return "Neutral"

    comment_lower = comment.lower()
    score = analyzer.polarity_scores(comment)["compound"]

    # Handle Experience attributes
    if attribute_type == "Experience":
        return "Positive" if score >= 0.1 else ("Negative" if score <= -0.1 else "Neutral")

    # Normal evaluation for "Reason for Recommendations"
    if attribute_type == "Normal":
        return "Positive" if score >= 0.1 else ("Negative" if score <= -0.1 else "Neutral")

    # Handle KeyValue context
    if attribute_type == "KeyValue":
        if any(word in comment_lower for word in ["good", "excellent", "professionalism"]):
            return "Positive"
        return "Positive" if score >= 0 else "Neutral"

    # Handle Importance context
    if attribute_type == "Importance":
        if "important" in comment_lower or "critical" in comment_lower:
            return "Positive"
        return "Positive" if score >= -0.05 else "Negative"

    # Handle Suggestion context (default to Neutral)
    if attribute_type == "Suggestion":
        return "Neutral" if -0.1 <= score <= 0.1 else ("Positive" if score > 0.1 else "Negative")

    # Default sentiment calculation
    return "Positive" if score >= 0.1 else ("Negative" if score <= -0.1 else "Neutral")

# Function to extract key phrases that match sentiment
def _extract_best_key_phrase(comment, sentiment):
    if pd.isna(comment) or comment.strip().lower() in ["test", "n/a"]:
        return "No key phrases"

    # Extract phrases using RAKE
    rake.extract_keywords_from_text(comment)
    phrases = rake.get_ranked_phrases()

    # Ensure phrases match sentiment
    relevant_phrases = []
    for phrase in phrases:
        phrase_score = analyzer.polarity_scores(phrase)["compound"]

        if sentiment == "Positive" and phrase_score > 0.1:
            relevant_phrases.append(phrase)
        elif sentiment == "Negative" and phrase_score < -0.1:
            relevant_phrases.append(phrase)

    # Select the most relevant key phrase
    best_phrase = relevant_phrases[0] if relevant_phrases else None

    # Fallback: Use first 2-4 meaningful words if RAKE fails
    if not best_phrase:
        words = [word for word in comment.split() if word.lower() not in custom_stopwords]
        best_phrase = " ".join(words[:4]) if words else "No key phrases"

    return best_phrase

# Apply functions to dataset
df["Sentiment"] = df.apply(lambda row: _analyze_sentiment(row["Comments"], _categorize_attribute(row["Attribute"])), axis=1)
df["Key Phrases"] = df.apply(lambda row: _extract_best_key_phrase(row["Comments"], row["Sentiment"]), axis=1)

# Output only the required columns
dataset = df
```
### Dependencies
To run the Python scripts, the following dependencies are required:
```bash
pip install pandas
pip install vaderSentiment
pip install rake-nltk
pip install nltk
```
### Additionally, download the NLTK stopwords corpus:
```bash
import nltk
nltk.download("stopwords")
```
### Installation and Setup
- 1. Clone this repository to your local machine.

- 2. Open the survey dummy data.xlsxfile in Power BI.

- 3. Install the required Python dependencies (listed above).

- 4. Run the Power BI file (dashboard.pbix) to view the dashboard.


### Usage Instructions
- **Navigation:** Scroll through the dashboard to explore different sections.

- **Filters:**

    - Use the global slicers on the right-hand side to filter data across all pages. <img width="160" alt="Image" src="https://github.com/user-attachments/assets/a34b5f93-e71d-480e-b2ef-8c15e606dc56" />
    
    - Each dashboard has independent filters for more granular analysis.

### Planned Updates
Future updates to the dashboard will include:

- Tracking yearly changes across all metrics (NPS and CSAT).

- Additional visualizations for deeper insights.

### Contact Information
For questions or feedback, feel free to reach out:

Email: silasdele121@gmail.com

### Acknowledgments
Python Libraries: pandas, vaderSentiment, rake-nltk, and nltk for sentiment analysis and keyword extraction.

Power BI: For enabling interactive and dynamic data visualization.


### License
This project is licensed under the MIT License. Feel free to use and modify the code as needed.

---

Downloadable Word Document
Below is the **Word document** containing the same content. You can download it and preview it in Word format.

[Download README.docx](https://files.fm/u/2z8y5b5b5)

---
