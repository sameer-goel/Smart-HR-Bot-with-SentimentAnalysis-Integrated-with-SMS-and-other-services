## Agenda: Create lambda function used for Lex bot fulfilment
We need to do following:
1. Create a lambda function that can fulfil Lex responses
2. Create Lex chat bot

## Reference docs:
You can open these docs for further information
Create an Amazon Lex Bot (Console) - https://docs.aws.amazon.com/lex/latest/dg/gs-bp-create-bot.html
Create a Lambda Function (Console) - https://docs.aws.amazon.com/lex/latest/dg/gs-bp-create-lambda-function.html

## Demo:
<img src="images/usecase1/usecase1.png" alt="usecase1" width="500">
 
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
  
6.	Copy code from Lab1_Mod1.py in Lambda editor

7.	Configure sample test event to test the code

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
    "name": "DoSomething",
    "slots": {
      "Name": "Srinivas"
    },
    "confirmationStatus": "None"
  }
}
```

Add more prompts and corresponding utterances to the slot, remember to BUILD again
 
Test your bot

Ref: https://docs.aws.amazon.com/lex/latest/dg/lambda-input-response-format.html