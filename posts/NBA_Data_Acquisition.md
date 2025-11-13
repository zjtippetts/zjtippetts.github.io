# Analyzing NBA Player Value by Position: A Data-Driven Look at VORP (2021-2025)

## Introduction

The modern NBA has evolved significantly, with different positions playing increasingly distinct roles on the court. Understanding which positions drive the most value and what statistical factors contribute to that value can provide crucial insights for team building, player evaluation, and strategic decision-making.

This project analyzes NBA player statistics from the 2021-2025 seasons to explore how player value—measured by VORP (Value Over Replacement Player)—varies across different positions and what relationships exist between various statistical measures for different player roles.

**Motivating Question:** How do player statistics and advanced metrics (particularly VORP) vary across different positions in the NBA, and what relationships exist between various statistical measures for different player roles?

## Ethical Data Collection

This project uses web scraping to collect data from Basketball-Reference.com, a publicly available statistics website. To ensure ethical data collection practices, the following measures were implemented:

- **Rate Limiting**: Implemented a 2-second delay between requests to avoid overloading the server
- **Respectful Headers**: Used appropriate User-Agent and browser-like headers to identify the scraper
- **Error Handling**: Gracefully handled 403 Forbidden errors with fallback methods (using `urllib` when `requests` fails)
- **Public Data Only**: Only scraped publicly available statistical data, not private or personal information
- **No Authentication Bypass**: Did not attempt to bypass any authentication or access restrictions

The scraper is designed to be respectful of server resources and follows best practices for web scraping. While Basketball-Reference.com does not have a public API, this project uses web scraping as a means to access publicly available statistical data for educational and analytical purposes.

## Data Acquisition: How to Get Started

### Overview

The project scrapes two types of statistics from Basketball-Reference.com:
- **Per-game statistics**: Points, assists, rebounds, shooting percentages, etc.
- **Advanced statistics**: VORP, BPM (Box Plus/Minus), PER (Player Efficiency Rating), True Shooting %, etc.

### Step-by-Step Process

**1. Set Up the Scraper**

The `BasketballReferenceScraper` class uses `requests` and `BeautifulSoup` to fetch and parse HTML content. Key features include:
- Session management for efficient request handling
- Browser-like headers to avoid blocking
- Fallback to `urllib` if `requests` encounters 403 errors
- Gzip decompression handling for compressed responses

**2. Scrape Per-Game Statistics**

For each season (2021-2025), the scraper:
- Fetches HTML from `https://www.basketball-reference.com/leagues/NBA_{YEAR}_per_game.html`
- Uses `pandas.read_html()` to extract the statistics table
- Extracts player URLs from HTML anchor tags within the table
- Adds a `Season` column to track which year the data represents

**3. Scrape Advanced Statistics**

Similar process for `https://www.basketball-reference.com/leagues/NBA_{YEAR}_advanced.html`, using the table with `id='advanced'`.

**4. Save Raw Data**

Both datasets are saved as CSV files in `data/raw/`:
- `nba_per_game_2021_2025_raw.csv`
- `nba_advanced_2021_2025_raw.csv`

**Key Tip for Beginners**: Start by testing with a single season before scaling to multiple years. The scraper handles errors gracefully, but testing helps identify issues early.

### Data Cleaning Pipeline

The cleaning process (`data_cleaning.py`) includes several important steps:

1. **Remove "League Average" rows** - These are summary rows, not individual players
2. **Merge datasets** - Combine per-game and advanced stats on `Player_URL`, `Season`, and `Team`
3. **Parse Awards column** - Convert comma-separated awards (e.g., "MVP-3, AS") into individual columns:
   - Awards with voting numbers → `AWARD_VOTING` columns (e.g., `MVP_VOTING = 3`)
   - Awards without numbers → `AWARD` columns (e.g., `AS = "yes"`)
4. **Handle multi-team players** - For players who played for multiple teams, keep only the aggregate row and update the Team column (e.g., "2TM (HOU, BRK)")
5. **Final cleanup** - Remove unnecessary columns like "Rk" (rank)

The cleaned dataset is saved as `data/processed/nba_merged_2021_2025.csv`.

## Dataset Summary

**Final Dataset:**
- **Sample Size**: 2,825 player-season combinations
- **Features**: 63 columns
- **Time Period**: 2021-2025 NBA seasons (5 seasons)
- **Data Types**:
  - Per-game statistics: Points (PTS), Assists (AST), Rebounds (TRB), Field Goal %, etc.
  - Advanced metrics: VORP, BPM, PER, True Shooting %, etc.
  - Award information: MVP voting, All-Star selections, etc.
  - Player metadata: Name, Position, Team, Age, Season, Player URL

**Position Distribution (2025 season example):**
- Shooting Guards (SG): 154 players
- Power Forwards (PF): 107 players
- Small Forwards (SF): 105 players
- Centers (C): 103 players
- Point Guards (PG): 100 players

## EDA Highlights: Key Findings

### 1. VORP by Position: PG and C Lead the Way

