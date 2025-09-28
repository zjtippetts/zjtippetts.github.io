# Cleaning Messy Fantasy Football Data with Pandas
## Introduction: Why Clean Data Matters
Fantasy football is all about data—player stats fuel draft picks, lineup decisions, and bragging rights. But raw datasets from sites like [Pro-Football-Reference](https://www.pro-football-reference.com/years/2024/fantasy.htm) often arrive messy: missing values, cryptic column names, or player names with symbols like * (Pro Bowl) or + (First-Team All-Pro). Cleaning this data is crucial for reliable analysis, whether you're ranking players or building a predictive model. In this tutorial, you'll learn how to use Pandas to load a messy fantasy football dataset, clean it step-by-step, and prepare it for analysis. By the end, you'll have a tidy dataset ready for visualization or modeling, plus skills to tackle any messy dataset.
We'll work with a sample CSV, fantasy_football_2024.csv, addressing common issues like missing values, inconsistent names, and special characters. Let's get started!
## Step 1: Loading the Dataset
First, we need to load the dataset into Pandas. Assume our file, fantasy_football_2024.csv, has columns like Player, Tm, FantPt, and Rk, but it's messy—some names have * or +, and column names are unclear.
Here's the code to load and inspect it:

### Load the CSV file
```python
import pandas as pd
df = pd.read_csv('fantasy_football_2024.csv')
```

### Display the first five rows
```python
print(df.head())
```

| Rk | Player           | Tm  | FantPos | Age | G  | GS | Cmp | Att | Yds  | TD | Int | Att | Yds  | Y/A  | TD | Tgt | Rec | Yds  | Y/R  | TD | Fmb | FL | TD | 2PM | 2PP | FantPt | PPR   | DKPt  | FDPt  | VBD | PosRank | OvRank |
|----|-----------------|-----|---------|-----|----|----|-----|-----|------|----|-----|-----|------|------|----|-----|-----|------|------|----|-----|----|----|-----|-----|--------|-------|-------|-------|-----|---------|--------|
| 1  | Saquon Barkley*+ | PHI | RB      | 27  | 16 | 16 | 0   | 0   | 0    | 0  | 0   | 345 | 2005 | 5.81 | 13 | 43  | 33  | 278  | 8.42 | 2  | 2   | 1  | 15 | 3   |     | 322    | 355.3 | 362.3 | 338.8 | 163 | 1       | 1      |
| 2  | Derrick Henry*  | BAL | RB      | 30  | 17 | 17 | 0   | 0   | 0    | 0  | 0   | 325 | 1921 | 5.91 | 16 | 22  | 19  | 193  | 10.16| 2  | 3   | 1  | 18 |     |     | 317    | 336.4 | 343.4 | 326.9 | 159 | 2       | 2      |
| 3  | Jahmyr Gibbs*   | DET | RB      | 22  | 17 | 4  | 0   | 0   | 0    | 0  | 0   | 250 | 1412 | 5.65 | 16 | 63  | 52  | 517  | 9.94 | 4  | 1   | 1  | 20 |     |     | 311    | 362.9 | 369.9 | 336.9 | 153 | 3       | 3      |
| 4  | Lamar Jackson*+ | BAL | QB      | 27  | 17 | 17 | 316 | 474 | 4172 | 41 | 4   | 139 | 915  | 6.58 | 4  | 0   | 0   | 0    |      | 0  | 10  | 5  | 4  | 1   |     | 430    | 430.4 | 445.4 | 434.4 | 140 | 1       | 4      |
| 5  | Ja'Marr Chase*+ | CIN | WR      | 24  | 17 | 16 | 0   | 0   | 0    | 0  | 0   | 3   | 32   | 10.67| 0  | 175 | 127 | 1708 | 13.45| 17 | 0   | 0  | 17 |     |     | 276    | 403   | 406   | 339.5 | 138 | 1       | 5      |


The `.head()` method shows the first few rows, revealing issues like Tm instead of Team or characters at the end of player names.
## Step 2: Spotting Data Issues
Before cleaning, let's identify problems. Common issues in fantasy football datasets include:

- Missing values: Stats like FantPt (fantasy points) may be blank.

- Repetitive column names: The data from the csv has columns with the same name.

- Special characters: Names like Saquon Barkley*+ include flags for accolades.

- Unneeded columns: OvRank (overall rank) may be irrelevant as the Rk column already provides overall rank.


Check for missing values and column names:
### Check for missing values
```python
print(df.isnull().sum())
```

Lists the number of missing entries per column.

### List column names
```python
print(df.columns)
```

Checks for possible column names we can clear up.

## Step 3: Cleaning the Data
Let's clean the dataset step-by-step.

**3.1 Handle Missing Values**

We'll fill missing metric values with 0.

### Fill missing values with 0
```python
# List of columns to skip
skip_cols = ['VBD', 'PosRank', 'OvRank']

# Fill NaN with 0 for all other columns
for col in df.columns:
    if col not in skip_cols:
        df[col] = df[col].fillna(0)
```

**3.2 Clean Player Names and Extract Flags**
Names like Saquon Barkley*+ or Derrick Henry+ need cleaning. We'll create ProBowl and AllPro columns to flag these accolades and remove the symbols.
### Create flag columns
```python
df['ProBowl'] = df['Player'].str.contains('\*', regex=True, na=False)
df['AllPro'] = df['Player'].str.contains('\+', regex=True, na=False)
```

### Remove * and + from Player names
```python
df['Player'] = df['Player'].str.replace('[\*\+]', '', regex=True)
```

**3.3 Rename Columns**
Rename repetetive column names for clarity:
```python
df = df.rename(columns={'Att': 'PassAtt', 'Yds': 'PassYds', 'TD': 'PassTD',
                        'Att.1': 'RushAtt', 'Yds.1': 'RushYds', 'TD.1': 'RushTD',
                        'Yds.2': 'RecYds', 'TD.2': 'RecTD',
                        'TD.3': 'TotalTD', })
```

**3.4 Fix Data Types**
Ensure FantasyPoints is a float for calculations:
```python
df['FantasyPoints'] = df['FantasyPoints'].astype(float)
```

**3.5 Drop Unneeded Columns**
Drop the OvRank column, as it’s not needed for analysis:
```python
df = df.drop(columns=['OvRank'])
```

**3.6 Add Year Column**
```python
df['Yr'] = 2024

cols = ['Yr'] + [c for c in df.columns if c != 'Yr']
df = df[cols]

```

## Step 4: The Cleaned Dataset
After cleaning, our dataset is tidy and analysis-ready. Here's a sample:

```python
print(df.head())
```

| Yr  | Rk | Player           | Tm  | FantPos | Age | G  | GS | Cmp | Att | PassYds | PassTD | Int | RushAtt | RushYds | Y/A | Rush_TD | Tgt | Rec | Rec_Yds | Y/R | Rec_TD | Fmb | FL | TotalTD | 2PM | 2PP | FantPt | PPR   | DKPt  | FDPt  | VBD | PosRank | ProBowl | AllPro |
|-----|----|-----------------|-----|---------|-----|----|----|-----|-----|----------|---------|-----|----------|----------|-----|---------|-----|-----|---------|-----|--------|-----|----|---------|-----|-----|--------|-------|-------|-------|-----|---------|---------|--------|
| 2024 | 1  | Saquon Barkley | PHI | RB      | 27  | 16 | 16 | 0   | 0   | 0        | 0       | 0   | 345      | 2005     | 5.81 | 13      | 43  | 33  | 278     | 8.42 | 2      | 2   | 1  | 15      | 3   | 0   | 322    | 355.3 | 362.3 | 338.8 | 163 | 1       | True    | True   |
| 2024 | 2  | Derrick Henry  | BAL | RB      | 30  | 17 | 17 | 0   | 0   | 0        | 0       | 0   | 325      | 1921     | 5.91 | 16      | 22  | 19  | 193     | 10.16| 2      | 3   | 1  | 18      | 0   | 0   | 317    | 336.4 | 343.4 | 326.9 | 159 | 2       | True    | False  |
| 2024 | 3  | Jahmyr Gibbs   | DET | RB      | 22  | 17 | 4  | 0   | 0   | 0        | 0       | 0   | 250      | 1412     | 5.65 | 16      | 63  | 52  | 517     | 9.94 | 4      | 1   | 1  | 20      | 0   | 0   | 311    | 362.9 | 369.9 | 336.9 | 153 | 3       | True    | False  |
| 2024 | 4  | Lamar Jackson | BAL | QB      | 27  | 17 | 17 | 316 | 474 | 4172     | 41      | 4   | 139      | 915      | 6.58 | 4       | 0   | 0   | 0       | 0    | 0      | 10  | 5  | 4       | 1   | 0   | 430    | 430.4 | 445.4 | 434.4 | 140 | 1       | True    | True   |
| 2024 | 5  | Ja'Marr Chase | CIN | WR      | 24  | 17 | 16 | 0   | 0   | 0        | 0       | 0   | 3        | 32       | 10.67| 0       | 175 | 127 | 1708    | 13.45| 17     | 0   | 0  | 17      | 0   | 0   | 276    | 403   | 406   | 339.5 | 138 | 1       | True    | True   |



With this clean data, you can:
Create visualizations with Matplotlib or Seaborn.
Calculate metrics like points per game.
Build a model to predict future performance.
Conclusion: Try It Yourself!
Cleaning messy fantasy football data with Pandas transforms raw stats into a powerful tool for analysis. You've learned to handle missing values, clean player names, and standardize columns—skills that apply to any dataset. Ready to practice? Clone this repo with the sample dataset and code, run the notebook, and share your cleaned dataset in the comments below!
