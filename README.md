# Customer Satisfaction Dashboard for Grandview Energy

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
   ![Highlights](media/image1.png)

2. **Demographics and NPS Insights**:
   ![Demographics and NPS](media/image2.png)

3. **Question Categories and Insights**:
   ![Question Categories](media/image3.png)

4. **Energy Cost Savings and Customer Suggestions**:
   ![Energy Cost Savings](media/image4.png)

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

    - Use the global slicers on the right-hand side to filter data across all pages.
    
    - Each dashboard has independent filters for more granular analysis.
    
    - A global date filter is available on the left-hand side.

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
