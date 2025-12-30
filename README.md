# SeattleStay AI: Topic Modeling, Sentiment Analysis, and Recommendation System from Seattle Airbnb Reviews

SeattleStay AI is a text analytics project that reads Seattle Airbnb guest reviews at scale and turns them into clear themes about what guests talk about most. I used topic modeling to find recurring experience themes, added sentiment scoring to measure tone, and then built a recommendation approach that suggests similar listings based on what guests describe in their reviews. 

---

## 1. Objective

Airbnb hosts and platform teams get thousands of reviews that are hard to summarize manually. The goal here was to automatically extract the main themes across reviews, connect those themes with sentiment, and use them to support discovery and comparison of listings. 

---

## 2. Data used

https://insideairbnb.com/get-the-data/

This project uses two Seattle Airbnb datasets:

1. Reviews dataset
   Around **541,092** reviews with the key text field as guest comments. 

2. Listings dataset
   About **6,862** listings with property attributes like bedrooms and bathrooms. 

Key fields used:

* `listing_id` to group reviews per listing 
* `comments` as the raw review text 
* listing attributes like `bedrooms` and `bathrooms` for context after aggregation 

---

## 3. Data cleaning and preprocessing

### 3.1 Missing and low value text

* Removed **87** reviews that had missing comments. 
* Filtered very short reviews under 20 characters so the models learn from meaningful text, leaving **521,447** reviews. 

### 3.2 Review length outliers

* Computed word counts per review and used an IQR approach to flag very short and very long reviews as outliers, around **27,500** reviews. 

### 3.3 Text normalization

Steps applied to the review text:

1. Lowercasing
2. Removing punctuation and numbers
3. Tokenization and stopword removal using NLTK
4. Adding custom stopwords such as “Seattle”, “stay”, and “great”
5. Lemmatization to reduce words to base form 

---

## 4. Exploratory analysis

Before modeling, I checked what guests talk about most using word frequency. The most common words included **clean, comfortable, easy, quiet, close,** and **beautiful**, which already suggests that cleanliness and convenience are central to guest satisfaction. 

---

## 5. Feature preparation for topic modeling

### 5.1 Aggregation to the listing level

Instead of modeling each review independently, I grouped all reviews by `listing_id` and created one aggregated review text per listing. This reduces noise and captures the overall guest perception of a property. 

After aggregation, I merged in bedrooms and bathrooms because property structure changes guest expectations and helps interpret themes for different listing types. 

### 5.2 Vectorization

I created two text representations:

1. TF IDF for term importance
2. Bag of Words for term frequency

After removing very rare and overly common terms, I retained roughly **22k to 25k** unique terms. 

---

## 6. Topic modeling experiments and results

I built and compared four topic modeling approaches: LSA, LDA, NMF, and BERTopic. 

### 6.1 LSA: Latent Semantic Analysis

I used LSA to capture broad latent concepts and found an optimal set of **5 topics**:

1. Cleanliness and comfort
2. Family stays and neighborhood value
3. Scenic city views
4. Beachside and Alki area stays
5. Responsive and friendly hosts 

Interpretation: guests consistently highlight cleanliness, calm neighborhoods, and proximity to landmarks such as Alki Beach and the Space Needle. 

### 6.2 LDA: Latent Dirichlet Allocation

For LDA, I selected **k = 5** based on the lowest perplexity. 

Main themes included:

* Downtown attractions
* Walkable and convenient stays
* Cozy homes with friendly hosts
* Quiet local charm and beachside stays
* Family and group accommodations 

### 6.3 NMF: Non Negative Matrix Factorization

NMF produced clearer separation between themes, including:

* Comfortable and clean spaces
* Rooftop and scenic views
* Apartments near restaurants and markets
* University district rentals
* Family and group stays 

Interpretation: NMF reinforced that convenience plus comfort drives review content, with some distinct lifestyle signals like rooftop access and lake proximity. 

### 6.4 BERTopic: context aware themes for recommendations

BERTopic produced the most readable and specific themes, and after reduction the model kept **15 distinct topics**. 

Examples of BERTopic themes:

* Cleanliness and host responsiveness
* Easy stay and basic convenience
* Family friendly stay
* Rooftop view deck experience
* Pet friendly stay
* Quiet stay with parking convenience
* Ballard neighborhood experience
* Cozy cottage experience 

This model was chosen as the main model because it produced the most coherent themes and it grouped listings by experience in a way that directly supports recommendations. 

---

## 7. Sentiment analysis

I used VADER sentiment scoring on each aggregated listing review and assigned compound sentiment scores.

Results:

* 97 percent positive
* 1.3 percent neutral
* 1 percent negative
* Average compound score of 0.94 

I also compared sentiment by BERTopic theme. Some themes were almost always strongly positive, such as Ballard neighborhood experience, pet friendly stay, and cozy cottage experience, while cleanliness and host responsiveness had a noticeably lower average sentiment. 

---

## 8. Recommendation system

The recommendation logic combines BERTopic topic probabilities with sentiment features into a single vector per listing and uses cosine similarity to find listings with similar guest experience profiles. 

I implemented three recommendation styles:

### 8.1 Listing to listing similarity

A user selects a listing ID, and the system returns the most similar listings based on cosine similarity between topic vectors. 

### 8.2 Search by experience

A user types a sentence like “quiet, safe neighborhood near metro, very clean place”, and the system converts that text into a topic preference vector and returns listings with the closest match. 

### 8.3 Positive filtered similar listings

This is the same as listing similarity, but it filters results using a sentiment threshold where compound score is greater than 0.2, so recommendations avoid listings with neutral or negative tone. 

---

## 9. What the results mean

Across models, the same core drivers showed up again and again:

1. Cleanliness and comfort matter most, and they dominate review language across methods. 
2. Convenience themes like walkability, nearby attractions, and calm neighborhoods are consistently tied to positive stays. 
3. Some “experience types” are very specific and are captured best by BERTopic, like pet friendly stays, cozy cottage stays, and neighborhood specific experiences. 
4. Sentiment is overwhelmingly positive in the dataset, but themes like cleanliness and host responsiveness still show relatively lower sentiment, which is useful for quality improvement focus. 

---

## 👤 Author

**Aditya Pramod Shetty**  
Master's in Business Analytics |
Isenberg School of Management | University of Massachusetts Amherst

📧 Email: adityashetty1205@gmail.com  
🔗 Linkedin: https://www.linkedin.com/in/aditya-shetty1205/

---

## Colab File Link:

https://colab.research.google.com/drive/1oD3rCKkhD3UgTKI2ltGcckFII4TcKvJ0?usp=sharing

---

## Tags

Seattle Airbnb, Airbnb reviews, topic modeling, LSA, LDA, NMF, BERTopic, sentiment analysis, VADER, cosine similarity, TF IDF, Bag of Words, text preprocessing, tokenization, lemmatization, recommendation system, Python, NLTK, scikit learn, natural language processing
