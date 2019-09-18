# User authentication and validation against identity database (DynamoDB in our case) - Lambda Validation
do_this() include the validation function validate_name()

## Demo:
<img src="images/usecase2/usecase2.png" alt="usecase2" width="500">

## New functions introduced here:
- elicit_slot: Informs Amazon Lex that the user is expected to provide a slot value in the response.
- delegate: Directs Amazon Lex to choose the next course of action based on the bot configuration.
- validate_friend: Validate the friend from a list
- build_validation_result: Return response to Lex with or without defined return message
Reference : https://docs.aws.amazon.com/lex/latest/dg/lambda-input-response-format.html

## Functions
```
def do_this(intent_request):
    # get the value of Name slot provided by Lex interface
    name = intent_request['currentIntent']['slots']["Name"]
    source = intent_request['invocationSource']
    
    if source == 'DialogCodeHook':
        # Perform basic validation on the supplied input slots.
        # Use the elicitSlot dialog action to re-prompt for the first violation detected.
        slots = get_slots(intent_request)
        validation_result = validate_name(name)
        if not validation_result['isValid']:
            slots[validation_result['violatedSlot']] = None
            return elicit_slot(intent_request['sessionAttributes'],
                               intent_request['currentIntent']['name'],
                               slots,
                               validation_result['violatedSlot'],
                               validation_result['message'])
        
        output_session_attributes = intent_request['sessionAttributes'] if intent_request['sessionAttributes'] is not None else {}
        return delegate(output_session_attributes, get_slots(intent_request))
    # return closer of the intent
    return close(intent_request['sessionAttributes'],
                 'Fulfilled',
                 {'contentType': 'PlainText',
                  'content': 'Hey {}!, I am Lex. \n\nNice to meet you! :) You can try HR portal functionalities: \n 1. Log my hours \n 2. Calculate my pay \n 3. Faq'.format(name)
                 }
                 )
```
### Validation function
```
def validate_name(name):
    #friends = ['srinivas', 'laxmi']
    friends=find_name_in_ddb(name)
    if name is not None and name.lower() not in friends:
        return build_validation_result(False,
                                       'Name',
                                       'I am sorry {}. This username does not exist, can you retype your username correctly'.format(name))
    
    return build_validation_result(True, None, None)
```