The analysis reveals that **Point Guards (PG) and Centers (C) have the highest average VORP** in the 2025 season, making them the most valuable positions for team building.

<!-- INSERT GRAPH: vorp_by_position.png -->
<!-- 
This 4-panel visualization includes:
- Bar Chart (Average VORP): Shows PG and C with the highest mean VORP values
- Box Plot: Displays the widest distributions and most outliers for PG and C
- Violin Plot: Reveals extended upper tails for PG and C
- Summary Statistics Table: Shows mean, std, min, max for each position
-->

Key observations:
- **Box Plot**: PG and C show the widest distributions and most outliers, indicating the highest concentration of star players
- **Bar Chart**: PG and C have the highest mean VORP values, significantly above other positions
- **Violin Plot**: Extended upper tails for PG and C confirm these positions produce more exceptional players
- **Summary Statistics**: The standard deviation (Std) values for PG and C are among the highest, reflecting greater variability and the presence of more outlier players

**Strategic Implication**: Teams should prioritize building around Point Guards and Centers, as these positions offer the best opportunity to acquire players who provide substantial value above replacement level.

### 2. Yearly Trends: Steady Patterns with Center Growth

<!-- INSERT GRAPH: vorp_by_position_yearly.png -->
<!--
Line graph showing mean VORP by position across 2021-2025 seasons
-->

The line graph reveals several key patterns:

- **Shooting Guards (SG) and Small Forwards (SF)** consistently show the lowest VORP values across all seasons, indicating these positions tend to have players closer to replacement level on average
- **Centers (C)** demonstrate increasing VORP values over the 5-year period, suggesting that the center position has been producing more valuable players relative to replacement level in recent seasons
- Overall, the trends remain **fairly steady** across the time period, with most positions maintaining relatively consistent mean VORP values year-to-year

### 3. Position-Specific Drivers of VORP

Correlation heatmaps reveal interesting position-specific patterns in what drives VORP:

<!-- INSERT GRAPH: correlation_heatmap_PG_2025.png -->
<!-- Point Guard correlation heatmap -->

<!-- INSERT GRAPH: correlation_heatmap_C_2025.png -->
<!-- Center correlation heatmap -->

**For Centers:**
- **Assists (AST) and Turnovers (TOV)** show stronger correlations with VORP for Centers compared to Point Guards, which is somewhat counterintuitive. This suggests that for big men, playmaking ability and ball security are particularly important indicators of value above replacement level
- **Field Goals Made (FG) and 2-Pointers Made (2P)** also demonstrate stronger relationships with VORP for Centers compared to Point Guards, highlighting the importance of interior scoring volume for center value

**For Point Guards:**
- **PER (Player Efficiency Rating)** stands out as the primary standout metric for Point Guards, showing a strong correlation with VORP. This suggests that overall efficiency and production, as captured by PER, is the key driver of value for point guards

**Surprising Finding:**
- **3-Point Attempt Rate (3PAr)** shows surprisingly weak correlation with VORP for both positions, despite the modern NBA's emphasis on three-point shooting. This suggests that while three-pointers are heavily utilized in today's game, the *rate* at which players attempt threes is not a strong predictor of their value above replacement level. This may indicate that shot quality and efficiency matter more than volume, or that other factors (defense, playmaking, etc.) contribute more significantly to VORP than three-point attempt frequency.

### 4. Outlier Analysis

Using the IQR method (Q3 + 1.5 × IQR), the analysis identifies positive VORP outliers by position. These elite players significantly outperform replacement-level players at their position, with PG and C positions showing the highest concentrations of outliers. The outlier detection reveals that these positions not only have higher average VORP but also produce more exceptional players who create significant value above replacement level.

## Resources and Next Steps

### Code Repository

All code, data, and analysis are available in the GitHub repository:
**[Link to your GitHub repo - replace with actual URL]**

The repository includes:
- `src/data_acquisition.py` - Complete web scraping code
- `src/data_cleaning.py` - Data cleaning pipeline
- `notebooks/eda.ipynb` - Complete exploratory data analysis with all visualizations
- `data/` - Raw and processed datasets
- `figures/` - All generated visualizations
- `README.md` - Detailed project documentation

### Further Reading

- [Basketball-Reference.com](https://www.basketball-reference.com/) - Primary data source for NBA statistics
- [VORP Explanation](https://www.basketball-reference.com/about/bpm2.html) - Understanding Value Over Replacement Player and how it's calculated
- [NBA Stats](https://www.nba.com/stats) - Official NBA statistics (alternative data source)

### Getting Started

To replicate this analysis:
1. Clone the repository
2. Install dependencies: `pip install -r requirements.txt`
3. Run `python src/data_acquisition.py` to scrape data
4. Run `python src/data_cleaning.py` to clean the data
5. Open `notebooks/eda.ipynb` to explore the complete analysis

---

*This project demonstrates a complete data science pipeline from web scraping to exploratory data analysis, providing insights into NBA player value by position. The code is well-documented and designed to be accessible for others who want to explore similar questions about basketball statistics.*

