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
| `listing_id`                 | Unique identifier for each Airbnb listing                                                                        |
| `name`     | Title/description of the listing                                                            |
| `host_id`          | Unique identifier for the host                                                         |
| `host_since`         | Date the host joined Airbnb                                                                 |
| `host_location`        | Graphic location of the host                                                               |
| `host_response_time`          | How quickly the host responds(e.g., within an hour)                                                                     |
| `host_response_rate`     | Proportion of inquiries the host responds to                                                 |
| `host_acceptance_rate`   | Proportion of booking requests accepted                      |
| `swipe_right_label`      | The preference that the samples swipe right                                                      |
| `likes_received`         | The likes that the samples received                                                              |
| `mutual_matches`         | The connection where both users express interest in each other                                   |
| `profile_pics_count`     | The pictures that the samples add to their profile                                               |
| `bio_length`             | The word count that the samples have on their profile bio length                                 |
| `message_sent_count`     | The messages that the samples sent on the dating app                                             |
| `emoji_usage_rate`       | The rate of emojis used by the samples during the conversation with their match                  |
| `last_active_hour`       | The timestamp indicating when the user was last online                                           |
| `swipe_time_of_day`      | The time when the sample usually swipes on the dating app                                        |
| `match_outcome`          | The outcome of the samples using the dating app to find a partner                                |

