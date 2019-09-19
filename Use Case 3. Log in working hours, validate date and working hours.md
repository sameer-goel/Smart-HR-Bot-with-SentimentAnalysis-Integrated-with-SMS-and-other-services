# Log in working hours, validate date and working hours
Log work hours and validate for invalid date or number of hours

## Demo
<img src="images/usecase3/usecase3.png" alt="usecase3" width="500">
> validate date
<img src="images/usecase3/usecase3dateval.png" alt="usecase3" width="500">
> validate hours
<img src="images/usecase3/usercase3hoursval.png" alt="usecase3" width="500">

## We need to do following:
1.	Create a dynamodb table called timesheet
2.	Put item in this table based on user interaction

## Functions introduced:
logMyHours()
validate_logMyHours()
putItemInDDB()

## Steps
1. Create a dynamodb table
<img src="images/usecase3/1.png">

2. Create new intent log my hours, see sample uttrances and slot types from image below
<img src="images/usecase3/2.png">

## Functions
```
def logMyHours(intent_request):
    work_hours = safeDecimal(get_slots(intent_request)["WorkHours"])
    work_date = get_slots(intent_request)["WorkDate"]
    user_name = safeString(get_slots(intent_request)["Username"])
    source = intent_request['invocationSource']
    logger.debug('user_name is {}'.format(user_name))
    user_name=safeString(user_name)
    if source == 'DialogCodeHook':
        slots = get_slots(intent_request)
        validation_result = validate_logMyHours(user_name,work_date, work_hours)
        
        if not validation_result['isValid']:
            slots[validation_result['violatedSlot']] = None
            return elicit_slot(intent_request['sessionAttributes'],
                               intent_request['currentIntent']['name'],
                               slots,
                               validation_result['violatedSlot'],
                               validation_result['message'])
        
        output_session_attributes = intent_request['sessionAttributes'] if intent_request['sessionAttributes'] is not None else {}
        return delegate(output_session_attributes, get_slots(intent_request))                   

    putItemInDDB(work_hours,work_date,user_name)
    
    return close(intent_request['sessionAttributes'],
                 'Fulfilled',
                 {'contentType': 'PlainText',
                  'content': ' Thank you {}!. Your {} hours for {} have been logged'.format(user_name, work_hours, work_date)})
```
### validate function
```
def validate_logMyHours(user_name,work_date, work_hours):
    
    if work_hours is not None:
        if work_hours < 0 or work_hours > 8:
            return build_validation_result(False, 'WorkHours', 'You can log upto 8 hours per shift, please reenter work hours for this day')
    
    date=work_date  
    if date is not None:
        if not isvalid_date(date):
            return build_validation_result(False, 'WorkDate', 'I did not understand that, please retype date')
        elif datetime.datetime.strptime(date, '%Y-%m-%d').date() > datetime.date.today():
            return build_validation_result(False, 'WorkDate', 'You can not log hours for future date, please retype date')

    return build_validation_result(True, None, None)
```

### Put item in DDB

