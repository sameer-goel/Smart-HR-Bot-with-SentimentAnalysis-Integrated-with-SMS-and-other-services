# Amazon Lex HR Portal
Build Amazon Lex chatbot to interact with Lambda, Amazon DynamoDB, Amazon Pinpoint, Amazon Comprehend, Amazon Connect, Twilio, Slack, Facebook, Kik and Alexa devices.

## Architecture
![architecture](images/readme/hrbot.png)

## Motivation to build this bot
Bot is built for those employees who does not have or does not like navigating to complicated HR website to communicate for doing basic daily tasks, and would rather do it using SMS, Call, Alexa devices as regular dialog coversation like you are asking your assitant or friend to do the task for you.

The task this bot can do are:

1. Logging daily work hours.
2. Calculate total pay for the month.
3. FAQ search like polcies, agent, salary etc
4. Most importantly, all the interactions are beging analyzed by Amazon Comprehend for sentiment analysis. This way if the user is not happy we can divert the interation immidiatly to human.
You can always add more more HR functionalities like vacations calander, leave balance etc

## Cost of this serverless setup for a month use even for production scale $2 - $5
- Lex       :   $0.38 (500 text requests)
- Pinpoint  : 	$0.06 (first 100 SMS FREE then $0.00645 per request)
- Lambda    :   $0.20 (PER 1M REQUESTS, First 1M requests per month are free)
- DynamoDB  :   $1.50 (First 25 GB per month is free + $1.25/million write + $0.25/million read)
- Comprehend:   $0.0001 (Upto 10M units)
- Connect   :   $0.06 (For inbound calls in USA)
- SNS       :   $0.50 (Per 1 million Amazon SNS, first 1 million is free)

## Use cases
### 1. Greet the user with welcome message and HR menu options - Lambda Fulfilment
<img src="images/usecase1/usecase1.png" alt="usecase1" width="500">

### 2. User authentication and validation against identity database (DynamoDB in our case) - Lambda Validation
<img src="images/usecase2/usercase2.png" alt="usecase2" width="500">

### 3. Log in working hours, validate date and working hours
<img src="images/usecase3/usecase3.png" alt="usecase3" width="500">
> validate date
<img src="images/usecase3/usecase3dateval.png" alt="usecase3" width="500">
> validate hours
<img src="images/usecase3/usercase3hoursval.png" alt="usecase3" width="500">

### 4. Calculate pay in a particular month, validate if existing user
<img src="images/usecase4/usecase4.png" alt="usecase4" width="500">
> validate if existing user
<img src="images/usecase4/usecase4userval.png" alt="usecase4" width="500">

### 5. Search common FAQs related to HR services, like Policy, Salary, Agent
<img src="images/usecase5/usecase5.png" alt="usecase5" width="500">

### 6. Sentiment Analysis of user interaction and connect to human support of negative sentiment found
<img src="images/usecase6/usecase6.png" alt="usecase6" width="500">

## Integrations
### 1. Slack, FB, Kik, twilio
- Slack: https://docs.aws.amazon.com/lex/latest/dg/slack-bot-association.html
- FB: https://docs.aws.amazon.com/lex/latest/dg/fb-bot-association.html
- Kik: https://docs.aws.amazon.com/lex/latest/dg/kik-bot-association.html
- Twilio: https://docs.aws.amazon.com/lex/latest/dg/twilio-bot-association.html
<img src="images/slack/Slack.png" width="700">

### 2. Amazon Pinpoint - two way SMS
- Similar documentation https://aws.amazon.com/blogs/messaging-and-targeting/create-an-sms-chatbox-with-amazon-pinpoint-and-lex/
<img src="images/pinpoint/sms.png" width="500">

### 3. Amazon Connect - connect center
- https://docs.aws.amazon.com/connect/latest/adminguide/amazon-lex.html

### 4. Amazon Sumerian - talk to virtual host
- https://docs.sumerian.amazonaws.com/tutorials/create/beginner/dialogue-component/
<img src="images/sumerian/sumeriana.jpg" width="500">
<img src="images/sumerian/sumerian.png" width="500">

### 5. Publish as Alexa Skill - Ask Alexa
- https://docs.aws.amazon.com/lex/latest/dg/export-to-alexa.html

## Future enhancements and implementations:
1. Integration with AR VR Amazon sumerian https://docs.sumerian.amazonaws.com/tutorials/create/beginner/dialogue-component/
2. One-time passwords (OTP) to authenticate users https://aws.amazon.com/blogs/machine-learning/authenticate-users-with-one-time-passwords-in-amazon-lex-chatbots/
3. Lex bot integration with Office 365 https://aws.amazon.com/blogs/machine-learning/schedule-an-appointment-in-office-365-using-an-amazon-lex-bot/
4. Use languange transalation to make it multilingual bot https://aws.amazon.com/blogs/machine-learning/create-a-translator-chatbot-using-amazon-translate-and-amazon-lex/
5. Document search using elasticseach https://aws.amazon.com/blogs/machine-learning/build-a-document-search-bot-using-amazon-lex-and-amazon-elasticsearch-service/
6. Deploy on web https://aws.amazon.com/blogs/machine-learning/deploy-a-web-ui-for-your-chatbot/
7. Build chatbot using Excel https://aws.amazon.com/blogs/machine-learning/build-an-amazon-lex-chatbot-with-microsoft-excel/
8. Use voice authenitcation with Pindrop and Connect: Fraud Detection with Pindrop and Amazon Connect - https://aws.amazon.com/solutionspace/contact-center/amazon-connect-pindrop/
<img src="images/pinpoint/pindrop.png" width="500">
