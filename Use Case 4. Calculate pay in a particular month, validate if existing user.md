# Calculate pay in a particular month, validate if existing user
Calculate pay based on username for a particular month and year.

## Demo
<img src="images/usecase4/usecase4.png" alt="usecase4" width="500">
> validate if existing user
<img src="images/usecase4/usecase4userval.png" alt="usecase4" width="500">

## We need to do following:
1.	Create a dynamodb table called timesheet
2.	Put item in this table based on user interaction

## Functions
```
def calcMyPay(intent_request):
    work_month = get_slots(intent_request)["WorkMonth"]
    work_year = get_slots(intent_request)["WorkYear"]
    user_name = safeString(get_slots(intent_request)["Username"])
    source = intent_request['invocationSource']
    
    if source == 'DialogCodeHook':

        slots = get_slots(intent_request)
        validation_result = validate_username(user_name)
        if not validation_result['isValid']:
            slots[validation_result['violatedSlot']] = None
            return elicit_slot(intent_request['sessionAttributes'],
                               intent_request['currentIntent']['name'],
                               slots,
                               validation_result['violatedSlot'],
                               validation_result['message'])
        
        output_session_attributes = intent_request['sessionAttributes'] if intent_request['sessionAttributes'] is not None else {}
        return delegate(output_session_attributes, get_slots(intent_request))

    total_hours=scanItemFromDDB(work_year,work_month,user_name)
    total_pay=total_hours*25 #better salary logic
    
    return close(intent_request['sessionAttributes'],
                 'Fulfilled',
                 {'contentType': 'PlainText',
                  'content': '{} your total pay for {}/{} is ${}. You have worked {} hours this month'.format(user_name,work_month,work_year,total_pay,total_hours)})

```
### validate username
```
def validate_username(name):
    #friends = ['srinivas', 'laxmi']
    usernames=scanUsernameInDDB(name)
    if name is not None and name.lower() not in usernames:
        return build_validation_result(False,
                                       'Username',
                                       'I am sorry {}. You are not a registered user. Please retrype username'.format(name))
    
    return build_validation_result(True, None, None)
```
### Calculate total hours
```
def scanItemFromDDB(work_year,work_month,user_name):

    user = user_name
    month_year = str(work_year)+'-'+str(work_month)
    table = dynamodb.Table('timesheet')
    fe = Attr('date').contains(month_year) & Attr('username').eq(user)
    pe = ("#ddate, workhours")
    ean = { "#ddate": "date", }
    
    read_response = table.scan(
        FilterExpression=fe,
        ProjectionExpression=pe,
        ExpressionAttributeNames= ean
    )
    total_hours=0
    for i in read_response[u'Items']:
        total_hours += decimal.Decimal(i['workhours'])
    
    return(total_hours)
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
    "name": "CalculateMyPay",
    "slots": {
      "Username": "sameer",
      "WorkMonth": "09",
      "WorkYear": "2019"
    },
    "confirmationStatus": "None"
  },
  "inputTranscript": "This is awesome"
}
```