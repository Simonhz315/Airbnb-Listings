# Airbnb-Listings

## 1. Dataset and Processing Pipeline
The dataset captures 11,849 Airbnb listings across New York City's five boroughs, spanning 33 features that cover host profiles, property characteristics, geographic location, pricing, review scores, and booking policies. The raw source contained over 268,000 rows, but after dropping all records with missing values, the working dataset was reduced to this cleaner subset.
This aggressive cleaning strategy has a real-world implication worth acknowledging: the surviving 11,849 listings are disproportionately mature, well-reviewed properties with fully completed host profiles. Newer listings, those without reviews yet, or hosts who haven't filled in every field were filtered out. In practice, this means the analysis reflects the established Airbnb market in NYC rather than the full spectrum that includes new entrants and casual hosts. This is actually a reasonable scope for price prediction — these established listings are the ones with stable, market-tested prices rather than speculative or experimental pricing.
Four boolean columns (host_is_superhost, host_has_profile_pic, host_identity_verified, instant_bookable) were converted from "t"/"f" strings to binary 0/1 integers, and remaining categorical columns were encoded using LabelEncoder. Identifier columns like listing_id and host_id were dropped since they carry no predictive signal. The top 1% of prices were also trimmed as outliers to prevent extreme luxury listings from distorting model training.

## 2. Feature Catalog
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

## 3. Capture What Actually Drives Price
Beyond the raw columns, several derived features were created to better represent the underlying factors that influence how a listing is priced in practice.
host_years measures tenure on the platform relative to January 2024. This captures something real: experienced hosts learn to optimize their listings, respond faster, accumulate reviews, and understand seasonal pricing — all of which translate into higher or more stable pricing power. amenity_count tallies the number of amenities from the raw JSON string. Rather than encoding hundreds of individual amenities, a simple count proxies the general "richness" of a listing — and in the real NYC market, the difference between a bare-bones studio and one with a washer/dryer, dishwasher, and workspace is significant. host_response_time_enc converts the categorical response time into an ordinal 1–4 scale, reflecting the real-world pattern where faster-responding hosts tend to run more professional operations and command stronger bookings. Room type and district were one-hot encoded into dummy variables, which correctly captures the fact that "Entire place" vs. "Private room" and "Manhattan" vs. "Queens" represent categorically different market segments, not points on a scale.

## 4. Model Comparison
Three regression models were trained on an 80/20 train-test split: Linear Regression as a baseline, Random Forest as a bagging ensemble, and Gradient Boosting as a sequential ensemble.

