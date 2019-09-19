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

use this code in lambda_handler(event, context) function to monitor all the user inputs for negative interactions.

## Configure test event

You can test this along with Use case 1 fucntion do_something(), we just added inputTranscript: at the bottom to test it out manualy
```
{
  "messageVersion": "1.0",
  "invocationSource": "DialogCodeHook",
  "userId": "test_user",
  "sessionAttributes": {},
  "bot": {
    "name": "HR_Bot",
    "alias": "$LATEST",
    "version": "$LATEST"
  },
  "outputDialogMode": "Text",
  "currentIntent": {
    "name": "HRInfo",
    "slots": {
      "Question": "policy"
    },
    "confirmationStatus": "None"
  },
  "inputTranscript": "This is awesome"
}
```