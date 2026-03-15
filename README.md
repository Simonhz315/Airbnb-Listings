# Airbnb-Listings

## 1. Executive Summary
This report documents the exploratory data analysis (EDA) and preprocessing steps performed on a dataset of Airbnb listings in New York City. The notebook focuses on loading, cleaning, and preparing the data for downstream modeling tasks such as price prediction. After removing rows with missing values, the working dataset contains 11,849 listings across 33 features spanning host attributes, property details, geographic information, pricing, review scores, and booking policies.

## 2. Dataset Overview
### 2.1. Source and Scope
The dataset was loaded from a local CSV file (Listings.csv) using pandas with Latin-1 encoding to handle special characters. The data covers Airbnb listings in New York City, with geographic coverage across all five boroughs: Manhattan, Brooklyn, Queens, the Bronx, and Staten Island. The city column uniformly reads “New York,” while the district column distinguishes boroughs.

### 2.2. Dataset Dimensions
After dropping all rows containing missing values (df.dropna()), the dataset was reduced to 11,849 rows and 33 columns. The original dataset was larger, but the aggressive dropna approach removed any listing with at least one null value across all 33 fields. This is a common first-pass cleaning strategy, though it trades completeness for simplicity—particularly in columns like review scores, which are often missing for newer listings that have not yet received reviews.

## 3. Feature Catalog
The 33 columns in the cleaned dataset fall into five thematic groups: host profile, geographic/location, property characteristics, pricing and booking rules, and guest review scores. The full catalog is presented below.
