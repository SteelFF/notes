
## BookMark
- [[#Training dataset for intents]]
    - [[#Training phrases]]
    - [[#Important words]]
- [[#Named entity recognition attributes]]
    - [[#Type]]
    - [[#Subtype]]
    - [[#Scope]]
    - [[#List]]
    - [[#Visible to agent]]
    - [[#Sensitive value]]
- [[#The user interface]]
    - [[#Intents tab]]
    - [[#Custom attribute types tab]]
    - [[#NER attributes tab]]
- [[#How to]]
    - [[#Create an intent]]
    - [[#Add a training phrase to an intent]]
    - [[#Add an important word to an intent]]
    - [[#Create a NER attribute and add to an intent]]
    - [[#Assign an intent to a dialog]]
    - [[#Use entities to branch a dialog]]
    - [[#Import intents]]
    - [[#Export intents]]
    - [[#Rename an intent]]
    - [[#Delete an intent]]
    - [[#Delete a training phrase]]
    - [[#Delete an important word]]
    - [[#Delete a NER attribute]]
- [[#Test intents]]
- [[#Troubleshooting]]
    - [[#Number of training phrases in intent higher or lower than others]]
    - [[#Incorrect intent is matched]]
- [[#Analytics]]
    - [[#Identify unrecognized intents]]

When end users contact a chatbot, they have a specific goal. Example: They may contact the chatbot to obtain information, perform an action, or resolve an issue. A well-designed chatbot can identify these goals and can respond accordingly.

Use intents to identify the end users' goals. To use intents, do the following:

1. Identify a list of potential goals.
2. For each goal, create an intent with a training dataset.
3. Assign the intents to relevant dialogs.

When an end user sends a message, the chatbot identifies the relevant intent and directs the end user to the dialog that is associated with the intent.

Example: For a chatbot that manages bookings:

|End user's message|Goal of the message|Intent that is matched for the goal|Dialog to which the intent is assigned|
|---|---|---|---|
|I want to create a new reservation|Create a booking|Create_booking|Create|
|Need to reschedule my booking|Manage a booking|Manage_booking|Manage|
|Can I cancel my appointment?|Cancel a booking|Cancel_booking|Cancel|

##### NOTE

Intents are mandatory for [AI](https://www.infobip.com/docs/answers/ai-chatbots) chatbots and optional for [rule-based](https://www.infobip.com/docs/answers/chatbot-types#rule-based-chatbots) chatbots.

If you enable [natural language processing](https://www.infobip.com/docs/answers/ai-chatbots/nlp) for a dialog, you must assign an intent to that dialog for NLP to work correctly.

## Training dataset for intents

The training dataset for intents helps the chatbot identify the intent of the end user's message. The dataset contains training phrases and important words.

### Training phrases

Training phrases are a set of phrases and sentences that end users are likely to use when they communicate with your chatbot. Training phrases help the chatbot identify the correct intent. The more the number of training phrases, the higher the probability for the chatbot to take the right action.

The following image shows examples of the training phrases for the **Baggage** intent.

[![Training dataset](https://www.infobip.com/docs/images/uploads/articles/answers-ai-nlp-synonym-matching-training-dataset.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-nlp-synonym-matching-training-dataset.png)

### Important words

Important words are single words that are added to intents and help the chatbot uniquely identify the intent. Example: For the Manage_booking intent, the important word is 'manage', whereas for the Cancel_booking intent, the important word is 'cancel'. Depending on which important word is present in the end user's message, the chatbot identifies the relevant intent. 

**Synonyms**: When end users respond to your chatbot, they may not necessarily use the important words. Instead, they may use variations or synonyms of these words. Example: Instead of using the word 'manage', the end user may say 'reschedule'.

So, instead of adding these variations and meanings as separate important words, you can add them as synonyms to an existing important word. Example: For the important word 'manage', you can add the following synonyms: reschedule, move, change, and update.

The chatbot takes the same action whether the end user's response contains the important word or a synonym.

## Named entity recognition attributes

Use [Named entity recognition](https://www.infobip.com/glossary/ner) (NER) attributes to extract information when end users respond with unstructured text. NER is a part of [natural language processing](https://www.infobip.com/docs/answers/ai-chatbots/nlp) (NLP). The goal of NER is to recognize and categorize information (entities) in the end user's message. These entities can be either a single word or a set of words. 

Use NER attributes in combination with intents in AI chatbots.

NER attributes have the following fields.

### Type

NER attributes can have one of the following data types:

- Standard NER attribute type
- Custom attribute type

#### Standard NER attribute types

NER attributes can have one of the following NER-enabled data types. The behavior of these data types is different from the data types for [standard attributes](https://www.infobip.com/docs/answers/chatbot-structure/attributes#standard-attributes-attribute-types).

- **City**: Data set from [GeoNames](http://www.geonames.org/)
- **Country**: Data set from [GeoNames](http://www.geonames.org/)
- **Currency**: Numeric amounts and currency names, codes, or symbols
- **Date**: The date is stored in the format YYYY-MM-DD.
    - If an end user's message contains the date in Month/Day/Year format, the date is stored incorrectly.
    - If an end user's message contains the date in a different format, Answers converts the date to the format YYYY-MM-DD.
    - The attribute supports only names of English months.
    - If a part of a date is missing, Answers stores the value as 0. Example: If an end user's message contains '1st March' and the year is missing, the date is stored as 0-3-1.
    - The attribute accepts the following values:  
        Day: Either from 1 to 31 or is missing  
        Month: Either from 1 to 12 or is missing  
        Year: Either from 1900 to 2100 or is missing
    - If the day (DD) information is present, the month information (MM) must also be present.
    - Example:

|End user's message|Date attribute|
|---|---|
|1st of March, 2023|2023-3-1|
|01/03/2023|2023-3-1|
|1.3.2023|2023-3-1|
|1/3/2023|2023-3-1|
|1-3-2023|2023-3-1|
|First of March, 2023|2023-3-1|
|First of March|0-3-1|
|March|0-3-0|
        
- **Day of week**: Text for the days of the week, in English
- **Number**: Numbers written in digits. Example: Use a NER attribute of type, Number, to extract the customer number
- **Relative date**: Supports the following relative dates.
    - TODAY
    - YESTERDAY
    - TOMORROW
    - DAY_BEFORE_YESTERDAY
    - DAY_AFTER_TOMORROW
    - LAST_MONTH
    - LAST_YEAR
    - THIS_MONTH
    - THIS_YEAR
    - NEXT_MONTH
    - NEXT_YEAR  
        Example:
        
|End user's message|Relative Date attribute|
        |---|---|
        |That happened today?|TODAY|
        |Is that the day before yesterday?|DAY_BEFORE_YESTERDAY|
        |Is that the day after tomorrow|DAY_AFTER_TOMORROW|
        |Was that the previous month?|LAST_MONTH|
        |Was that the last month?|LAST_MONTH|
        

##### Validation for standard data types

|Standard data type|Condition|Validation|
|---|---|---|
|City|is equal to \| is not equal to \| is null \| is not null|Data set from GeoNames|
|Country|is equal to \| is not equal to \| is null \| is not null|Data set from GeoNames|
|Currency|is equal to \| is not equal to \| is null \| is not null|Numeric amounts and currency names/codes/symbols|
|Date|is equal to \| is not equal to \| is before \| is before or equal to \| is after \| is after or equal to \| is null \| is not null|Checks if a word in the text is a date element (day, year, or month)|
|Day of the week|is equal to \| is not equal to \| is null \| is not null|Textual input for days of the week in English|
|Number|is equal to \| is not equal to \| is before \| is before or equal to \| is after \| is after or equal to \| is null \| is not null|Numbers written with digits|

#### Custom attribute types

In addition to the NER-enabled data types, which are defined by Answers, you can create custom attribute types for NER attributes. Use custom attribute types to identify predefined entity values in end user messages.

Create a custom attribute type and add custom values to it. If the end user's message contains one of the custom values, configure the chatbot to direct the flow accordingly. For an example that explains how to use custom attribute types, refer to the [Use entities](https://www.infobip.com/docs/answers/ai-chatbots/intents#use-entities-to-branch-a-dialog-how-to) section.

Custom attribute types are case sensitive. So, add values in the same format that you expect end users to use.

All AI-supported languages support custom attribute types.

#### Supported data type by language

The availability of an attribute type depends on the chatbot language.

English, Spanish, and Portuguese support the following NER attribute types:

- City
- Country
- Currency
- Date
- Day of week
- Number
- Relative date
- Custom attribute types

Other languages support the following NER attribute type:

- Number
- Custom attribute types

##### NOTE

If you change the language of the chatbot and the new languages does not support the NER attribute type, the NER attribute becomes invalid. You must correct the error before you activate the chatbot. Example: Your chatbot language is English and you create the startDate NER attribute with the Date attribute type. If you then change the chatbot language to Arabic, the startDate NER attribute is invalid because Arabic does not support the Date NER attribute type.

### Subtype

Subtypes enable you to capture 'to' and 'from' values in responses from end users. Example: If your chatbot is for booking flights, use two **City** type NER attributes with **Source** and **Target** subtypes to capture the 'from' and 'to' cities.

NER attributes can have one of the following subtypes:

- Source
- Target
- None

Subtype is optional. But, if you want to assign two NER attributes of the same [type](https://www.infobip.com/docs/answers/ai-chatbots/intents#type-named-entity-recognition-attributes) to an intent, you must define the subtype for each attribute. Also, the subtype for each of these attributes must be different. 

If you do not add the subtype, you can add only one attribute of a specific type to an intent.

Example: For Intent_1, you can add the following attributes of the type City and define their subtypes:

- Location_from attribute - Source subtype
- Location_to attribute - Target subtype
- Location attribute - None subtype

When the end user sends a message, the attributes are recognized as follows:

|End user message|Location_from attribute|Location_to attribute|Location attribute|
|---|---|---|---|
|I want to travel from London to Zagreb|London|Zagreb|empty|
|I want to travel to Zagreb|empty|Zagreb|empty|
|I want to visit Zagreb|empty|empty|Zagreb|

##### NOTE

Source and Target subtypes are available for Country, City, and Currency attribute types.

### Scope

The scope can be one of the following:

- Local: The attribute is specific to a dialog
- Global: The attribute can be used across multiple dialogs within the chatbot

### List

You can select whether the NER attribute is for a single value or a list. If you define the attribute as a single value, the attribute stores the first relevant value from the end user's message. If you define the attribute as a list, the attribute stores all relevant values from the end user's message.

Example: You want to store the name of the city in the city attribute. The end user message is "I want to visit London, Chennai, and Zagreb". If you define the city attribute as a single value, it stores the first city in the end user's response, that is, London. If you define the city attribute as a list, it stores all the cities in the end user's response, that is, London, Chennai, and Zagreb.

To define the attribute as a list, select **List**. Otherwise, the attribute is set as a single value.

If you define the attribute as a list, you can use other Answers elements to obtain specific information. 

Example: You want to obtain the name of the city from the end user. The end user's message is "I want to visit London, Chennai, and Zagreb".

1. Define the city attribute as a list. city stores all the cities in the end user's response. So, city contains London, Chennai, and Zagreb.
2. Use the [Conditions](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#condition) element to identify the number of values in the city attribute.
    1. If the number of values is 1, use this value for the next steps of the chatbot flow. 
    2. If the number of values is more than 1, use either the Quick Reply or the Carousel element to ask the end user to provide the specific value from of the list of values.  
          
        In this example, the number of values is 3. So, use a Quick Reply or Carousel element to ask the end user the specific city from the 3 values.  
        "In which of these cities you would like to start your trip? London, Chennai, Zagreb"
3. The end user chooses Zagreb.
4. Use Attribute element [validation](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#attribute) to make sure that only 1 value, that is, Zagreb, is now stored in the city attribute.

### Visible to agent

Indicates whether the NER attribute value is visible to an agent when the chat is transferred from the chatbot to the agent. This field is applicable only when you either use the Infobip Conversations solution or integrate with an external contact center solution.

### Sensitive value

Indicates whether the NER attribute contains sensitive data. Example: The attribute is used for authentication, and it contains usernames or passwords. If you select this field, the attribute value is encrypted when it is stored in the database. This setting is especially useful when chats are transferred to live agents.

## The user interface

### Intents tab

This tab contains a list of intents.

- **Intent name**: Name of the intent
- **Training phrases**: The number of phrases used to train the intent
- **Important words**: The number of important words used with the intent
- **NER attribute**: The number of NER attributes used with the intent

[![The Intents tab](https://www.infobip.com/docs/images/uploads/articles/answers-intents-tab.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-tab.png)

To view the list of training phrases, important words, and NER attributes for an intent, click the intent and navigate to the relevant tab.

[![Training phrases for intents](https://www.infobip.com/docs/images/uploads/articles/answers-intents-training-phrases.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-training-phrases.png)

### Custom attribute types tab

To access this tab, go to **Attributes** tab > **Custom attribute types**. This tab shows a list of custom attribute types and the number of list items that they contain. Click a custom attribute type to view these items.

[![Custom attributes tab](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-custom-attributes-tab.png)](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-custom-attributes-tab.png)

### NER attributes tab

To view a list of NER attributes, go to the **Attributes** tab > **NER attributes** tab.

Each attribute has the following fields.

- **Name**: Name of the NER attribute
- **Type**: Either a NER-enabled standard attribute type or a custom attribute type
- **Subtype**: Subcategory for the NER attribute type
- **Scope**: Can be one of the following:
    - Local: The attribute is specific to a dialog
    - Global: The attribute can be used across multiple dialogs within the chatbot
- **List**: Indicates whether the NER attribute is for a single value or a list
- **Visible to agent**: Indicates whether the NER attribute value is visible to an agent when the chat is transferred from the chatbot to the agent.
- **Sensitive value**: Indicates whether the NER attribute contains sensitive data such as usernames or passwords.

[![NER attributes tab](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-ner-attributes-tab.png)](https://www.infobip.com/docs/images/uploads/articles/answers-attributes-ner-attributes-tab.png)

## How to

### Create an intent

Refer to the [guidelines](https://www.infobip.com/docs/answers/ai-chatbots/guidelines) for creating intents.

You can create an intent either from the Intents tab or from a dialog.

#### Create intent from the Intents tab

1. On the Infobip [web interface](https://portal.infobip.com/), go to **Answers** > **Chatbots** and navigate to your chatbot.
2. Go to the **Intents** tab.
3. Click **Add intent**.
4. Click either the pencil icon or within the 'New intent' text, and enter the name of the intent.  
    [![Create intent](https://www.infobip.com/docs/images/uploads/articles/answers-intents-create-intent-from-intents-tab.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-create-intent-from-intents-tab.png)
5. Add [training phrases](https://www.infobip.com/docs/answers/ai-chatbots/intents#add-a-training-phrase-to-an-intent-how-to) and [important words](https://www.infobip.com/docs/answers/ai-chatbots/intents#add-an-important-word-to-an-intent-how-to) to the intent.

#### Create intent from within a dialog

You can create intents only from standard dialogs. You cannot create intents from the Default, Authentication, and Session Expire dialogs.

Follow these steps.

1. On the Infobip [web interface](https://portal.infobip.com/), go to **Answers** > **Chatbots** and navigate to your chatbot.
2. Click the dialog for which you want to create the intent.  
    [![Create intent from dialog](https://www.infobip.com/docs/images/uploads/articles/answers-intents-create-intent-from-dialog.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-create-intent-from-dialog.png)
3. In the **Intent Settings** section, click the menu and click **Create new intent**.  
    [![Create new intent](https://www.infobip.com/docs/images/uploads/articles/answers-intents-create-intent-from-dialog-new-intent.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-create-intent-from-dialog-new-intent.png)
4. Enter the name of the intent and click the checkmark.  
    [![Name the intent](https://www.infobip.com/docs/images/uploads/articles/answers-intents-create-intent-from-dialog-name-intent.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-create-intent-from-dialog-name-intent.png)
5. Add [training phrases](https://www.infobip.com/docs/answers/ai-chatbots/intents#add-a-training-phrase-to-an-intent-how-to) and [important words](https://www.infobip.com/docs/answers/ai-chatbots/intents#add-an-important-word-to-an-intent-how-to) to the intent.

### Add a training phrase to an intent

Refer to the [guidelines](https://www.infobip.com/docs/answers/ai-chatbots/guidelines) for creating training phrases.

1. Go to the **Intents** tab.
2. Click the intent for which you want to add training phrases.
3. In the **Training phrases** tab, click **Add training phrase** and add the phrase.  
    [![Add training phrase](https://www.infobip.com/docs/images/uploads/articles/answers-intents-add-training-phrase.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-add-training-phrase.png)

### Add an important word to an intent

Refer to the [guidelines](https://www.infobip.com/docs/answers/ai-chatbots/guidelines) for creating important words.

1. Go to the **Intents** tab.
2. Click the intent for which you want to add important words.
3. In the **Important Words** tab, click **Add important word**.
4. Enter the important word.
5. (Optional) Add the synonyms.  
    [![Add important words](https://www.infobip.com/docs/images/uploads/articles/answers-intents-add-important-word.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-add-important-word.png)

### Create a NER attribute and add to an intent

Carry out the steps in each of the following sections sequentially:

1. Create an intent. Refer to the [How to](https://www.infobip.com/docs/answers/ai-chatbots/intents#create-an-intent-how-to) section.
2. Create a custom attribute type.
3. Create a NER attribute that uses the custom attribute type.
4. Add the NER attribute to the intent.

#### Create a custom attribute type

1. On the Infobip [web interface](https://portal.infobip.com/), go to **Answers** > **Chatbots** and navigate to your chatbot.
2. Go to the **Attributes** tab > **Custom attribute types** tab.
3. Click **Add custom type**.
4. Click the new custom type that is created.  
    [![Add custom attribute type](https://www.infobip.com/docs/images/uploads/articles/answers-intents-ner-add-custom-type.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-ner-add-custom-type.png)
5. Click either the pencil icon or within the 'New custom attribute type' text, and enter the name of the custom attribute type. Example: mobile_devices.
6. Click **Add custom value**.
7. Add each value that this attribute type can contain. Example: mobile phone, tablet, laptop. Enter the values in the exact format, including upper and lower case, in which you expect the end user to respond.  
    [![Add custom values](https://www.infobip.com/docs/images/uploads/articles/answers-intents-ner-add-custom-values.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-ner-add-custom-values.png)

#### Create a NER attribute

1. Go to the **Attributes** tab > **NER attributes** tab.
2. Click **Add NER attribute**.
3. In the **Name** field, enter a name for the attribute. Example: mobile_attribute.
4. In the **Type** field, choose the custom attribute that you created. If you want to use the attribute in a [Conditions](https://www.infobip.com/docs/answers/chatbot-elements/core-elements#condition) element, refer to the [Validation for Attributes](https://www.infobip.com/docs/answers/chatbot-structure/attributes#validation-of-attribute-values-validation-for-attributes) section.  
    [![Add the custom attribute type to the NER attribute](https://www.infobip.com/docs/images/uploads/articles/answers-ai-intents-ner-attribute.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-intents-ner-attribute.png)
5. In the **Scope** field, choose one of the following:
    - Local: The attribute is specific to a dialog.
    - Global: The attribute can be used across multiple dialogs within the chatbot.
6. In the [**List**](http://www.infobip.com/docs/answers/ai-chatbots/intents#list-named-entity-recognition-attributes) field, select whether the NER attribute is for a single value or a list.
7. In the [**Visible to agent**](https://www.infobip.com/docs/answers/ai-chatbots/intents#visible-to-agent-named-entity-recognition-attributes) field, determine whether the attribute value is visible to an agent when the chat is transferred from the chatbot to the agent.
8. In the [**Sensitive value**](https://www.infobip.com/docs/answers/ai-chatbots/intents#sensitive-value-named-entity-recognition-attributes) field, select whether the attribute contains sensitive data.

#### Add the NER attribute to the intent

1. Go to the **Intents** tab.
2. Navigate to the intent > **NER attributes** tab.
3. Click **Add NER attribute**.
4. Choose the NER attribute that you created.  
    [![Add a NER attribute to an intent](https://www.infobip.com/docs/images/uploads/articles/answers-ai-intents-add-ner-to-intent.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-intents-add-ner-to-intent.png)

### Assign an intent to a dialog

After you create an intent, assign it to the relevant dialog. Example: Assign the Manage_booking intent to the Manage dialog.

You can assign intents only to standard dialogs. You cannot assign intents to the Default, Authentication, and Session Expire dialogs.

1. In the chatbot editor, click the name of the dialog to view the settings.  
    [![Assign an intent to a dialog](https://www.infobip.com/docs/images/uploads/articles/answers-intents-assign-intent-to-dialog.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-assign-intent-to-dialog.png)
2. To use an existing intent, choose the intent from the list.  
    [![Choose the intent](https://www.infobip.com/docs/images/uploads/articles/answers-intents-assign-intent-to-dialog-choose-intent.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-assign-intent-to-dialog-choose-intent.png)
3. To create a new intent for the dialog, refer to the [Create an Intent](https://www.infobip.com/docs/answers/ai-chatbots/intents#create-an-intent-how-to) section.

### Use entities to branch a dialog

If end users contact your chatbot for similar products or categories, you would need to create a separate intent and dialog for each product. When directing the flow, you would need to add a branch for each product. You may need to add a similar set of training phrases to each intent.

Also, if there are several products, it could lead to a large number of intents and branches. 

To avoid this situation, you can do one of the following:

- If there is a small number of similar products, you can branch based on entity values.
- If there is a large number of similar products, you can branch based on entity types.

#### Branch based on entity values

Example: End users contact your chatbot about different car brands - CarA, CarB, and CarC.

To branch based on entity values, do the following:

1. Create a custom attribute type for cars and add the brands as attribute values.  
    [![Create a custom attribute](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-create-entity.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-create-entity.png)
2. Create a NER attribute. In the **Type** field, choose the custom attribute type.  
    [![Create NER attribute](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-create-ner-attribute.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-create-ner-attribute.png)
3. Create an intent that contains training phrases for all car brands.  
    [![Create an intent](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-create-intent.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-create-intent.png)
4. Add the NER attribute to the intent.  
    [![Add NER attribute to the intent](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-add-ner-to-intent.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-add-ner-to-intent.png)
5. Branch the chatbot based on the value of the NER attribute.  
    [![Branch based on value](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-branch.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-branch.png)
6. Test the flow in the simulator. Example: If you enter a question about CarB, the chatbot takes you to the dialog for CarB.  
    [![Test in the simulator](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-test.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-value-test.png)  
     

#### Branch based on entity types

Example: End users contact your chatbot about different car brands and models.

- CarA - Model_A1, Model_A2, Model_A3, Model_A4
- CarB - Model_B1, Model_B2, Model_B3, Model_B4
- CarC - Model_C1, Model_C2, Model_C3

To branch based on entity types, do the following:

1. Create a custom attribute type for each car brand and add the models as attribute values.
    - Entity_CarA = {Model_A1, Model_A2, Model_A3, Model_A4}
    - Entity_CarB = {Model_B1, Model_B2, Model_B3, Model_B4}
    - Entity_CarC = {Model_C1, Model_C2, Model_C3}  
        [![Create the custom attribute type](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-create-entity.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-create-entity.png)
2. Create NER attributes for each car brand. In the **Type** field, choose the custom attribute types.  
    [![Create NER attribute](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-create-ner-attribute.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-create-ner-attribute.png)
3. Create an intent that contains training phrases for all car brands.  
    [![Create an intent](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-create-intent.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-create-intent.png)
4. Add the NER attributes to the intent.  
    [![Add NER attribute to the intent](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-add-ner-to-intent.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-add-ner-to-intent.png)
5. Branch the chatbot based on the NER attribute.  
    [![Branch based on entity type](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-branch.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-branch.png)
6. Test the flow in the simulator. Example: If you enter a question about Model_B3, the chatbot takes you to the dialog for CarB.  
    [![Test the flow in the simulator](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-test.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-guidelines-entity-branch-type-test.png)

### Import intents

Automate the process of adding intents and training phrases, instead of manually adding them one by one.

Carry out the steps in the following sections sequentially:

1. (Optional) Download a sample file for reference.
2. Create a file that contains intents, training phrases, important words, and synonyms.
3. Upload the completed file.

#### Download a sample file

This is an optional process.

1. On the Infobip [web interface](https://portal.infobip.com/), go to **Answers** > **Chatbots** and navigate to your chatbot.
2. Go to the **Intents** tab.
3. Click **Import**.
4. Click **Download example**.

#### Create the file

Prepare a file that contains phrases and sentences that end users are likely to use in conversations with your chatbot. Ideally, use phrases taken from your customer-facing portals. Example: online chats with your customer service.

Make sure that the file follows these criteria:

- The file type must be .csv or .txt.
- There must be no empty rows.
- The file must not have a header.
- Training phrases:
    - Add each training phrase to a separate line.
    - Use a semicolon to separate training phrases and intents.
    - Organize the training phrases by intent.
    - Format:  
        Training_Phrase;Intent
- Important words:
    - Add each important word to a separate line.
    - Use a semicolon to separate important words, intents, and the synonyms of the important words.
    - Use commas to separate multiple synonyms.
    - Organize the important words by intent.
    - Format:   
        Important_Word;Intent;Synonym1,Synonym2,Synonym3

**Example**: For the Welcome and Goodbye intents, the file is as follows:  
How are you?;Welcome  
How can I help?;Welcome  
Thank you.;Goodbye  
Glad to have been of help.;Goodbye  
Bye.;Goodbye;farewell,ciao  
Close;Goodbye;end,finished

#### Import the file

1. On the Infobip [web interface](https://portal.infobip.com/), go to **Answers** > **Chatbots** and navigate to your chatbot.
2. Go to the **Intents** tab.
3. Click **Import**.  
    [![Import intents](https://www.infobip.com/docs/images/uploads/articles/answers-intents-import.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-import.png)
4. Select the file.
5. Click **Import**.  
    [![Choose the file](https://www.infobip.com/docs/images/uploads/articles/answers-import-intent.png)](https://www.infobip.com/docs/images/uploads/articles/answers-import-intent.png)

If there are issues with the file, Answers shows an error.

- Incorrect file type  
    [![Error when importing intents](https://www.infobip.com/docs/images/uploads/articles/error-import-intents-wrong-file.png)](https://www.infobip.com/docs/images/uploads/articles/error-import-intents-wrong-file.png)
- Incorrect formatting. Example: wrong delimiter  
    [![Error when importing intents](https://www.infobip.com/docs/images/uploads/articles/error-import-intents.png)](https://www.infobip.com/docs/images/uploads/articles/error-import-intents.png)

### Export intents

1. On the Infobip [web interface](https://portal.infobip.com/), go to **Answers** > **Chatbots** and navigate to your chatbot.
2. Go to the **Intents** tab.
3. Select one or more intents on the page. To export all intents, select the checkbox against **Intent name**.  
    [![Export intents](https://www.infobip.com/docs/images/uploads/articles/answers-ai-intents-export.png)](https://www.infobip.com/docs/images/uploads/articles/answers-ai-intents-export.png)
4. Click **Export**.

The intents are downloaded in a predefined format as a .csv file.

##### NOTE

Any NER attributes, which are part of the intents, are not exported.

### Rename an intent

1. Click an intent.  
    [![Edit the intent](https://www.infobip.com/docs/images/uploads/articles/answers-intents-edit-1.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-edit-1.png)
2. Either click the name of the intent or click the pencil icon.  
    [![Rename the intent](https://www.infobip.com/docs/images/uploads/articles/answers-intents-rename.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-rename.png)
3. Add the new name.

### Delete an intent

Follow one of these options:

#### Delete one or more intents

1. Select one or more intents.
2. Click **Delete**.  
    [![Delete the intent](https://www.infobip.com/docs/images/uploads/articles/answers-intents-delete-1.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-delete-1.png)

#### Delete from within an intent

1. Click an intent.  
    [![Edit the intent](https://www.infobip.com/docs/images/uploads/articles/answers-intents-edit-1.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-edit-1.png)
2. Click the **Delete** icon.  
    [![Delete the intent](https://www.infobip.com/docs/images/uploads/articles/answers-intents-delete-2.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-delete-2.png)
3. Confirm the action.  
    [![Delete intent](https://www.infobip.com/docs/images/uploads/articles/Delete-intents_2.png)](https://www.infobip.com/docs/images/uploads/articles/Delete-intents_2.png)

When the intent is deleted, you get a notification.

[![Deleted intents](https://www.infobip.com/docs/images/uploads/articles/Intents-deleted.png)](https://www.infobip.com/docs/images/uploads/articles/Intents-deleted.png)

### Delete a training phrase

1. Go to the **Intents** tab.
2. Click the intent in which you want to delete training phrases.
3. In the **Training phrases** tab, hover over the training phrase that you want to delete.
4. Click the **Delete** icon.  
    [![Delete a training phrase](https://www.infobip.com/docs/images/uploads/articles/answers-intents-training-phrase-delete.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-training-phrase-delete.png)

### Delete an important word

1. Go to the **Intents** tab.
2. Click the intent for which you want to delete important words.
3. In the **Important Words** tab, hover over the important word that you want to delete.
4. Click the **Delete** icon.  
    [![Delete important word](https://www.infobip.com/docs/images/uploads/articles/answers-intents-important-word-delete.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-important-word-delete.png)

### Delete a NER attribute

1. Go to the **Intents** > **NER attributes** tab.
2. Hover over the NER attribute that you want to delete.
3. Click the **Delete** icon.  
    [![Delete NER attribute](https://www.infobip.com/docs/images/uploads/articles/answers-intents-ner-attribute-delete.png)](https://www.infobip.com/docs/images/uploads/articles/answers-intents-ner-attribute-delete.png)

## Test intents

Check how well Answers recognizes the intents in your chatbot.

In the simulator, do the following:

1. Click **Start Simulation**.
2. Enter an end user message.
3. Click **Intent engine**.

[![intent engine in simulator](https://www.infobip.com/docs/images/uploads/articles/answers-simulator-intent.png)](https://www.infobip.com/docs/images/uploads/articles/answers-simulator-intent.png)

In the right-hand pane, you can view the following:

**Intent matched:** The intent with the highest score that was recognized by evaluating the [tokenized](https://www.infobip.com/docs/answers/ai-chatbots/nlp#tokenization) text against training phrases. If an incorrect intent is matched, follow the steps in the [Incorrect intent is matched](https://www.infobip.com/docs/answers/ai-chatbots/intents#incorrect-intent-is-matched-troubleshooting) section.

**Route to dialog**: The dialog that was linked to the recognized intent (in the **intent matched** section) when the chatbot was built.

**Original text**: The original end user message without any changes, such as autocorrection or other post processing actions.

**Tokenized text**: The text that was obtained after word autocorrection and removing stop words.

**Autocorrected:** Indicates whether autocorrection was applied. If autocorrection was applied, the field value is **true**.

**Resolved attributes:** Resolved NER attributes that are configured for the recognized intent.

For any dialog, the intent engine details are available only outside that dialog.

## Troubleshooting

### Number of training phrases in intent higher or lower than others

The system has detected that the number of training phrases for one of your intents is significantly higher or lower than for others. Use a similar number of training phrases for all intents.

### Incorrect intent is matched

If the intent match is not correct, check the **Tokenized text** section to understand which words were used to evaluate the intent score.

[![intent engine in simulator](https://www.infobip.com/docs/images/uploads/articles/answers-simulator-intent.png)](https://www.infobip.com/docs/images/uploads/articles/answers-simulator-intent.png)

Example: Your chatbot uses the following intents - 'book appointment', 'cancel appointment', and 'reschedule appointment'. If Answers incorrectly recognizes 'book appointment' as the intent instead of 'cancel appointment, do one or more of the following:

- Add more training phrases to the correct intent (cancel appointment).
- Relocate some of the training phrases between intents (from 'book appointment' to 'cancel appointment')
- If intents are similar, merge them into a single intent, and use a different approach to distinguish between the actions. Example: Use a combination of named entity recognition (NER) and a custom attribute type. Refer to the [Use Entities to Branch a Dialog](https://www.infobip.com/docs/answers/ai-chatbots/intents#use-entities-to-branch-a-dialog-how-to) > Branch based on Entity Types section.

## Analytics

### Identify unrecognized intents

Use the unrecognized intents message list in the [Conversational analytics](https://www.infobip.com/docs/answers/analytics#conversational-analytics) to identify the end user messages for which the chatbot cannot identify the intent.

If this list also contains user start messages, it means you need to include these messages as training phrases in your starting intent.

If this list contains messages that you did not expect as possible training phrases or intents, use these messages as training phrases or create new intents, if required.

![Unrecognized intents list in analytics](https://www.infobip.com/docs/images/uploads/articles/answers-analytics-conversational-unrecognized-intents.png)