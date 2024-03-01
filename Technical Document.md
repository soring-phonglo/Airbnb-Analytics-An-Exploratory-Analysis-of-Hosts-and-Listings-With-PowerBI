
# EDA with PowerBI: Airbnb Insights 

## BUSINESS OBJECTIVE

The purpose of this analysis is multi-faceted. It aims to uncover insights into district-level performance (only NYC), identify factors influencing overall ratings, visualise pricing trends, and assess the impact of various attributes on user satisfaction. Through a systematic exploration of the provided datasets, it is intended to equip stakeholders with actionable intelligence that can inform decision-making, improve host practices, and enhance the overall Airbnb experience for both hosts and guests, all accomplished using Power BI.
 
 ## DATASET DESCRIPTION
 (1) Reviews.csv:
- Contains information about reviews left for Airbnb listings.
  
(2) Listings.csv:
- Contains comprehensive details about the listed stays on Airbnb.
- Includes information such as pricing, amenities, host details, and more. 

Comprehensive descriptions of each column can 
be found in their respective data dictionary files (Listings_data_dictionary.csv
and Reviews_data_dictionary.csv)

## ANALYSIS GOALS
- District Location Scores Assessment (Only for NYC)
- Host Response Time Impact Examination
- Listing Prices Analysis
- Composite Score Analysis
- Listing Age & Host Tenure Analysis
- Property Type Price Analysis
- Monthly Visitor Trend (Pre-pandemic vs Pandemic levels)
- Comprehensive City Insights


## STEPS FOLLOWED
- Step 1:  Load data into Power BI Desktop, datasets are csv files.
- Step 2 : Open power query editor & in view tab under Data preview section, check "column distribution", "column quality" & "column profile" options.
- Step 3 : Also since by default, profile will be opened only for 1000 rows so you need to select "column profiling based on entire dataset".
### Data Cleaning & Transformation
- Step 4: 41% of data missing in 'Listings'[host_response_time]: Replaced blank cells with 'NA'
- Step 5: 86% of data missing in 'Listings'[district]. District data only available for Listings[city] as 'New York'. Replaced blank with 'NA'
- Step 6: 46% of data missing in 'Listings'[host_response_rate]
- Step 7: 40% of data missing in 'Listings'[host_acceptance_rate]

        Above missing data were not imputed as the missing data % were too big for imputation.
- Step 8: Changed data type to percentage (%) for 'Listings'[host_response_rate] & 'Listings'[host_acceptance_rate]
- Step 9: Checked for duplicacy in 'Listings'[listing_id] by creating a calculated column:
        
        DuplicateFlag = IF(COUNTROWS(FILTER('Listings', Listings[listing_id] = EARLIER(Listings[listing_id]))) > 1, "Duplicate", "Not Duplicate")
![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/bf621f53-66af-416d-869d-b73a013857cf)

### Analysis/ Reporting
#### District Location Scores (NYC only)
- Step 10: Created a clustered bar chart to show average of review scores district-wise (NYC only): X-axis- Average of 'Listings[review_scores_location], Y-axis- 'Listings[district']
- Step 11: Created an accompanying table with 'Listings'[listing_id], 'Listings'[name] & 'Listings'[review_scores_location] as columns that would provide a more granular view of location score for individual listings within selected districts
        ![Screenshot 2024-02-29 223307](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/4e0d1b31-8135-4ee2-8af4-fc5c655b1763)

#### Host Response Time Distribution
- Step 12: Created a clustered column chart to see the host response time distribution: X-axis- 'Listings'[host_response_time], Y-axis- % of grand total for count of 'Listings'[host_response_time].
        ![Screenshot 2024-02-29 230351](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/1d6aa989-1313-4b53-a083-15a5e89ac8fb)

#### Host Response Time Impact
- Step 13: Created a line chart to visualise the impact of host response time (amongst those hosts who have specified a timeframe) on the overall ratings: X-axis- 'Listings'[host_response_time], Y-axis- Average of 'Listings'[review_scores_ratings]. 
        
        Filter on the page: host_response_time is not NA
        
![Screenshot 2024-02-29 231200](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/38f68674-2137-4f41-8c79-0fd598004aec)
#### Price Across Cities
- Step 14: Inserted a map: Location- 'Listings'[city], Legend- 'Listings'[city], Bubble size- Average of 'Listings'[price].
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/bc469dcd-7425-415d-a618-73d570560893)
- Step 15: Inserted a card to display the city name with the highest average price and superimposed another card with the average price value of the city with the highest average price. To achieve this, two measures were created and stored in a seperate 'Measure' table.
Measure tables:

        MaxAvgListingPriceCity = 
        CALCULATE(
        VALUES('Listings'[city]),
        TOPN(1, ALL('Listings'[city]), CALCULATE(AVERAGE('Listings'[price])))
        )
        
        MaxAvgListingPriceCityNum = 
        CALCULATE(
        AVERAGE('Listings'[price]),
        TOPN(1, ALL('Listings'[city]), CALCULATE(AVERAGE('Listings'[price])))
        )
        
