# Amazon Lex HR Portal
Build Amazon Lex chatbot to interact with Lambda, Amazon DynamoDB, Amazon Pinpoint, Amazon Comprehend, Amazon Connect, Twilio, Slack, Facebook, Kik and Alexa devices

## Architecture
![architecture](images/readme/hrbot.png)

## Motivation to build this bot
Bot is built for those employees who does not have or does not like navigating to complicated HR website to communicate for doing basic daily tasks, and would rather do it using SMS, Call, Alexa devices as regular dialog coversation like you are asking your assitant or friend to do the task for you.

The task this bot can do are:

1. Logging daily work hours.
2. Calculate total pay for the month.
3. FAQ search like polcies, agent, salary etc
4. Most importantly, all the interactions are beging analyzed by Amazon Comprehend for sentiment analysis. This way if the user is not happy we can divert the interation immidiatly to human.

Future enhancement and implementation
1. Integration with AR VR Amazon sumerian https://docs.sumerian.amazonaws.com/tutorials/create/beginner/dialogue-component/
2. More HR functionalities like vacations calander, leave balance etc
