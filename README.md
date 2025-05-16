import tweepy
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import matplotlib.pyplot as plt

# Twitter API credentials (replace with your own keys)
API_KEY = 'YOUR_API_KEY'
API_SECRET_KEY = 'YOUR_API_SECRET_KEY'
ACCESS_TOKEN = 'YOUR_ACCESS_TOKEN'
ACCESS_TOKEN_SECRET = 'YOUR_ACCESS_TOKEN_SECRET'

# Authenticate to the Twitter API
auth = tweepy.OAuth1UserHandler(API_KEY, API_SECRET_KEY, ACCESS_TOKEN, ACCESS_TOKEN_SECRET)
api = tweepy.API(auth)

# Define the search term (you can change it to any topic)
search_term = 'climate change'  # Example topic
tweet_count = 100  # Number of tweets to collect

# Collect tweets related to the search term
tweets = tweepy.Cursor(api.search, q=search_term, lang='en', tweet_mode='extended').items(tweet_count)

# Initialize the sentiment analyzer (VADER)
analyzer = SentimentIntensityAnalyzer()

# Lists to store sentiments
sentiment_labels = []

# Analyze sentiment for each tweet
for tweet in tweets:
    sentiment_score = analyzer.polarity_scores(tweet.full_text)
    sentiment = sentiment_score['compound']
    
    # Assign sentiment label based on compound score
    if sentiment >= 0.05:
        sentiment_label = 'Positive'
    elif sentiment <= -0.05:
        sentiment_label = 'Negative'
    else:
        sentiment_label = 'Neutral'
    
    sentiment_labels.append(sentiment_label)

# Count occurrences of each sentiment label
sentiment_counts = {
    'Positive': sentiment_labels.count('Positive'),
    'Negative': sentiment_labels.count('Negative'),
    'Neutral': sentiment_labels.count('Neutral')
}

# Visualize sentiment distribution using a bar chart
plt.bar(sentiment_counts.keys(), sentiment_counts.values(), color=['green', 'red', 'gray'])
plt.title(f'Sentiment Distribution for "{search_term}"')
plt.xlabel('Sentiment')
plt.ylabel('Number of Tweets')
plt.show()

# Print sentiment counts (optional)
print(f"Sentiment counts for '{search_term}':")
print(f"Positive: {sentiment_counts['Positive']}")
print(f"Negative: {sentiment_counts['Negative']}")
print(f"Neutral: {sentiment_counts['Neutral']}")