![image](https://github.com/Simonhz315/Airbnb-Listings/blob/main/R%5E2%20Score.png)
![image](https://github.com/Simonhz315/Airbnb-Listings/blob/main/Model%20Comparison%20-%20MAE%20%26%20RMSE.png)

Gradient Boosting emerged as the best model with an R² of 0.584, MAE of $39.83, and RMSE of $66.90. Random Forest followed closely (R² = 0.555, MAE = $39.69, RMSE = $69.20), while Linear Regression lagged behind (R² = 0.454, MAE = $47.20, RMSE = $76.68).
### What the Error Metrics Reveal About Pricing Complexity
The MAE and RMSE comparison bar chart makes the performance gap visually clear. Both tree-based models cut typical prediction error by roughly $7–8 compared to Linear Regression, but the more telling metric is the RMSE gap. Gradient Boosting's RMSE of $66.90 versus Random Forest's $69.20 means it handles extreme mispredictions better — since RMSE penalizes large errors quadratically, the lower number indicates fewer catastrophic misses. Interestingly, Random Forest actually edges out Gradient Boosting on MAE by $0.14, meaning their "typical" error on a regular listing is essentially identical. The difference shows up in the tails: Gradient Boosting's sequential error-correction mechanism is specifically designed to clean up the cases where earlier trees got it wrong, which matters most for the harder-to-predict premium listings.
### Why Linear Regression Fails and What That Says About the Market
Linear Regression's R² of 0.454 — capturing less than half the price variance — is not a failure of the algorithm so much as a confirmation of how Airbnb pricing actually works. The model assumes each feature contributes independently and additively to price: one more bedroom adds a fixed dollar amount regardless of whether the listing is in Manhattan or the Bronx, and a higher review score always shifts the price by the same increment. But NYC Airbnb pricing is deeply interactive. An extra bedroom in a Williamsburg loft is worth far more than an extra bedroom in a Bronx walkup. Going from 1 to 2 bedrooms matters more than going from 4 to 5. A Superhost badge in a competitive Manhattan neighborhood carries different weight than in an area with less supply. These are multiplicative, non-linear relationships that Linear Regression simply cannot capture without hand-engineered interaction terms.
The R² bar chart shows the clear performance ladder, and it mirrors a real market truth: Airbnb pricing is not a linear formula. It's a complex surface shaped by interactions between location, property size, host quality, and guest perception — exactly the kind of pattern that tree-based ensemble models are built to find.
### The 42% Unexplained Variance: What's Missing from the Data
Even the best model leaves roughly 42% of price variance unexplained. This gap is not a modeling failure — it reflects genuine real-world factors that structured listing data cannot capture. Seasonal demand swings (summer tourism, holiday weekends, New Year's Eve) can double or halve a listing's optimal price. The quality of listing photographs — which guests heavily rely on when deciding between similarly-described options — is nowhere in this dataset. The actual wording of the listing description, the neighborhood's walkability, proximity to subway stations, and whether the apartment has that intangible "Instagram-worthy" aesthetic all influence what guests are willing to pay. An R² of 0.58 with only structured features is actually a strong result and suggests the model has extracted most of the signal available in this data.

## 5. The Actual vs. Predicted Scatter
The Gradient Boosting scatter plot of actual versus predicted prices provides the most granular view of model behavior, and it reveals a pattern that directly mirrors the structure of the NYC rental market.
Below approximately $200 per night, predictions cluster tightly along the perfect-prediction diagonal. This is where the bulk of NYC Airbnb listings sit — standard apartments and private rooms in Brooklyn, Queens, and non-premium Manhattan neighborhoods. In this range, pricing is relatively "rational" and driven by observable features: bedroom count, location, and basic amenity levels explain most of the variation. The model has abundant training data in this range and the price-setting behavior of hosts is more consistent and market-driven.
Above $300, scatter increases dramatically and the model systematically underpredicts. This is the premium and luxury segment — high-end Manhattan penthouses, designer lofts in Tribeca, or unique properties like converted artist studios in SoHo. These listings are underrepresented in the training data, and their prices are driven by factors that structured data cannot quantify: architectural character, designer furniture, rooftop access, celebrity-adjacent locations, or simply the host's ability to market exclusivity. The tree-based model's leaf nodes in this price range contain fewer samples, so predictions regress toward the population average — a well-known limitation that reflects the genuine difficulty of pricing unique, premium real estate.
This pattern is a faithful mirror of how the actual NYC short-term rental market works. The budget-to-midrange segment is commoditized and price-competitive; listings in this band compete primarily on location and capacity, and prices converge around predictable norms. The premium segment is fragmented and taste-driven, where pricing power comes from differentiation rather than standardizable features.

![image](https://github.com/Simonhz315/Airbnb-Listings/blob/main/Gradient%20Boosting%20-%20Actual%20vs.%20Predicted.png)

## 6. What Actually Determines Price in NYC
The Gradient Boosting feature importance chart ranks the 15 strongest predictors and paints a clear picture of the NYC Airbnb pricing hierarchy.

![image](https://github.com/Simonhz315/Airbnb-Listings/blob/main/Top%2015%20Feature%20Importances%20-%20Gradient%20Boosting.png)

### Property Size
accommodates and bedrooms sit at the top, which reflects the most fundamental economic reality of the rental market: larger spaces that host more guests command higher nightly rates. This is true across every borough and every price tier. A 1-bedroom hosting 2 guests and a 3-bedroom hosting 6 guests are in fundamentally different market segments regardless of neighborhood. Guests search and filter by capacity first, making these the primary structural determinants of what a listing can charge.
### Location
Latitude, longitude, and the district dummy variables collectively rank among the strongest features. This captures the enormous geographic price gradient across NYC — a reality that anyone who has apartment-hunted in the city would recognize immediately. Manhattan commands a structural premium over Brooklyn, which commands a premium over Queens, which in turn commands a premium over the Bronx and Staten Island. But it's more granular than borough-level: the coordinates capture neighborhood-level variation, where a listing in the West Village prices very differently from one in Washington Heights despite both being in Manhattan. The model learned what real estate agents have always known — in New York, location isn't just important, it's the single most powerful multiplier on base price.
### Reviews Signal Quality and Justify Premiums
Review scores — particularly the overall rating and location sub-score — contribute meaningfully but rank below size and location. This ordering reflects how pricing actually works in practice. Reviews don't set a listing's price tier (that's determined by where it is and how big it is), but they differentiate within a tier. Two identical 1-bedroom apartments in the East Village will diverge on price based on reviews: the one with a 95 rating and glowing cleanliness scores can charge a $20–30 premium over the one with a 75 rating. But even a perfect-score listing in far eastern Queens won't outprice a mediocre Manhattan studio. Reviews are the tiebreaker, not the primary driver — and the model's importance rankings correctly reflect this market dynamic.
### Amenities
amenity_count shows meaningful but secondary importance. In the real market, amenities like in-unit laundry, a dedicated workspace, a dishwasher, or air conditioning do justify higher prices — but they're additive rather than transformative. A listing with 30 amenities versus one with 10 amenities might command a 10–15% premium, but amenity count alone won't move a listing from the budget tier to the premium tier. The model captures this proportional relationship accurately.
### Host Experience
host_years and host_is_superhost have the lowest but non-zero importances among the top features. This is perhaps the most interesting finding from a business perspective. The Superhost badge and years of experience don't dramatically change what a host can charge — the market sets price floors and ceilings based on property and location. What host quality more likely affects is booking frequency rather than nightly rate: Superhosts may not charge much more, but they probably get booked more consistently because the badge reduces perceived risk for guests. This suggests that if the analysis were expanded to model expected revenue (price × occupancy rate) rather than just nightly price, host attributes would likely become much more important.

## 6. Connection to the Findings
Taken together, the model results, scatter plot, and feature importances tell a coherent story about how the NYC Airbnb market is structured.
The market operates on a hierarchy: property capacity sets the floor, location multiplies it, reviews and amenities fine-tune it, and host reputation adds a marginal polish. This mirrors the broader real estate principle that value is determined first by size, then by location, then by condition and finish — short-term rentals follow the same logic as long-term leases, just compressed into nightly rates.
The model's strong performance in the sub-$200 range and weaker performance above $300 reflects a real structural divide in the market. The budget-to-mid segment is efficient and commoditized — prices converge on predictable levels because supply is abundant and guests comparison-shop actively. The premium segment is inefficient and idiosyncratic — prices vary widely because these listings compete on uniqueness, aesthetics, and experience rather than on standardizable features.
The 42% of unexplained variance is not a gap to be closed entirely by better modeling. Some of it is genuinely unpredictable: hosts set prices based on gut feel, competitive monitoring, or revenue management tools that consider real-time demand. Some of it is driven by information the dataset doesn't contain — photography quality, description copywriting, proximity to transit, seasonal events, and the constant churn of NYC's tourist and business traveler patterns. A model that explains 58% of price variance from structured listing attributes alone is capturing the systematic, market-driven component of pricing and leaving the idiosyncratic, human-judgment component appropriately unmodeled.


## 7. Future Analysis
Several extensions could deepen this analysis and connect to additional data mining techniques. Applying PCA to the six correlated review sub-scores (accuracy, cleanliness, checkin, communication, location, value) could compress them into 1–2 principal components, reducing multicollinearity while preserving the core "quality" signal. Running association rules on the raw amenities column could reveal which amenity bundles tend to co-occur and whether certain combinations (e.g., workspace + fast wifi + coffee maker) are associated with price premiums that the count alone doesn't capture. A two-stage approach — first classifying listings into "budget," "mid-range," and "premium" segments, then building separate regression models for each — would directly address the scatter plot's underprediction problem at high price points, since each sub-model would learn pricing patterns specific to its market tier. Finally, a log-transform on the price target would compress the right-skewed distribution and give the model more proportional resolution across the full price spectrum, likely improving RMSE in the premium range where the current model struggles most.
