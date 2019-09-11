**Agenda:** Create lambda function used for Lex bot validation and
fulfilment

**We need to do following:**

Create a lambda function that can fulfil Lex responses

**Steps:**

1.  Create a new lambda function with 2.7 python as Runtime language and
    Create new role

![](media/image1.png){width="7.5in" height="3.6645833333333333in"}

2.  Create intent

![](media/image2.png){width="4.375in" height="1.9583333333333333in"}

3.  Create

<!-- -->

1.  Utterances

2.  Slots

3.  Fulfillment Lambda

![](media/image3.png){width="7.5in" height="4.5in"}

4.  Edit Lambda code from lab1.py

5.  Configure sample test event

![](media/image4.png){width="3.4166666666666665in"
height="0.4861111111111111in"}

![](media/image5.png){width="5.666666666666667in"
height="5.694444444444445in"}

{

\"messageVersion\": \"1.0\",

\"invocationSource\": \"DialogCodeHook\",

\"userId\": \"test\_user\",

\"sessionAttributes\": {},

\"bot\": {

\"name\": \"HR\_Bot\",

\"alias\": \"\$LATEST\",

\"version\": \"\$LATEST\"

},

\"outputDialogMode\": \"Text\",

\"currentIntent\": {

\"name\": \"DoSomething\",

\"slots\": {

\"Name\": \"Srinivas\"

},

\"confirmationStatus\": \"None\"

}

}

Add more prompts and corresponding utterances to the slot, remember to
BUILD again

![](media/image6.png){width="4.180555555555555in"
height="4.138888888888889in"}

Test your bot and ask me questions

**Ref:**
https://docs.aws.amazon.com/lex/latest/dg/lambda-input-response-format.html
