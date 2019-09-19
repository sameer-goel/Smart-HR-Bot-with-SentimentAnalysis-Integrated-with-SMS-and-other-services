## Sentiment Analysis of user interaction and connect to human support of negative sentiment found
Nothing makes frustated user more frustrated if he is not happy reposnding to bot. Simply connect him to Human support.

## Demo
<img src="images/usecase6/usecase6.png" alt="usecase6" width="500">

## Function
```
# Check for sentiment
    sentiment=client.detect_sentiment(Text=event['inputTranscript'],LanguageCode='en')['Sentiment']
    if sentiment=='NEGATIVE':
        return close({},
                 'Fulfilled',
                 {'contentType': 'PlainText',
                  'content': 'I am sorry. Seems like you are having troubles with our chat service. Let me transfer you to the a human support.'}) #trigger a call to human support
```