```
def putItemInDDB(work_hours,work_date,user_name):
    w_hours = work_hours
    w_date = work_date
    user = user_name.lower()
    
    table = dynamodb.Table('timesheet')
    put_response = table.put_item(
       Item={
            'date': w_date,
            'username': user,
            'workhours': w_hours
        }
    )
    return (put_response ["ResponseMetadata"]["HTTPStatusCode"])
```

 ## Configure test event
 change the values according to your data
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
    "name": "LogMyHours",
    "slots": {
      "Username": "Laxmi",
      "WorkDate": "2019-10-09",
      "WorkHours": 7
    },
    "confirmationStatus": "None"
  },
  "inputTranscript": "This is awesome"
}
 ```

 You should test dynmoDB function in jupyter notebook first and then test lambda using events and then actual bot

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
# DynamoDb table decaration
dynamodb = boto3.resource('dynamodb', region_name='us-east-1')
# Client for Comprehend
client = boto3.client('comprehend')
"""
Helper function for Lex
"""  
def get_slots(intent_request):
    return intent_request['currentIntent']['slots']

def safeString(n):
    """
    Safely convert n value to string.
    """
    if n is not None:
        return str(n)
    return (n)
    
def safeDecimal(n):
    """
    Safely convert n value to int.
    """
    if n is not None:
        return decimal.Decimal(n)
    return (n)

def isvalid_date(date):
    try:
        dateutil.parser.parse(date)
        return True
    except ValueError:
        return False
    
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
    
# ElicitIntent: Informs Amazon Lex that the user is expected to respond with an utterance that includes an intent. 
def elicit_slot(session_attributes, intent_name, slots, slot_to_elicit, message):
    return {
        'sessionAttributes': session_attributes,
        'dialogAction': {
            'type': 'ElicitSlot',
            'intentName': intent_name,
            'slots': slots,
            'slotToElicit': slot_to_elicit,
            'message': message
        }
    }

#Delegate: Directs Amazon Lex to choose the next course of action based on the bot configuration.
def delegate(session_attributes, slots):
    return {
        'sessionAttributes': session_attributes,
        'dialogAction': {
            'type': 'Delegate',
            'slots': slots
        }
    }

# return response to Lex with/without defined return message
def build_validation_result(is_valid, violated_slot, message_content):
    if message_content is None:
        return {
            "isValid": is_valid,
            "violatedSlot": violated_slot,
        }

    return {
        'isValid': is_valid,
        'violatedSlot': violated_slot,
        'message': {'contentType': 'PlainText', 'content': message_content}
    }
    
"""
Functions to validate the user input
"""
def validate_logMyHours(user_name,work_date, work_hours):
    
    if work_hours is not None:
        if work_hours < 0 or work_hours > 8:
            return build_validation_result(False, 'WorkHours', 'You can log upto 8 hours per shift, please reenter work hours for this day')
    
    date=work_date  
    if date is not None:
        if not isvalid_date(date):
            return build_validation_result(False, 'WorkDate', 'I did not understand that, please retype date')
        elif datetime.datetime.strptime(date, '%Y-%m-%d').date() > datetime.date.today():
            return build_validation_result(False, 'WorkDate', 'You can not log hours for future date, please retype date')

    return build_validation_result(True, None, None)
        
"""
Dynamodb Functions read or write values
"""
def putItemInDDB(work_hours,work_date,user_name):
    w_hours = work_hours
    w_date = work_date
    user = user_name.lower()
    
    table = dynamodb.Table('timesheet')
    put_response = table.put_item(
       Item={
            'date': w_date,
            'username': user,
            'workhours': w_hours
        }
    )
    return (put_response ["ResponseMetadata"]["HTTPStatusCode"])

"""
Functions to fulfill intent
"""
def logMyHours(intent_request):
    work_hours = safeDecimal(get_slots(intent_request)["WorkHours"])
    work_date = get_slots(intent_request)["WorkDate"]
    user_name = safeString(get_slots(intent_request)["Username"])
    source = intent_request['invocationSource']
    logger.debug('user_name is {}'.format(user_name))
    user_name=safeString(user_name)
    if source == 'DialogCodeHook':
        slots = get_slots(intent_request)
        validation_result = validate_logMyHours(user_name,work_date, work_hours)
        
        if not validation_result['isValid']:
            slots[validation_result['violatedSlot']] = None
            return elicit_slot(intent_request['sessionAttributes'],
                               intent_request['currentIntent']['name'],
                               slots,
                               validation_result['violatedSlot'],
                               validation_result['message'])
        
        output_session_attributes = intent_request['sessionAttributes'] if intent_request['sessionAttributes'] is not None else {}
        return delegate(output_session_attributes, get_slots(intent_request))                   

    putItemInDDB(work_hours,work_date,user_name)
    
    return close(intent_request['sessionAttributes'],
                 'Fulfilled',
                 {'contentType': 'PlainText',
                  'content': ' Thank you {}!. Your {} hours for {} have been logged'.format(user_name, work_hours, work_date)})
         
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
    elif intent_name == 'LogMyHours':
        return logMyHours(intent_request)
    elif intent_name == 'CalculateMyPay':
        return calcMyPay(intent_request)
    elif intent_name == 'HRInfo':
        return hrInfo(intent_request)

    raise Exception('Intent with name ' + intent_name + ' not supported')

def lambda_handler(event, context):
    """
    Route the incoming request based on intent.
    The JSON body of the request is provided in the event slot.
    """
    # By default, treat the user request as coming from the America/New_York time zone.
    os.environ['TZ'] = 'America/New_York'
    time.tzset()

    # Check for sentiment
    sentiment=client.detect_sentiment(Text=event['inputTranscript'],LanguageCode='en')['Sentiment']
    if sentiment=='NEGATIVE':
        return close({},
                 'Fulfilled',
                 {'contentType': 'PlainText',
                  'content': 'I am sorry. Seems like you are having troubles with our chat service. Let me transfer you to the a human support.'}) #trigger a call to human support
    return dispatch(event)
```