Card that shows the city with the highest average price superimposed by another card that shows the actual value:
![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/0dd4c1ac-f002-41a6-86a2-5766f6374e7e)

- Step 16: The above step was repeated to achieve the same for the city with the lowest average price, albeit with modified measures.
Measures:

        MinAvgListingPriceCity = 
        CALCULATE(
        VALUES('Listings'[city]),
        TOPN(1, ALL('Listings'[city]), CALCULATE(AVERAGE('Listings'[price]), 'Listings'[price] > 0), 1)
        )

        MinAvgListingPriceNum = 
        CALCULATE(
        AVERAGE('Listings'[price]),
        TOPN(1, ALL('Listings'[city]), CALCULATE(AVERAGE('Listings'[price]), 'Listings'[price] > 0), 1)
        )
Card that shows the city with the lowest average price superimposed by another card that shows the actual value:
![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/c3bf374c-cf4c-49f5-bc94-c56d15777c33)

It all comes together as shown below:
![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/c69fda8e-4617-41f5-a549-92a7bb153aca)
#### Price Across Cities By Room Type
- Step 17: Inserted Stacked bar chart to discover price trends across cities by room type: Y-axis- 'Listings'[city], X-axis- Average of 'Listings'[price], Legend- 'Listings'[room_type]
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/b38b9b2c-9ed3-4d00-ae84-cc24a816697b)

#### Composite Score Analysis
- Step 18: In Table view, in 'Listings' table, a calculated column was created as 'Composite score' that integrated check-in experience and host communication score. 

        Composite score = Listings[review_scores_checkin] + Listings[review_scores_communication]
![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/d864c329-de82-4c07-98eb-95077fd418c8)

- Step 19: In Report View, a clustered column chart was inserted to display composite scores for districts in NYC only (as districts for other cities were NA): X-axis- 'Listings'[district], Y-axis- Average of 'Listings'[Composite score]
Filter on the above- 'Listings'[district] is not NA
- Step 20: The above step was repeated but instead of districts, it was done for cities, with X-axis as 'Listings'[city] and everything else remaining the same
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/93c656a4-f10d-46d5-9aba-887d0ac56fe2)

#### Listing Age & Host Tenure Analysis
- Step 21: Created a calculated column- 'listing age' that would contain the age of the listings.
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/b442aa1a-4485-4de7-8a68-7b3172d31e03)
- Step 22: Inserted a card to display total number of listings with 10+ years of Tenure. Measure used for calculation given below:

        Count of Listing Age>10 = CALCULATE(COUNTROWS(Listings), Listings[listing age] > 10)
Snapshot of the card:
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/bba42259-1e7b-447a-acae-abf1927c0444)


- Step 23: Inserted a line chart to discover any trends between listing age and ratings: X-axis- 'Listings'[listing age], Y-axis- Average of 'Listings'[review_scores_rating]
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/0320febe-82aa-4200-a0cd-de2b6ee01ded)

- Step 24: Subsequently, inserted a table to display host tenure and listings age with a particular focus on hosts with 10+ years of tenure. Columns: 'Listings'[host_id], 'Listings'[name], 'Listings'[listing age]. Filter- 'Listings'[listing age] > 10
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/a6c087a6-44a4-48be-baee-0863cfec1504)

Snapshot of the whole report:
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/d0600ff3-1f83-4cc1-b29e-7c9ace7bdc9f)


#### Property Type Price Analysis
- Step 25: Inserted a tree map to help us quickly understand how prices are distributed across room type & property type, which is useful for both hosts and guests when making decisions: Category- 'Listings'[room_type], Details- 'Listings'[property_type], Values- Average of 'Listings'[price]
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/e0b435ab-98b7-4e13-8e33-25759159f332)

