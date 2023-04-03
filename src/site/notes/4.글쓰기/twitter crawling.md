---
{"dg-publish":true,"permalink":"/4.글쓰기/twitter crawling/","dgPassFrontmatter":true}
---


트위터에서 한달치 트윗을 크롤링하는 방법은 여러 가지가 있습니다. 하지만 트위터에서는 사용자 데이터의 개인정보 보호를 위해 API 사용을 권장합니다. 따라서, 트위터 API를 사용하는 것이 가장 적절한 방법입니다.

1.  Twitter API 사용하기

-   Twitter Developer 계정을 만들고, 애플리케이션을 생성합니다.
-   생성한 애플리케이션의 API Key와 API Secret Key, Access Token과 Access Token Secret를 받아옵니다.
-   Tweepy나 Twitter API 등의 라이브러리를 이용해 해당 API를 호출하여 데이터를 받아옵니다.
-   Tweepy 라이브러리를 사용한 코드 예시:

makefileCopy code

```
import tweepy
# Twitter API credentials
consumer_key = "your_consumer_key"
consumer_secret = "your_consumer_secret"
access_token = "your_access_token"
access_secret = "your_access_secret"

# Authentication
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_secret)

# API object
api = tweepy.API(auth)

# Search for tweets with certain keywords and within a certain time frame
tweets = tweepy.Cursor(api.search_tweets,
              q="keyword",
              since_id="2022-03-01",
              until="2022-03-31",
              lang="ko",
              tweet_mode="extended").items()

# Loop through tweets and do something with each tweet
for tweet in tweets:
    print(tweet.full_text)
```

2.  BeautifulSoup, Selenium 등을 이용해 웹 스크래핑하기

-   BeautifulSoup이나 Selenium 등의 라이브러리를 이용해 트위터 웹 페이지를 크롤링합니다.
-   트위터 웹 페이지에서 필요한 데이터를 가져옵니다.
-   트위터에서 사용자 데이터의 개인정보 보호를 위해 API 사용을 권장하고 있기 때문에, 이 방법은 비추천합니다. 또한, 웹 스크래핑은 서버 부하 문제를 일으킬 수 있습니다. 따라서, API 사용을 권장합니다.