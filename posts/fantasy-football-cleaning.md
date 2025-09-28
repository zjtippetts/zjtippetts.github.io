# Cleaning Messy Fantasy Football Data with Pandas
## Introduction: Why Clean Data Matters
Fantasy football is all about data—player stats fuel draft picks, lineup decisions, and bragging rights. But raw datasets from sites like Pro-Football-Reference often arrive messy: missing values, cryptic column names, or player names with symbols like * (Pro Bowl) or + (First-Team All-Pro). Cleaning this data is crucial for reliable analysis, whether you're ranking players or building a predictive model. In this tutorial, you'll learn how to use Pandas to load a messy fantasy football dataset, clean it step-by-step, and prepare it for analysis. By the end, you'll have a tidy dataset ready for visualization or modeling, plus skills to tackle any messy dataset.
We'll work with a sample CSV, fantasy_football_2024.csv, addressing common issues like missing values, inconsistent names, and special characters. Let's get started!
## Step 1: Loading the Dataset
First, we need to load the dataset into Pandas. Assume our file, fantasy_football_2024.csv, has columns like Player, Tm, FantPt, and Rk, but it's messy—some names have * or +, and column names are unclear.
Here's the code to load and inspect it:
import pandas as pd

### Load the CSV file
```python df = pd.read_csv('fantasy_football_2024.csv')```

### Display the first five rows
`print(df.head())`

The .head() method shows the first few rows, revealing issues like Tm instead of Team or names like Christian McCaffrey*.
## Step 2: Spotting Data Issues
Before cleaning, let's identify problems. Common issues in fantasy football datasets include:
Missing values: Stats like FantPt (fantasy points) may be blank.
Cryptic column names: Tm or FantPt are unclear.
Special characters: Names like Patrick Mahomes+ include flags for accolades.
Unneeded columns: Rk (rank) may be irrelevant.
Check for missing values and column names:
### Check for missing values
`print(df.isnull().sum())`

### List column names
`print(df.columns)`

Suppose the output shows:
Column
Missing Values
Player
0
Tm
3
FantPt
8
Rk
0

This confirms missing values in Tm and FantPt, and Rk is likely unnecessary.
## Step 3: Cleaning the Data
Let's clean the dataset step-by-step.
**3.1 Handle Missing Values**
We'll fill missing FantPt values with 0 (assuming no points scored) and drop rows with missing Tm, as team data is critical.
### Fill missing FantPt with 0
`df['FantPt'] = df['FantPt'].fillna(0)`

### Drop rows with missing Tm
`df = df.dropna(subset=['Tm'])`

**3.2 Clean Player Names and Extract Flags**
Names like Christian McCaffrey* or Patrick Mahomes+ need cleaning. We'll create ProBowl and AllPro columns to flag these accolades and remove the symbols.
### Create flag columns
`df['ProBowl'] = df['Player'].str.contains('\*', regex=True, na=False)
df['AllPro'] = df['Player'].str.contains('\+', regex=True, na=False)`

### Remove * and + from Player names
`df['Player'] = df['Player'].str.replace('[\*\+]', '', regex=True)`

Here's the transformation:
Player Before
Player After
ProBowl
AllPro
Christian McCaffrey*
Christian McCaffrey
True
False
Patrick Mahomes+
Patrick Mahomes
False
True
Josh Allen
Josh Allen
False
False

**3.3 Rename Columns**
Rename Tm to Team and FantPt to FantasyPoints for clarity:
`df = df.rename(columns={'Tm': 'Team', 'FantPt': 'FantasyPoints'})`

**3.4 Fix Data Types**
Ensure FantasyPoints is a float for calculations:
`df['FantasyPoints'] = df['FantasyPoints'].astype(float)`

**3.5 Drop Unneeded Columns**
Drop the Rk column, as it’s not needed for analysis:
`df = df.drop(columns=['Rk'])`

## Step 4: The Cleaned Dataset
After cleaning, our dataset is tidy and analysis-ready. Here's a sample:
Player
Team
FantasyPoints
ProBowl
AllPro
Christian McCaffrey
SF
180.5
True
False
Patrick Mahomes
KC
200.3
False
True
Josh Allen
BUF
195.7
False
False

With this clean data, you can:
Create visualizations with Matplotlib or Seaborn.
Calculate metrics like points per game.
Build a model to predict future performance.
Conclusion: Try It Yourself!
Cleaning messy fantasy football data with Pandas transforms raw stats into a powerful tool for analysis. You've learned to handle missing values, clean player names, and standardize columns—skills that apply to any dataset. Ready to practice? Clone this repo with the sample dataset and code, run the notebook, and share your cleaned dataset in the comments below!

