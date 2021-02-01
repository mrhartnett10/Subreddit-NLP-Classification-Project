## Quick Introduction:

Recently Deloitte has been attempting to undergo a bit of an employee brand revamp. In the past they have targeted candidates who would be classified as intjs (very precise, introverted, calculated) (via Myers-Briggs Type Indicator questionnaire), but now they'd like incorproate indiviudals who trend closer to to the esfp (much more social, entertainers driven). They are hoping to increase the character diversity among their staff, essentially they're looking to liven up the party! That is why Deloitte has approached me to assist in finding these candidates and classifying if they match the esfp criteria they are after.

### Problem Statement: 

By scraping and analyzing posts from both intj and espf subreddit, can I create a nlp driven classification model that can assist Deloitte in their search for recognizing and identifying their sought after esfp candidates. 

### Background:

As mentioned briefly in the quick intro, intj and espf are two personality types from the Myer-Briggs Type Indicator questionnaire. As defined by wikipedia: The Myers–Briggs Type Indicator (MBTI) is an introspective self-report questionnaire indicating differing psychological preferences in how people perceive the world and make decisions. The test attempts to assign four categories: introversion or extraversion, sensing or intuition, thinking or feeling, judging or perceiving. One letter from each category is taken to produce a four-letter test result, like "INFJ" or "ENFP"

INTJ type: INTJ (introverted, intuitive, thinking, and judging) is one of the 16 personality types identified by a personality assessment called the Myers-Briggs Type Indicator (MBTI). Sometimes referred to as the "Architect," or the "Strategist," people with INTJ personalities are highly analytical, creative and logical.According to psychologist David Keirsey, developer of the Keirsey Temperament Sorter, approximately one to four percent of the population has an INTJ personality type.
https://www.verywellmind.com/intj-introverted-intuitive-thinking-judging-2795988

ESPF: ESFPs are vivacious entertainers who charm and engage those around them. They are spontaneous, energetic, and fun-loving, and take pleasure in the things around them: food, clothes, nature, animals, and especially people. ESFPs are typically warm and talkative and have a contagious enthusiasm for life. They like to be in the middle of the action and the center of attention. They have a playful, open sense of humor, and like to draw out other people and help them have a good time.
https://www.truity.com/personality-type/ESFP#:~:text=ESFPs%20are%20vivacious%20entertainers%20who,a%20contagious%20enthusiasm%20for%20life.

The hope will be to identify enough of a difference in language, that we will be able to analyze a piece of text and determine if they are classified as either ESPF (our interested target) or intj (our 0 y-value). 

### Data Dictionary:
 
|Feature|Type|Dataset|Description|
|---|---|---|---|
|subreddit|object|Main Set|Indicates which subreddit the accompanying post belongs to|
|selftext|object|Main Set|The body of the text found within the post|
|title|object|Main Set|The title of the post written by the user|
|combotext|object|Main Set|A combo text of both the selftext and the title text|
|post_length|int|Main Set|The length of the post denoted by number of characters|
|post_word_count|int|Main Set|The length of the post denoted by the number of words|
|vader_neg|float|vader_sent|The associated negative score for each post through VADER sentiment analysis|
|vader_pos|float|vader_sent|The associated positive score for each post through VADER sentiment analysis|
|vader_neu|float|vader_neu|The associated neutral score for each post through VADER sentiment analysis|
|vader_compound|float|vader_neu|The associated compound score for each post through VADER sentiment analysis|


### Data Retrieval:

For this project I utilized the Pushshift API to scrape the two respective subreddits. Pushshift is rather intuitive but is limited in that it only allows for 100 posts per scrape. This resulted in the challenge of building a function that would loop through enough times and scrape together a large enough chunk of posts to sufficiently begin analysis. In order to do this you must also be aware of the amount of times you initiate a scrape. If you overload the website you may end up receiving a JSON error so it was vital both morally, but also functionally to implement a time.sleep command in the function. Once the function was adequately built, it was as simple as entering the appropriate subreddit and then saving the data to a csv file for later cleaning.

### Cleaning:

Cleaning process included the standard checking for nulls and missing values, although this time was slightly different since an originally empty cell, would convert to a null value when exported to csv and then read back into another notebook. This required replacing nulls with an empty string which then allowed us to combine both the selftext and the title to create a combotext column, allowing for more "data" to analyze.

The other main challenges were just finding ways to deal with replacing white spaces, urls, and other unwanted internet character that were not important to our model. My methods can be viewed throughout the notebook with appropriate descriptions. 

### EDA:

Visually I looked at distribution of post length the most, and discovered a fair amount of outliers for both subreddits (some people had a lot to say!) Then I examined the sentiment scores of each post which were determined using VADER sentiment analysis: https://github.com/cjhutto/vaderSentiment. Distributions were fairly similar with some slight differences, but not enough of a difference to determine a model from or to justify using sentiment scores as our model features. 

The last section of EDA I looked at CountVectorizer and Tfidfvectorizer. Initially through cvec, I observed that there was quite a bit of overlap in most common words with the set so I tried to utilize tvec to punish the normal words as well as created a dataframe to filter out which words appeared more heavily  across both sets. Once I was able to determine enough of a set of unique words that differed in Tfidf weights, I set those as my model features. 

### Modeling:

Modeling I explored five different models: Logistic Regression, KNN, SVM, Random Forest, and then Random Forest again on my entire tvec set (all the words as features). While the last Random Forest did fare best with the ultimate test set and achieved our best result in predicting ESPF candidates, it suffered from being severely overfit. The most balanced model was my original logistic regression model, but while it didn't experience high variance it experience high bias in tha it only beat out our baseline by roughly 11%. 

### Conclusions & Recommendations: 

While we set out to be able to utilize NPL analysis and modeling to determing prime candidates that match the ESPF characteristics to liven up the workplace, we certainly encountered trouble finding stark differences in language used across the two subreddits. In fact, I would say the two subreddits are more common then they are uncommon which would make it quite difficult to create a predictor model to accurately classify one versus the other simply based on speech. 

Despite these challenges we were still able to improve on the baseline with all models, with the logistic regression model being the most favored amongst them all. But if we are willing to take a risk and truly value sensitivity then we can give a shot with random forest model with ALL the tfidfvectorization data as opposed to the just more unique top features we were able to dwindle down to. 

While this may be a good starting point, if the company is truly interested in hiring the right "party starter" for the office environment, they will have to perform further due dilligence in junction with this preliminary screening system. 

