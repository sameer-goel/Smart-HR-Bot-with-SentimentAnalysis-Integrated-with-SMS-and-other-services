# Use Case 1 - Greet the user with welcome message and HR menu options - Lambda Fulfilment
Simple use-case where you repond with a message from lambda

## Demo:
<img src="images/usecase1/usecase1.png" alt="usecase1" width="500">

## We need to do following:
1. Create Lex chat bot 
2. Create a lambda function that can fulfil Lex responses

## Reference docs:
You can open these docs for further information
Create an Amazon Lex Bot (Console) - https://docs.aws.amazon.com/lex/latest/dg/gs-bp-create-bot.html
Create a Lambda Function (Console) - https://docs.aws.amazon.com/lex/latest/dg/gs-bp-create-lambda-function.html

## Steps:

### Navigate to Lex service in AWS Console

1.	Create a new chatbot
 
<img src="images/usecase1/1.png" alt="1" width="500">

2.	Enter the bot name HR_Portal, COPPA as No and hit create 
 
 <img src="images/usecase1/2.png" width="500">

3.	Create intent
 <img src="images/usecase1/3.png" width="500">

4.	Now Create
-	Utterances – Hi, Hey, Hello
-	Slots – Name: Name, Slot Type: AMAON.US_FIRST_NAME
<img src="images/usecase1/4.png" width="1000">

Click gear icon and put some Promts like:
- Please confirm your identity
- Please confirm your username
- Please confirm your identity by entering your username

Corresponding utterances:
- my name is ​{Name}​
<img src="images/usecase1/4a.png" width="1000">

5. Save
<img src="images/usecase1/4b.png" width="1000">

### Navigate to Lambda service in new tab
6.	Create a new lambda function with 2.7 python as Runtime language and Create new role
<img src="images/usecase1/5.png" width="1000">
  
6.	Copy code from bottom of this page in Lambda editor

7.	Configure sample test event to test the code
<img src="images/usecase1/6.png" width="1000">
<img src="images/usecase1/7.png" width="1000">

## Configure test event in lambda
change the values according to your data
``` 
{
  "messageVersion": "1.0",
  "invocationSource": "DialogCodeHook",
  "userId": "tast_user",
  "sessionAttributes": {},
  "bot": {
    "name": "HR_Bot",
    "alias": "$LATEST",
    "version": "$LATEST"
  },
  "outputDialogMode": "Text",
  "currentIntent": {
    "name": "DoSomething",
    "slots": {
      "Name": "laxmi"
    },
    "confirmationStatus": "None"
  },
  "inputTranscript": "This is awesome"
}
```

Add more prompts and corresponding utterances to the slot, remember to BUILD again
 
Test your bot

Refer this page to understand code https://docs.aws.amazon.com/lex/latest/dg/lambda-input-response-format.html

##  Function involved
Refer this page to understand code https://docs.aws.amazon.com/lex/latest/dg/lambda-input-response-format.html
```
def do_this(intent_request):
    # get the value of Name slot provided by Lex interface
    name = intent_request['currentIntent']['slots']["Name"]
    source = intent_request['invocationSource']

    # return closer of the intent
    return close(intent_request['sessionAttributes'],
                 'Fulfilled',
                 {'contentType': 'PlainText',
                  'content': 'Hey {}!, I am Lex. \n\nNice to meet you! :) You can try HR portal functionalities: \n 1. Log my hours \n 2. Calculate my pay \n 3. Faq'.format(name)})
```
## Code

```
import os
import time
import logging
import boto3
import decimal
import dateutil.parser
import datetime
from boto3.dynamodb.conditions import Key, Attr

# Logger
logger = logging.getLogger()
logger.setLevel(logging.DEBUG)
# Client for Comprehend
client = boto3.client('comprehend')
"""
Helper function for Lex
"""  
def get_slots(intent_request):
    return intent_request['currentIntent']['slots']
    
#Close: Informs Amazon Lex not to expect a response from the user. Just return any final message from Lambda to Lex
def close(session_attributes, fulfillment_state, message):
    response = {
        'sessionAttributes': session_attributes,
        'dialogAction': {
            'type': 'Close',
            'fulfillmentState': fulfillment_state,
            'message': message
        }
    }
    return response
    

"""
Functions to fulfill intent
"""
def do_this(intent_request):
    # get the value of Name slot provided by Lex interface
    name = intent_request['currentIntent']['slots']["Name"]
    source = intent_request['invocationSource']
    
    # return closer of the intent
    return close(intent_request['sessionAttributes'],
                 'Fulfilled',
                 {'contentType': 'PlainText',
                  'content': 'Hey {}!, I am Lex. \n\nNice to meet you! :) You can access following HR portal functionalities: \n 1. Log my hours \n 2. Calculate my pay \n 3. FAQ'.format(name)
                 }
                 )
         
#############################################################################################################
def dispatch(intent_request):
    """
    Called when the user specifies an intent for this bot.
    """
    logger.debug('dispatch userId={}, intentName={}'.format(intent_request['userId'], intent_request['currentIntent']['name']))
    intent_name = intent_request['currentIntent']['name']

    # Dispatch to your bot's intent handlers
    if intent_name == 'DoSomething':
        return do_this(intent_request)

    raise Exception('Intent with name ' + intent_name + ' not supported')

def lambda_handler(event, context):
    """
    Route the incoming request based on intent.
    The JSON body of the request is provided in the event slot.
    """
    # By default, treat the user request as coming from the America/New_York time zone.
    os.environ['TZ'] = 'America/New_York'
    time.tzset()

    return dispatch(event)
```