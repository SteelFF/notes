## Training phrases

- Training phrases must contain phrases and sentences that end users are likely to use in the conversation.
- Training phrases must be complete sentences.
- You must add a minimum of 10 training phrases to an intent to pass validation.
    - Duplicate training phrases are not counted towards this minimum limit.

## Important words

- An important word and its synonyms can be part of only one intent. They cannot be used as important words or synonyms in other intents.
- Each important word must be part of at least 2 training phrases in an intent. Example: For the Manage_booking intent, the important word 'manage' can be part of the training phrases 'I need to manage my booking' and 'Help me manage my booking'.
    - For better accuracy in identifying intents, add important words to more than two training phrases.
- You do not need to define both singular and plural forms of important words. Add only one or the other. Example: Add either 'offer' or 'offers'.

## Intents

- All intents in the chatbot must be configured in the same way. If one intent contains only important words and another intent contains both important words and phrases, validation fails.
- You can add intents only to standard dialogs. You cannot add intents to the Default, Authentication, and Session Expire dialogs.
- You cannot delete or rename the Default intent, which is assigned to the Default dialog.
- You can add training phrases and important words to the Default intent.
- You cannot unlink the Default intent from the Default dialog.

## Avoid similar intents

Make sure that you do not have multiple intents that have the same purpose.

Example: There are 3 car brands - CarA, Car B, and Car C. The intent for each brand is as follows:

|Intent|Training Phrase|Entity|
|---|---|---|
|Buy_CarA|- Which CarA models do you have in stock?<br>- Do you have a leasing option for CarA?|CarA|
|Buy_CarB|- Which CarB models do you have in stock?<br>- Do you have a leasing option for CarB?|CarB|
|Buy_CarC|- Which CarC models do you have in stock?<br>- Do you have a leasing option for CarC?|CarC|

In this example, the purpose of all the intents is the same - buying a specific brand of a car.

The training phrases for these intents are also the same, except for the brand name. If such overlap or similarity occurs for all the training phrases of the intents, the chatbot may not be able to manage the situation correctly. Example: If the end user message does not contain the car model, the chatbot randomly recognizes any of the intents - Buy_CarA, Buy_CarB, or Buy_CarC.

To avoid this issue, you can create an entity that contains each car brand and [branch the dialog based on the entity](https://www.infobip.com/docs/answers/ai-chatbots/intents#use-entities-to-branch-a-dialog-how-to).

## Avoid similar or identical training phrases in discrete intents

If your intents have different purposes, make sure that you do not have similar or identical training phrases in these intents.

In the following example, the two intents, Model and Product, have different purposes. But the training phrases are similar. So, the chatbot may not be able to identify the correct intent for the end user's message.

|Intent|Training Phrase|
|---|---|
|Model|What model is the car?|
|Product|What is the model of the car?|

## Remove conflicting intents

If there are conflicts in the training dataset for intents, the chatbot may not be able to identify the correct intent for the end user's message. 

Conflicts between intents can occur because of the following reasons:

- The intents have the same training phrase.  
    Example:
    
    |Intent|Training Phrase|
    |---|---|
    |Buy_CarA|Do you have a leasing option?|
    |Buy_CarB|Do you have a leasing option?|
    
- The intents have training phrases that are similar in meaning and sentence construction.
    
    Example:
    
    |Intent|Training Phrase|
    |---|---|
    |Buy_CarA|What leasing options do you have?|
    |Buy_CarB|What are the leasing options|
    

Identify conflicting intents. You can then create an entity that contains each car brand and [branch the dialog based on the entity](https://www.infobip.com/docs/answers/ai-chatbots/intents#use-entities-to-branch-a-dialog-how-to).

## Use variations in training phrases

In an intent, make sure that the training phrases have variations.

Example: For the Exchange_Currency intent, the training phrases are as follows.

- I would like to change from EUR to USD.
- I would like to change from EUR to GBP.
- I would like to change from USD to EUR.
- I would like to change from USD to GBP.
- I would like to change from GBP to EUR.
- I would like to change from GBP to USD.

If the end user sends a different variation of the message, the chatbot may not be able to identify the intent.

Example: The end user's message is "I want to convert US dollars to British pounds".

This message is a variation of the training phrase, 'I would like to change from USD to GBP.' There are no overlapping words between the end user's message and the training dataset because the chatbot ignores words such as 'I' and 'to'. So, the chatbot is unable to assign the Exchange_Currency intent to the message.

## Use sufficient number of training phrases

If an intent has very few training phrases, the chatbot will not have enough data to learn how to correctly identify the intent. The larger the number of training phrases for an intent, the better the chatbot can identify this intent when an end user sends a relevant message.

To complete validation, you need to add a minimum of 10 training phrases to an intent. This number includes only training phrases and not keywords. Infobip recommends that you add at least 100 training phrases. For the best performance, add at least 400 training phrases.

## Create more training phrases for frequently occurring words

Words that occur frequently in an intent are more important than words that occur rarely. So, the number of training phrases for the frequently-occuring words must be more than that of the rarely-occuring words.

If a word is used frequently in end user messages, add more training phrases that contain this word. Example: If the word 'money' is used in 70% of end user messages and the word 'current' is used in only 2% of the messages, create more training phrases that contain the word 'money'.

- How much money is there in my account?
- Show me my money.
- How much money do I have?

## Balance the training dataset size

If all intents are similar in size, the chatbot will assign the same priority to each intent. A less important intent may be assigned to an end user's message. To avoid this issue, make sure that the training dataset for important intents is larger than that of less important intents. Example: For a bank, the Mortgage intent is more important than the Welcome and Goodbye intents.

[Natural Language Processing](https://www.infobip.com/docs/answers/ai-chatbots/nlp "Natural Language Processing")