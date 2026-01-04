# 🏏 IPL Dream11 ETL Pipeline & Fantasy Points Calculator

## 📋 Project Overview
This project implements an end-to-end **Extract, Transform, Load (ETL)** pipeline designed to automate the calculation of fantasy cricket points (Dream11 system) for the Indian Premier League (IPL).

The system extracts raw match ball-by-ball data from a source **AWS RDS (MySQL)** database, transforms the data using **Python & Pandas** to apply complex fantasy point logic (batting, strike rates, boundaries, captaincy bonuses), and loads the processed scoring data into a destination Data Warehouse (target AWS RDS) for downstream usage.

This pipeline serves as the foundational data engineering layer, preparing high-quality, structured data for the Data Analysis team and enabling the Machine Learning team to build future **Fantasy Point Predictor** models.

## 🏗️ Architecture & Workflow

The pipeline follows a classic ETL architecture:

### 1. Ingestion/Seeding (`Insert_data.ipynb`)
* Reads raw historical IPL data from local CSV files (`IPL.csv`).
* Establishes a connection to the Source AWS RDS instance using `mysql.connector`.
* Seeds the raw data into a MySQL database named `dream11` (Table: `ipl`).

### 2. ETL Execution (`Extract_Transform_Load.ipynb`)
* **EXTRACT:** Connects to the Source RDS and retrieves ball-by-ball match data. Merges this with auxiliary CSV datasets (`Player.csv`, `Player_Match.csv`) to enrich data with player details and captaincy status.
* **TRANSFORM:** Aggregates data to calculate key batting metrics (Runs, Balls, Fours, Sixes, Strike Rate). Applies the **Dream11 Points Algorithm** to generate a final score for every batter in every match.
* **LOAD:** Connects to the **Target AWS RDS** instance (`database-2`) and loads the final processed data into a structured table (`batter_points`) using `SQLAlchemy`.

## 🛠️ Tech Stack

* **Language:** Python 3.12+
* **Data Processing:** Pandas, NumPy
* **Database:** AWS RDS (MySQL)
* **Database Connectivity:** `mysql.connector`, `pymysql`, `SQLAlchemy`
* **Environment:** Jupyter Notebook / Google Colab

## 🧠 Dream11 Points Logic (Transformation Layer)

The core transformation logic calculates points based on the official fantasy rules implemented in the `dream11()` function:

### 1. Base Points
* **Run:** +1 point per run
* **Boundary Bonus:** +1 point per Four
* **Six Bonus:** +2 points per Six

### 2. Milestone Bonuses
* **30+ Runs:** +4 points
* **Half-Century (50+ Runs):** +8 points
* **Century (100+ Runs):** +16 points
* **Duck (0 Runs):** -2 points

### 3. Strike Rate (SR) Adjustments
*(Applicable only if batter faces ≥ 10 balls)*
* **SR > 170:** +6 points
* **SR 150.01 - 170:** +4 points
* **SR 130.01 - 150:** +2 points
* **SR 60 - 70:** -2 points
* **SR 50 - 59.99:** -4 points
* **SR < 50:** -6 points

### 4. Captaincy Multiplier
* If the player is marked as **Captain** (`Is_Captain == 1`), their total score is multiplied by **2x**.

## 📂 Repository Structure

```bash
├── data/
│   ├── IPL.csv              # Raw ball-by-ball data
│   ├── Player.csv           # Player metadata (ID, Name, Country)
│   └── Player_Match.csv     # Match specific info (Captaincy status)
├── notebooks/
│   ├── Insert_data.ipynb             # Script to seed raw data into Source DB
│   └── Extract_Transform_Load.ipynb  # Main ETL pipeline script
├── README.md                # Project Documentation
└── requirements.txt         # Python dependencies
```
## 🚀 How to Run

### Prerequisites
Ensure you have access to the AWS RDS instances and the required CSV data files.

**Install Dependencies:**
```bash
pip install pandas mysql-connector-python pymysql sqlalchemy
```

---

## 🚀 ETL Pipeline Workflow

### Step 1: Database Seeding
1. Open `Insert_data.ipynb`
2. Update the **AWS RDS host credentials**
3. Run all cells to upload `IPL.csv` data into the **source database**

---

### Step 2: Run the ETL Pipeline
1. Open `Extract_Transform_Load.ipynb`
2. Ensure the following files are present in the specified directory:
   - `Player.csv`
   - `Player_Match.csv`
3. Run the notebook to:
   - **Extract** data from the source database
   - **Transform** data by calculating Dream11 batter scores
   - **Load** the final results into the destination database table:  
     `batter_points`

---

## 📊 Sample Output Data

The final data loaded into the destination database follows this structure:

| match_id | batter        | score |
|--------:|---------------|------:|
| 598027  | CH Gayle      | 145.0 |
| 501210  | SR Tendulkar  | 98.0  |
| 335982  | BB McCullum   | 158.0 |

> **Note:** The scores shown above are illustrative and are calculated based on the transformation logic applied in the ETL pipeline.

---

## 🔮 Future Scope

### ⏱ Automated Scheduling
- Deploy the ETL pipeline using **Apache Airflow** or **AWS Lambda**
- Automate daily execution during the IPL season

### 🤖 Machine Learning Integration
- Use the `batter_points` table as a **target variable**
- Train regression models to predict player performance in upcoming matches

### 📈 Visualization Dashboard
- Connect the destination RDS to **Power BI** or **Tableau**
- Visualize:
  - Top-performing players
  - Match-wise consistency
  - Performance trends over the season

---

## 🤝 Contribution
Contributions are welcome!  
Please fork the repository and create a pull request for:
- Feature enhancements
- Bug fixes
- Performance improvements

---

## 👤 Author
**Aatish**

---

⭐ If you found this project useful, don’t forget to give it a star!
