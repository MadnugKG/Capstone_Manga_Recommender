# Capstone - Manga Recommender

### Content
- [Background](#Background)
- [Problem Statement](#Problem-Statement)
- [What is Manga?](#What-is-Manga?)
- [Data Dictionary](#Data-Dictionary)
- [EDA](#EDA)
- [Recommender System](#Recommender-System)
- [Business Recommendations](#Business-Recommendations)
- [Potential Improvements](#Potential-Improvements)
- [Conclusion](#Conclusion)


### Background
You are a data scientist working in a consultancy firm who has been engaged by a client who specialises in selling manga. In order to establish their presence online, they plan to engage your consultancy firm to help setup an online store. However, the owner is skeptical about the effectiveness of a recommender system and is unwilling to include it as part of the package. 

### Problem Statement
As instructed by your manager, you are tasked to create a manga recommender to showcase to the client that a recommender system can improve its sales online and should be included into the package. To complete your task, you will need to do the following:-
- Scrape and clean data from websites
- Create a managa recommender system using the scraped data
- Determine how well it is able to recommend at least 1 manga that the reader has read before
- Calculate the potential increase in revenue from the recommender system

### What is Manga?
Manga is Japan originated comic and its name comes from the Kranji “漫画”. Manga is usually coloured in black and white due to time constraints, artistic reasons (as coloring could lessen the impact of the artwork) and to keep printing costs low ([*source*](https://en.wikipedia.org/wiki/Manga)).


### Webscrape Data Source
The datasets were webscraped from myanimelist.net


### Libraries Used
- Standard libraries installed by Anaconda
- LightFM


### Folders and Contents
|Folder Name|Contents|
|:---:|:---|
|assets|Assets used in the README|
|code|Contain jupyter notebook to:-  <br> - data_scrape  <br> - data_consolidation  <br> - data_clean  <br> - eda  <br> - recommender_system_bsm_cfsm  <br> - recomender_system_LightFM  <br> - production_model|
|data|Scraped data from myanimelist.net.  <br> Contains manga titles, manga details and user scores  <br> This has been stored in the zip file.|
|data_cleaned|Data that were cleaned for use in EDA and modeling|
|data_production|csv files to the rankings, similarity matrix and LightFM models|
|data_titles_based_cf_user_scores|Calculated scores from collaborative filtering for each title and member|


### Data Dictionary
Below are features used for the modeling

|Predictors|Data Type|Description|
|:---:|:---:|:---:|
|index|int|Unique index number for the title|
|manga_title|object|Name of the manga|
|manga_link|object|Link to the myanimelist.net website for each title|
|member|object|Name of the member|
|score|float|Rating given by the user for the manga|
|genres|float|Indicate if genre applys to the manga title|
|themes|float|Indicate if theme applys to the manga title|
|demographic|float|Indicate if demographic applys to the manga title|


### EDA
#### How are the scores distributed?
The overall score is top heavy as the distribution peaks around 8 to 10 (out of 10).
When the scores are split by the reading status of the member, the distribution "On-Hold" and 'Dropped" shifted towards the left. This makes sense as member usually drop a title because it is bad or does not suit their taste.
<br>
<img src="assets\score_distribution.png">
<br>
<img src="assets\score_distribution_by_status.png">


#### What are the most popular genres, themes and targeted demographic?
The top 5 most popular genres are:-
- Romanace
- Comedy
- Drama
- Action
- Fantasy

<br>

The top 5 most popular themes are (excluding "Not Specified"):-
- School
- Psychological
- Historical
- Harem
- Martial Arts

<br>

The top targeted demographic is shoujo.

Below are the meaning of words used in the demographic classification.

|Term|Meaning|
|:---:|:---:|
|Shoujo|Adolescent girls and young adult women ([*source*](https://en.wikipedia.org/wiki/Sh%C5%8Djo_manga))|
|Seinen|Young adult men ([*source*](https://en.wikipedia.org/wiki/Seinen_manga))|
|Shounen|Young teen male ([*source*](https://en.wikipedia.org/wiki/Seinen_manga))|
|Josei|Adult women ([*source*](https://en.wikipedia.org/wiki/Josei_manga))|
|Kids|Young children|

<img src="assets\breakdown_of_genres_themes_and_demographic.png">

<br>

#### What are the best rated genres?
Award winning has the highest score which made sense as those titles won awards before so it should be of high quality. The next 4 top genres are sports, suspense, adventure and drama.
<img src="assets\average_score_n_submission_genre.png">


#### What are the best rated themes?
The top 5 rated themes are romanatic subtext, visual arts, racing, gore and iyashikei (means "healing type" or "healing" in Japanese ([*source*](https://en.wikipedia.org/wiki/Iyashikei))). However, despite the higher ratings, romanatic subtext, visual arts and racing themes have relatively lower members reading those mangas.
<img src="assets\average_score_n_submission_theme.png">


#### What are the best rated demographic?
The top 2 rated demographic are "Kids" and "Shounen". However, the number of members who read mangas targeted at kids are very low and might be skewed by outliers or other factors such as young kids rating the manga high despite the manga might not be rated highly by other more mature members.
<img src="assets\average_score_n_submission_demographic.png">


#### What are the top 25 and bottom 25 manga?
Top 3 mangas are 'Berserk', 'JoJo no Kimyou na Bouken Part 7: Steel Ball Run', 'One Piece' while the bottom 3 mangas are 'Oukoku Game', 'Bradherley no Basha', 'Bungaku Shojo'. For the remaining titles, please refer to the charts below.
<img src="assets\top_25_rated_manga.png">
<img src="assets\bottom_25_rated_manga.png">


### Recommender System
For the recommender system, 3 types were built:-

- Basic similarity matrix (Using genres, themes, demographic as features)
- Manga based collaborative filtering
- LightFM

#### Basic Similarity Metrics (BSM)
The basic similarity metrics works by using the selected features (genres, themes, demographic) for each manga title and create a similarity matrix (manga title vs manga title matrix) by calculating a score to pair each manga to each other. However, since this model only recommend titles based on 1 title, an addition step is included so that it would sum the similarity scores for the recommended titles and recommend the top 10 titles to the member.

<br>

<img src="assets\basic_similiarity_recommender_chart.png">

<br>

The score for BSM is as shown:-

<br>

|Model|Train Accuracy|Test Accuracy|Train Precision@k|Test Precision@k|
|:---:|:---:|:---:|:---:|:---:|
|Null Model|0.015584|0.082798|-|-|
|Basic Similarity Metrics|0.82350|0.319338|0.13937|0.041654|

<br>

|Train|Test|
|:---:|:---:|
|<img src="assets\train_bsm_accuracy.PNG">|<img src="assets\test_bsm_accuracy.PNG">|

<br>

When looking at the ratio of the of misclassification by genres and themes, romance and school have higher % percentage when compared to the ratio for the sucessful classification for the train dataset.

|Genres|Themes|
|:---:|:---:|
|<img src="assets\bsm_genres_potential_culprit.PNG">|<img src="assets\bsm_themes_potential_culprit.PNG">|


#### Manga Based Collaborative Filtering Similarity Matrix (CFSM)
The manga based collaborative filtering similarity matrix works by finding similar mangas. For example, if manga A is also read by most members who read manga B, then if a new reader reads manga B, the system would recommend manga A to the new reader.

The score for CFSM compared to BSM is as shown:-

<br>

|Model|Train Accuracy|Test Accuracy|Train Precision@k|Test Precision@k|
|:---:|:---:|:---:|:---:|:---:|
|Null Model|0.015584|0.082798|-|-|
|Basic Similarity Metrics|0.82350|0.319338|0.13937|0.041654|
|Collaborative Filtering Similarity Matrix|0.51970|0.766412|0.16218|0.171908|

<br>

|Train|Test|
|:---:|:---:|
|<img src="assets\train_cfsm_accuracy.PNG">|<img src="assets\test_cfsm_accuracy.PNG">|

<br>

|Genres|
|:---:|
|<img src="assets\cfsm_genres_potential_culprit.PNG">|

<br>

|Themes|
|:---:|
|<img src="assets\cfsm_themes_potential_culprit.PNG">|

When looking at the ratio of the of misclassification by genres and themes for the CFSM, adventure, action, horror, gore, mythology and military have higher % percentage when compared to the ratio for the sucessful classification for the train dataset.


#### LightFM
When using lighFM to create a recommender system for the manga recommender, despite adding multiple features such as genres, themes, demographic, the evaluation results on the test data set were not good as they showed significantly worse result as compared to the BSM and CF-SM. As a result, it was decided that the LightFM model would not be added into the final production model.

| |train_precision@k|test_precision@k|train_recall@k|test_recall@k|train_auc|test_auc|train_accuracy|test_accuracy|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Basic Model (Warp)|0.132022|0.182901|0.700657|0.110010|0.988722|-|0.2666|0.776845|
|With Genres as Features|0.102631|0.134173|0.579957|0.080387|0.982738|0.859975|0.1441|0.399746|
|With Themes as Features|0.117668|0.149033|0.638509|0.088886|0.984754|-|0.1855|0.528753|
|With Demographic as Features|0.108577|0.157405|0.581739|0.094109|0.980135|0.876193|0.1535|0.434097|
|With Genres, Themes & Demographic as Features|0.103533|0.125674|0.592820|0.075226|0.982237|-|0.1366|0.380407|


#### Final Production Model
As the BSM performs better than CFSM when the member read less than 10 titles while the CFSM performs better and more stable when member reads more than 10 titles, hence, the final production model will use the BSM when member reads less than 10 titles and production model will switch over to CFSM when the member reads more than 10 titles. This will allow the recommender system to gain the best of both models.

<br>

| |Train|Test|
|:---:|:---:|:---:|
|Normal|<img src="assets\train_accuracy_bsm_cfsm.png">|<img src="assets\test_accuracy_bsm_cfsm.png">|
|Zoomed|<img src="assets\train_accuracy_bsm_cfsm_zoomed.png">|<img src="assets\test_accuracy_bsm_cfsm_zoomed.png">|

<br>

<img src="assets\production_model.PNG">


### Business Recommendations
- Assumption: 10% of the reader eventually purchased the recommended book
- Customer Per Month: 900,000
- Price Per Book: &#36;10.00
- Model Recommendation Accuracy: 80%
- Total revenue per month with recommender system: 	&#36;720,000
- Total revenue per month without recommender system: 	&#36;72,000
- 10 times increase in revenues


### Potential Improvements
- Look into other recommender system
- Further optimizing the LightFM model by changing default parameters
- Combining different other types of recommender system
- Using neural net to decide the weightage of each model
- Input user preference when recommending
- Having more titles in the dataset


### Conclusion
- Hybrid recommender is necessary as no one model can fit all cases
    - Basic similarity works well when the number of titles read is low
    - Collaborative filtering works better when the number of titles read by readers are high
- Having an recommender system greatly helps increase sales as it brings user to items that they would be interested in purchasing