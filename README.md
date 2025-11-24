# Renewable Energy Site Selection in Karnataka

### A GIS & MCDM-based Decision Support System

**TEAM MEMBERS**
- Aryan Rastogi - MT2024026
- Deepanshu Saini - MT2024039
- Kapil Aditya Reddy - IMT2023052
- Shivam Padaliya - MT2024107

---

## Overview
Finding the perfect spot for a solar park or wind farm isn't just about looking for sun or wind. It's a complex puzzle involving terrain, land use, infrastructure, and climate patterns.

This project solves that puzzle for the state of **Karnataka, India**. We combined **Geographic Information Systems (GIS)** with the **Analytic Hierarchy Process (AHP)** to scientifically rank 27 districts based on their suitability for renewable energy projects.

## Key Features
-   **Smart Decision Making:** Uses AHP to weigh conflicting factors like Wind Speed vs. Slope.
-   **Data-Driven:** Analyzes thousands of data points including meteorological data, population density, and land use.
-   **Interactive Visualization:** A modern React-based dashboard to explore the results on dynamic maps.
-   **Multi-Criteria Analysis:** Considers 10+ parameters including Solar Radiation, Temperature, Elevation, and Proximity to Highways.

## Tech Stack
-   **Analysis & Modeling:** Python, Pandas, Ahpy, Folium
-   **Frontend:** React, Vite, Tailwind CSS, Leaflet
-   **Data:** Open-Elevation API, Meteorological Datasets

## Getting Started

### Prerequisites
-   Python 3.8+
-   Node.js & npm

### 1. Run the Analysis (Backend)
The core logic resides in Jupyter Notebooks.
```bash
cd code
# Install dependencies (pandas, ahpy, etc.)
# Run GIS_Notebook_2.ipynb to generate the rankings
```

### 2. Launch the Dashboard (Frontend)
Visualize the results.
```bash
cd react-app
npm install
npm run dev
```

## Project Structure
```
├── code/               # Python Notebooks for GIS & AHP Analysis
├── react-app/          # Interactive Web Application
├── Research Papers/    # Academic references
└── COMPREHENSIVE_PROJECT_REPORT.md  # Detailed documentation
```

## Why This Matters
As India pushes towards its renewable energy goals, efficient site selection can save millions in infrastructure costs and maximize energy generation. This tool bridges the gap between raw data and strategic decision-making.

---
*Check out the [Comprehensive Report](COMPREHENSIVE_PROJECT_REPORT.md) for deep technical details.*
