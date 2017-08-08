# fuzz_matching
Systematic matching of Google Play and iTunes app store ids &amp; features

Task
Please come up with an approach for matching apps across platforms, using the attributes provided in datasets 1 and 2. 

My Approach:

Produce a comparative matching system that takes Dataset_1 or training-set as input and matches the gp_id with its corresponding ios_id based on fuzzy matching ratios calculated between the shared column-features, thereby, creating an output like Dataset_2 or the test set.

First, pre-process the data so that the index == "app_id" and all columns of interest are lower(), strip(), and punctuation replace(). 
Removing punctuation from publisher_name and app_name had negative effects on match capabilities, so for first procedure retain punctuation in both except for categories.

After pre-processing, split dataset_train into gp and ios dfs or gp_train and ios_train. 
These dfs will enact the procedure used to compare each value in the column field of one frame with that of the other.

Using 2 simple for loops with 3 iterations, produce a match_score (app_name match), pub_score (publisher_name match), and cat_score (categories match) for each value in the column in question of gp_train with that of ios_train. 
The 4 ratio scores – ratio, partial ratio, token sort ratio, and token set ratio – are functions from Fuzzy Wuzzy library that calculate a Levenshtein distance / metric of similarity between string sequences. 
Each ratio is calculated for each pair of app_name, publisher_name, and categories, then averaged as the match_score, pub_score, and cat_score. 

The results – gp_id, ios_id, [app_name_gp, app_name_ios, match_score], [pub_name_gp, pub_name_ios, pub_score], [cat_name_gp, cat_name_ios, cat_score] – are appended into 3 empty lists then transformed into 3 DataFrames.
Each DataFrame – match, pub, cat – contains all possible matches between the two columns, i.e. len(gp_train)*len(ios_train). 

After all of the combinations of matches based on app_name, publisher_name, & categories are computed, we then merge the 3 dfs into one based on gp_id & ios_id. 
This produces a complete matches dataframe that holds every combination of match with its corresponding scores.

From here, we compute a final score – the average of the match_score, pub_score, & cat_score, which will be used to help filter the matches. Because match_score is the most important feature, we sort_values(ascending=False) with the highest match_score > pub_score > Score > cat_score dropping duplicates on gp_id & ios_id, respectively. Create protocol to label matches via if statements then sort on is_match, drop_duplicates(), then format output to match test-set.
Store remaining unmatched ids for further procedures.
