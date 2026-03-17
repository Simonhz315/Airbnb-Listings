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
| Column Name              | Description                                                                                      |
|--------------------------|--------------------------------------------------------------------------------------------------|
| `listing_id`                 | Unique identifier for each Airbnb listing  |
| `name`     | Title/description of the listing  |
| `host_id`          | Unique identifier for the host |
| `host_since`         | Date the host joined Airbnb |
| `host_location`        | Graphic location of the host |
| `host_response_time`          | How quickly the host responds(e.g., within an hour)              |
| `host_response_rate`     | Proportion of inquiries the host responds to                                                 |
| `host_acceptance_rate`   | Proportion of booking requests accepted                      |
| `host_is_superhost` | Whether the host holds Superhost status (t/f) |
| `host_total_listings_count` | Number of listings managed by the host |
| `host_has_profile_pic` | whether the host has uploaded a profile picture (t/f) |
| `host_identity_verified` | Whether the host's identity has been verified (t/f) |
| `neighborhood` | Specific neighborhood of the listing |
| `district` | Borough/district |
| `city` | City of the listing |
| `latitude/longitude` | Geographical coordinates of the listing |
| `property_type` | Type of property (e.g., Entire apartment) |
| `room_type` | Listing category (Entire place, Private room, etc.) |
| `accommodates` | Maximum number of guests the listing can host |
| `bedrooms`| Number of bedrooms |
| `amenities` | JSON-formatted list of available amenities |
| `price` | Nightly price in USD |
| `minimum_nights` | Minimum booking length requried |
| `maximum_nights` | Maximum booking length allowed |
| `review_scores_rating` | Overall review rating (0-100) |
| `review_scores_accuracy` | Accuracy sub-score (0-100) |
| `review_scores_cleanliness` | Cleanliness sub-score (0-100) |
| `review_scores_checkin` | Check-in sub-score (0-100) |
| `review_scores_communication` | Communication sub-score (0-100) |
| `review_scores_location` | Location sub-score (0-100) |
| `review_scors_value` | Value sub-score (0-100) |
| `instant_bookable` | Whether the listing can be booked instantly (t/f) |

## 4. Data Cleaning and Preprocessing
### 4.1. Missing Value Treatment
The notebook applies df.dropna(inplace=True) as its sole missing-value strategy, which removes every row containing at least one null across all 33 columns. This reduced the dataset from approximately 268,000+ rows to 11,849 clean rows—a substantial reduction that indicates many listings had missing review scores or host profile fields. While this ensures a fully complete dataset for modeling, it introduces survivorship bias toward well-established listings with complete profiles and review histories.

### 4.2. Boolean Encoding
Four boolean columns originally stored as text (“t” / “f”) were converted to binary integers (1 / 0) for compatibility with machine learning algorithms. The affected columns are:
•	instant_bookable — whether the listing supports instant booking
•	host_is_superhost — whether the host holds Airbnb Superhost status
•	host_has_profile_pic — whether the host uploaded a profile picture
•	host_identity_verified — whether the host’s identity is verified by Airbnb
The encoding was performed using pandas .replace({'t': 1, 'f': 0}) followed by .astype(int). A commented-out cell also explored pd.get_dummies() for these columns but was not executed in the final run.

### 4.3. Column Aliasing
Each of the 33 columns was assigned to a standalone Python variable (e.g., price = df['price']). While this does not transform the data, it suggests the notebook author intended to use these variables for subsequent analysis steps such as correlation calculations, visualizations, or model feature selection

## 5. Key Observations from the Data
### 5.1. Host Profile Patterns
The sample rows in the notebook output reveal that the vast majority of hosts in the cleaned dataset have profile pictures and verified identities. Most hosts respond within an hour, and response/acceptance rates cluster near 100%. Superhost status is less common—most sample rows show non-Superhosts—which is consistent with the general Airbnb platform where Superhost is an earned distinction requiring sustained high ratings and booking volumes.

### 5.2. Geographic Distribution
The data spans all five NYC boroughs under the district column, with neighborhoods including Bushwick, Chelsea, East Village, Bayside, Long Island City, Greenwich Village, Williamsburg, and West Village visible in the sample. Manhattan and Brooklyn appear to dominate the visible rows, which aligns with known patterns of Airbnb supply concentration in New York City.

### 5.3. Property and Pricing Characteristics
The sample listings are overwhelmingly “Entire apartment” / “Entire place” types, typically accommodating 2–5 guests with 1 bedroom. Nightly prices in the sample range from $71 to $188, with many listings requiring a 30-night minimum stay (suggesting monthly rental positioning). Review scores tend to cluster at the high end of their respective scales, with overall ratings between 65 and 100 and sub-scores frequently at 9 or 10 out of 10.

### 5.4. Review Score Distribution
The review score columns (rating, accuracy, cleanliness, checkin, communication, location, value) are all present and numeric after cleaning. The overall rating uses a 0–100 scale while the six sub-dimensions use a 0–10 scale. High scores dominate the sample, which is typical of Airbnb’s review ecosystem where ratings are generally inflated toward the top of the scale.

## 6. Scope and Limitations
The notebook as submitted represents the initial data loading and preprocessing stage. Several analytical steps that would typically follow in a complete EDA pipeline were not yet performed, including:
1.	Descriptive statistics — no .describe() or summary statistics were generated for numeric columns.
2.	Visualizations — matplotlib and seaborn were imported but no plots were created in the notebook.
3.	Correlation analysis — no heatmaps or pairwise correlation matrices were computed.
4.	Feature engineering — the amenities column remains in raw JSON string format and was not parsed into usable features.
5.	Modeling — LinearRegression, SequentialFeatureSelector, and ExhaustiveFeatureSelector from mlxtend were imported but not yet used.
6.	Train/test splitting — train_test_split was imported from sklearn but no split was executed.
These imported but unused libraries strongly suggest the notebook is part of a larger pipeline that will include feature selection (both sequential and exhaustive) and linear regression modeling with price as the likely target variable.

## 7. Summary
The notebook successfully loads and cleans an Airbnb listings dataset for New York City, producing a complete 11,849-row, 33-column dataframe ready for analysis. The preprocessing converts boolean text fields to numeric format and establishes column aliases for downstream use. The imported-but-unused modeling libraries (sklearn’s LinearRegression and train_test_split, mlxtend’s sequential and exhaustive feature selectors) indicate that the next phase of this project will involve feature selection and regression modeling to predict listing prices based on host, property, location, and review attributes.
