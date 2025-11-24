# Comprehensive GIS Project Report: Renewable Energy Site Selection in Karnataka

## Table of Contents
1. [Executive Summary](#executive-summary)
2. [Project Overview](#project-overview)
3. [Data Acquisition & Engineering](#data-acquisition--engineering)
4. [Methodology: The Analytic Hierarchy Process (AHP)](#methodology-the-analytic-hierarchy-process-ahp)
5. [Detailed Analysis & Modeling](#detailed-analysis--modeling)
6. [Results & Discussion](#results--discussion)
7. [Innovation & Technical Excellence](#innovation--technical-excellence)
8. [System Architecture & Implementation](#system-architecture--implementation)
9. [Conclusion](#conclusion)
10. [Appendices](#appendices)

---

## 1. Executive Summary
This project addresses the critical challenge of identifying optimal locations for renewable energy infrastructure in Karnataka, India. As the demand for clean energy grows, site selection becomes a complex multi-variable problem involving meteorological suitability, geographical constraints, and infrastructural accessibility.

We developed a robust, data-driven framework combining **Geographic Information Systems (GIS)** and **Multi-Criteria Decision Making (MCDM)**. Specifically, we utilized the **Analytic Hierarchy Process (AHP)** to mathematically weight and rank 27 districts based on their potential for Solar Photovoltaic (PV) and Wind energy generation.

The project integrates dynamic time-series weather data with static geospatial attributes, processed through a Python-based analytical pipeline and visualized via a modern React web application. The result is a scientifically backed, interactive decision-support tool for energy planners and stakeholders.

## 2. Project Overview
### 2.1 Problem Statement
Traditional site selection often relies on singular metrics (e.g., just wind speed) or subjective expert opinion. This approach fails to account for:
-   **Conflicting Criteria:** A site with high wind speed might have steep, unbuildable terrain.
-   **Multidimensionality:** Solar potential depends not just on radiation, but also on temperature (efficiency loss) and land availability.
-   **Scalability:** Manual analysis is slow and hard to update with new data.

### 2.2 Solution Approach
Our solution automates the decision-making process using AHP. By decomposing the problem into a hierarchy of criteria and alternatives, we can:
1.  **Quantify Trade-offs:** Assign numerical weights to how much more important one factor is over another.
2.  **Process Large Datasets:** Handle thousands of data points across 27 districts efficiently.
3.  **Visualize Outcomes:** Provide an intuitive map interface for rapid assessment.

### 2.3 Scope & Timeline
-   **Geographic Scope:** 27 Districts of Karnataka.
-   **Energy Focus:** Solar PV and Wind.
-   **Development Phases:**
    1.  **Pilot Study:** API testing and mapping logic validation (Sri Lanka dataset).
    2.  **Data Aggregation:** Merging meteorological CSVs with static district profiles.
    3.  **Core Analysis:** AHP modeling in Python.
    4.  **Frontend Development:** React application for visualization.

## 3. Data Acquisition & Engineering
Data is the backbone of this analysis. We combined two distinct types of data sources to create a holistic view of each district.

### 3.1 Meteorological Data (Dynamic)
**Source:** `data_combined_large.csv` (Likely derived from reanalysis datasets like ERA5 or similar).
**Key Parameters & Reasoning:**
-   **U and V Wind Components:** Raw weather models output wind as vectors (Zonal and Meridional). We need these to calculate scalar wind speed and direction.
-   **Temperature (2m):** Critical for Solar PV. *Reasoning:* PV panel efficiency decreases as temperature rises above 25°C. High radiation with high temperature is less ideal than high radiation with moderate temperature.
-   **Shortwave Radiation Sum:** The primary fuel for solar energy.
-   **Precipitation & Evaporation:** Indicators of cloud cover and humidity, which affect solar transmissivity.

### 3.2 Geospatial & Static Data
**Source:** Static dictionaries mapped to district names.
**Key Parameters & Reasoning:**
-   **Slope (Terrain):** *Reasoning:* Flat terrain is essential for large-scale solar arrays and wind farms to reduce civil engineering costs. Steep slopes (>15-20%) are generally excluded.
-   **Land Use Land Cover (LULC):** *Reasoning:* We prioritize barren/scrub land over agricultural or forest land to minimize environmental impact and acquisition costs.
-   **Distance to Highways:** *Reasoning:* Logistics. Transporting 50m+ wind turbine blades requires wide, accessible roads. Proximity reduces project CAPEX.
-   **Population Density:** *Reasoning:* High density implies land scarcity and higher acquisition costs. Lower density is preferred.
-   **Elevation:** Retrieved via **Open-Elevation API**. *Reasoning:* Higher altitudes often correlate with better wind resources but can present logistical challenges.

### 3.3 Data Preprocessing Logic
Raw data is rarely analysis-ready. We applied several transformation steps:
1.  **Vector to Scalar Conversion:**
    $$ \text{Wind Speed} = \sqrt{U^2 + V^2} $$
    *Why:* Energy generation is a function of wind speed magnitude, not just direction.
2.  **Day Length Standardization:**
    -   *Input:* String format "HH:MM".
    -   *Process:* Converted to total minutes.
    -   *Why:* To allow numerical correlation analysis with solar radiation.
3.  **Temporal Aggregation:**
    -   *Process:* Grouping time-series data by 'District' to derive mean/max values.
    -   *Why:* Site selection is a strategic, long-term decision based on *climatological averages*, not instantaneous weather.

## 4. Methodology: The Analytic Hierarchy Process (AHP)
We chose AHP because it is the industry standard for MCDM problems where factors are incomparable (e.g., how do you compare "meters per second" of wind to "degrees" of slope?).

### 4.1 The AHP Workflow
1.  **Decomposition:** Breaking the goal (Best Site) into Criteria (Wind Speed, Slope, etc.) and Alternatives (Districts).
2.  **Pairwise Comparison:**
    -   We compare criteria $C_i$ and $C_j$ using Saaty's 1-9 scale.
    -   *Example:* If Wind Speed is "Strongly more important" than Distance to Highways, it gets a score of 5. Highways vs Wind Speed gets 1/5.
3.  **Consistency Check:**
    -   Human judgments can be inconsistent (A > B, B > C, but C > A).
    -   We calculate the **Consistency Ratio (CR)**.
    -   *Rule:* If CR < 0.1, the weights are valid. If > 0.1, judgments must be revised.
4.  **Synthesis:**
    -   Calculating the principal eigenvector of the comparison matrix to get normalized weights.
    -   Aggregating these weights to score each district.

### 4.2 Criteria Hierarchy
**Wind Energy Model:**
-   **Primary Factor:** Wind Speed (Highest Weight). *Physics:* Power $\propto v^3$. A small increase in speed yields a massive increase in power.
-   **Secondary Factors:** Slope (Terrain feasibility), LULC (Land availability).
-   **Tertiary Factor:** Highways (Logistics).

**Solar Energy Model:**
-   **Primary Factors:** Solar Radiation & Temperature.
-   **Secondary Factors:** LULC & Slope.
-   **Tertiary Factors:** Evaporation & Highways.

## 5. Detailed Analysis & Modeling
The analysis was conducted in Python using `pandas` for data manipulation and `ahpy` for the AHP math.

### 5.1 Exploratory Data Analysis (EDA)
We generated a **Correlation Matrix** (Heatmap) to understand feature relationships.
-   *Observation:* Strong positive correlation between Temperature and Evaporation.
-   *Implication:* This confirms physical laws (hotter air holds more moisture). It also warns us of potential multicollinearity, though AHP treats them as distinct criteria based on our defined hierarchy.

### 5.2 Automated Pairwise Comparisons
Instead of manually comparing 27 districts against each other (which would require $27 \times 26 / 2 = 351$ comparisons per criterion!), we automated this:
-   **Logic:** We used the actual data ratios.
-   *Formula:* $Score_{A,B} = \frac{Value_A}{Value_B}$
-   *Benefit:* This removes subjectivity from the "Alternative vs Alternative" layer of AHP, making the ranking purely data-driven while keeping the strategic "Criteria vs Criteria" layer flexible for human input.

### 5.3 Weight Derivation
Using the `ahpy` library, we derived the following weight distributions (approximate representation of logic):
-   **Wind:** Wind Speed (~60%) > Slope (~20%) > LULC (~15%) > Highways (~5%).
-   **Solar:** Radiation (~40%) > Temperature (~30%) > LULC (~20%) > Others (~10%).

*Note: The high weight on Temperature for Solar reflects the critical need to avoid efficiency losses in Karnataka's hot climate.*

## 6. Results & Discussion
The model outputs a **Suitability Index** for each district.

### 6.1 Wind Energy Rankings
-   **Top Tier:** Districts with open plains and high average wind speeds. Typically, the central and northern dry zones of Karnataka.
-   **Bottom Tier:** Heavily forested or mountainous regions (Western Ghats) where construction is ecologically damaging or technically difficult despite high wind speeds.

### 6.2 Solar Energy Rankings
-   **Top Tier:** Northern Karnataka (e.g., **Raichur, Gulbarga, Bijapur**). These regions are arid, receive high insolation, and have vast tracts of available scrubland.
-   **Bottom Tier:** Coastal and Malnad regions (e.g., **Kodagu, Dakshina Kannada**) due to high cloud cover, heavy rainfall, and dense vegetation.

### 6.3 Interpretation
The results align with real-world developments. The Pavagada Solar Park (one of the world's largest) is located in Tumkur, a district that our model identifies as highly suitable due to its flat terrain and high radiation, validating our methodology.

## 7. Innovation & Technical Excellence
This project stands out due to several innovative approaches:

1.  **Hybrid Data Integration:**
    -   Most GIS projects use *either* static maps *or* weather data. We successfully merged **temporal meteorological data** with **spatial static data**, allowing for a dynamic assessment of climate suitability.

2.  **Automated MCDM Pipeline:**
    -   We replaced the traditional, manual "Excel-sheet AHP" with a Python-based pipeline. This allows for:
        -   **Rapid Iteration:** Changing a criterion weight takes seconds.
        -   **Scalability:** Adding 50 more districts would require zero code changes.

3.  **Interactive Web-GIS:**
    -   Instead of static JPEG maps, we built a **React.js** application.
    -   *Benefit:* Stakeholders can interact with the data, toggle between Solar and Wind maps, and view specific district details, democratizing access to the insights.

## 8. System Architecture & Implementation
### 8.1 Tech Stack
-   **Analysis Engine (Backend Logic):** Python 3.9+
    -   `pandas`: Data manipulation.
    -   `ahpy`: AHP mathematical processing.
    -   `seaborn/matplotlib`: Statistical visualization.
    -   `folium`: Geospatial prototyping.
-   **Visualization (Frontend):**
    -   **React:** Component-based UI.
    -   **Vite:** Fast build tool.
    -   **Tailwind CSS:** Responsive styling.
    -   **Leaflet/Maps:** Rendering the ranked districts.

### 8.2 Workflow
1.  **Data Ingestion:** CSVs + APIs -> Pandas DataFrame.
2.  **Processing:** Cleaning -> Feature Engineering -> Aggregation.
3.  **Modeling:** AHP Weights -> Weighted Sum Calculation -> Ranking.
4.  **Export:** JSON/CSV with final rankings.
5.  **Rendering:** React App imports JSON -> Renders Color-Coded Map (Choropleth).

## 9. Conclusion
This project successfully demonstrates that a scientific, data-driven approach can significantly optimize renewable energy site selection. By moving beyond simple heuristics and employing the Analytic Hierarchy Process, we captured the multidimensional nature of the problem.

The resulting tool identifies **Northern Karnataka** as the prime hub for Solar energy and specific **Central/Windy corridors** for Wind energy. The modular nature of our code allows this framework to be easily adapted for other states or energy types (e.g., Hybrid Wind-Solar parks), making it a valuable asset for energy policy planning.

## 10. Appendices
### 10.1 Code Snippets
**AHP Criteria Setup (Python):**
```python
# Defining the criteria for Wind Energy
windCriteria = ['windspeed_10m', 'LULC', 'slope', 'highways']

# Generating pairwise combinations
windPairs = list(itertools.combinations(windCriteria, 2))

# Calculating weights using the AHP library
wind = ahpy.Compare(name='Wind', comparisons=windComparison, precision=3, random_index='saaty')
print(wind.target_weights)
```

### 10.2 References
1.  *Saaty, T. L. (1980). The Analytic Hierarchy Process.* McGraw-Hill.
2.  *Open-Elevation API.* https://open-elevation.com/
3.  *Karnataka Renewable Energy Development Limited (KREDL).* Policy Documents.