#### Monthly Visitor Trend (Pre-pandemic vs Pandemic levels)
- Step 26: In the Reviews dataset, created two calculated columns- 'ReviewYear2019' and 'ReviewYear2020' to help determine the count of visitors in 2019 vs 2020
Calculated Columns:  
        
        ReviewYear2019 = IF(YEAR(Reviews[date]) = 2019, "y", BLANK())

        ReviewYear2020 = IF(YEAR(Reviews[date]) = 2020, "y", BLANK())
- Step 27: Inserted a line chart to display monthly visitor trend for 2019 vs 2020: X-axis- 'Reviews'[date]-> Month, Y-axis- Count of 'Reviews'[ReviewYear2019], Secondary y-axis- Count of 'Reviews[ReviewYear2020]
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/a5b78c41-ae88-4ebe-968a-8f5f6102545a)

- Step 28: Inserted two cards to display total no of visitors for 2019 & 2020. For 2019 card- Count of 'Reviews'[ReviewYear2019], for 2020 card- Count of 'Reviews'[ReviewYear2020]
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/70fbba6a-36f1-4e34-a62f-f23d0975a05d)
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/fac9cf9d-e912-452f-bea4-62018a54022d)

- Step 29: Additionally inserted two more cards that would display the difference in visitors from 2019 to 2020 in thousands and percentage. Two dynamic measures were created to achieve this-

        VisitorsDifference = COUNTA(Reviews[ReviewYear2020]) - COUNTA(Reviews[ReviewYear2019])

        VisitorsDifference% = (COUNTA(Reviews[ReviewYear2020]) - COUNTA(Reviews[ReviewYear2019])) / COUNTA(Reviews[ReviewYear2019])
Visitor difference cards:
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/72aecc25-eee0-4d34-9f11-dd662ac77fde)

It all comes together as shown below:
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/58491f2a-8824-4ef4-a120-f2b0c31d4d55)

#### Comprehensive City Insights
Building a dashboard that presents listing prices, guest ratings, 
and visitor trends for multiple cities, with a particular focus on assessing 
changes in visitor trends in 2020 in contrast to earlier years.

- Step 30: Inserted a clustered column chart to show visitors trends across cities: X-axis- 'Listings'[city], Y-axis- Count of 'Reviews'[review_id]
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/be35cd27-6610-4f6a-bb21-ecb970cce24a)
- Step 31: Inserted a line chart to show monthly visitors trend: X-axis- 'Reviews'[date]->Month, Y-axis- Count of 'Reviews'[review_id]
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/bb9af889-29ce-4bae-900f-b218feda6c47)
- Step 32: Inserted a matrix to display key guest scores ratings and metrics city-wise: Rows- 'Listings'[city]; Values- Average of 'Listings'[review_scores_location], Average of 'Listings'[review_scores_cleanliness], Average of 'Listings'[review_scores_value], Average of 'Listings[review_scores_ratings]

        Applied conditional formatting of background color (gradient style) on Average of 'Listings[review_scores_ratings] to highlight cells
        based on the magnitude of the value of the cell

        Note: Column names have been renamed using 'Rename for this visual' option from the respective dropdown of 
        each value in the Values field for better readability
![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/70e6ef1c-c0b0-4a16-b22a-55a8d4c53a1f)

- Step 33: Inserted a clustered bar chart that displays average listing price across cities: Y-axis- 'Listings'[city], X-axis- Average of 'Listings'[price]
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/1c091c18-7b1c-4a94-b135-5bb0f2a70514)
- Step 34: Inserted a card that displays the overall total no of listings. Measure created to achieve this given below:

        TotalListings = DISTINCTCOUNT(Listings[listing_id])
![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/ab07c66a-4925-49c5-9b61-fe8ee107ce63)
- Step 34: Created two slicers to filter city and years
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/47a52586-dae7-4477-b10f-a49b550c6f45)
- Step 35: Inserted an airbnb logo by going to Insert tab > Elements > Image
        ![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/c39588cf-1b3a-47a4-95ae-cc7227d9e801)

## SNAPSHOT OF THE ENTIRE DASHBOARD
![image](https://github.com/soring-phonglo/EDA-with-Power-BI/assets/157036065/2ee46eec-185f-4a26-8ffa-2a3b862fc1d1)

