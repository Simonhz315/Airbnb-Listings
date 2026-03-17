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
