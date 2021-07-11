######################################
Chatbot and it's underlying Mechanism
######################################



.. note::

		This section of the documentation delivers a *high level design* of **Chatbot** and its functionalities. The code section (Developer oriented) consist of low level design and how bot actually works under the hood. 


Architecture of the bot / How Question and Answering happen in our bot
======================================================================

Bot access databases to answer queries, Those database may contain some json files. those json files contains some predefined questions, answers and tags (Which we call intents). the very first thing our bot does whenever user type something is that it try to identify the intention of the user, what the user is talking about. :guilabel:`intent.py` Does this thing for us, it extracts useful information from the text e.g. Intent, named entites, slots (Slots are explained later in the section) once we have the intent of the text then it tries to find the similar intent in its existing database then look for appropriate responses under that intent. Every intent has it own conversational flows (described later in the section) or we can say it as the type of conversation. Every flow has some task associated with which is given by user and accomplished by bot.

**SAMPLE JSON :-**

This is not the exact same json which we use on premesis but the idea is exacatly same.

.. code:: json
    
    {"intents": [
        {"tag": "greeting",
         "patterns": ["Hi", "How are you", "Is anyone there?", "Hello", "Good day"],
         "responses": ["Hello, thanks for visiting", "Good to see you again", "Hi there, how can I help?"],
         "context_set": ""
        },
        {"tag": "goodbye",
         "patterns": ["Bye", "See you later", "Goodbye"],
         "responses": ["See you later, thanks for visiting", "Have a nice day", "Bye! Come back again soon."]
        },
        {"tag": "thanks",
         "patterns": ["Thanks", "Thank you", "That's helpful"],
         "responses": ["Happy to help!", "Any time!", "My pleasure"]
        },
        {"tag": "hours",
         "patterns": ["What hours are you open?", "What are your hours?", "When are you open?" ],
         "responses": ["We're open every day 9am-9pm", "Our hours are 9am-9pm every day"]
        },
        {"tag": "payments",
         "patterns": ["Do you take credit cards?", "Do you accept Mastercard?", "Are you cash only?" ],
         "responses": ["We accept VISA, Mastercard and AMEX", "We accept most major credit cards"]
        },
        {"tag": "opentoday",
         "patterns": ["Are you open today?", "When do you open today?", "What are your hours today?"],
         "responses": ["We're open every day from 9am-9pm", "Our hours are 9am-9pm every day"]
        }
   ]
   }

..  important::
    
    `To get the most out of the bot`, we should always keep domain in our mind while training the bot. the data should be domain specific and with no grammatical mistakes and abundance of sample question, answers and their intents associated with it. Domain can be a telecom company, hotel booking website, flight booking website etc.


Configuring your bot
====================

Configuration means giving scopes to our bot under which it is going to perform or answer a certain query.
Identify the scenarios that users might use your application for. Define the actions and relevant information that needs to be recognized. Start by defining intents and entities. Then, add training utterances for each intent. Start testing your model with other utterances to get a sense of how the app behaves, and you can decide if any improvement is needed. You can improve your application by following these best practices.


.. image:: G:/Sphinx/Images/Screenshot1.png
  :width: 30%

.. image:: G:/Sphinx/Images/Screenshot2.png
  :width: 30%



.. tip::

	Configuiring the bot let's you add the utterences (questions) or the json files which we have talked about in the earlier section, So it's always better to add every possible utterence ( relevant to the flow) your bot can encounter during the conversation.

Conversation and Flows?
=======================

So, flow is basically a way to indentify the type of converstation user is having with the bot.
Every type of user input has a intent associated with it which we have to figure out and then start the flow accordingly. bot is built with a fixed set of intents and a set of if statements describing how to respond in different contexts.

===============
Types of Flows
===============

- Third party integration/api conversation.
- Faq.
- Goal oriented conversation.
- NL to SQL.

FAQs
----

These type of questions doesn't have any goal or a motive. User has some doubt or want a piece of information regarding a service provided by the company (over which the bot is deployed) and job of the bot is to clarify the query and give coinciding answer. This flow ends immediately after the response.

`Example`: *What is your cancellation Policy?*, *Can I book more than one room at a time?*
These ``type`` of questions comes under the FAQ flow. 


.. image:: G:/Sphinx/Images/Screenshot.png
  :width: 400
  :alt: Alternative text


Goal oriented conversation
---------------------------

Goal oriented conversations are more human like conversation, in which bot tries to hold the conversation by compeleting the task given by user within the conversation. 

`Example`: *book a flight for me?* , these type of questions comes under the Goal oriented flow.
here, unlike FAQ flow the bot has a motive which needs to be completed within the conversation. 


"""""""""""""""
What are slots?
"""""""""""""""
In order to complete task given by user, bot might need some data (```data which is needed for the task```) of user ``E.g.``:- Name, phone number, ID, date of birth etc. the flow ends after all of the slots has been asked and the task givin by user is accomplished. ```A Goal is considered as a single conversation```. we can consider slots as the prompts givin by bot to ask for some information.

**THIS IS THE SAMPLE JSON USED DURING THE GOAL ORIENTED CONVERSATIONS**

.. code:: json
    
    {
    "checkbalance": {
        "question": "check balance",
        "category": "",
        "sub_category": "",
        "image_url": "https://bot.delhicctv.in/images/oredoo.png",
        "message": "Account Balance: 10 TSD valid until 01-April-2021 ,Data Balance:   2GB until 23rd-March-2021,SMS:   300 valid until 23rd-March-2021,Minutes:  300 Minutes valid until 23rd-March-2021",
        "slots": {
            "mobile-number": {
                "data_validation": "true",
                "api_url": "https://bot.delhicctv.in/checkauth",
                "parameter": "mobile-number",
                "method": "POST",
                "image_url": "https://bot.delhicctv.in/images/oredoo.png",
                "action_api_false": {
                    "message": "This mobile number is not registered"
                },
                "sample_data": [
                    "8887664546"
                ],
                "exhaustive": "false",
                "data_type": "num",
                "question": "May I have your Mobile number?"
            },
            "otp": {
                "data_validation": "true",
                "api_url": "https://bot.delhicctv.in/checkotp",
                "parameter": "otp",
                "method": "POST",
                "image_url": "https://bot.delhicctv.in/images/oredoo.png",
                "action_api_false": {
                    "message": "This OTP is wrong Please enter the correct OTP"
                },
                "sample_data": [
                    "12345"
                ],
                "exhaustive": "false",
                "data_type": "num",
                "question": "We have sent you an OTP on your registered mobile"
            },
            "balance-type": {
                "data_validation": "false",
                "api_url": "",
                "image_url": "https://bot.delhicctv.in/images/oredoo.png",
                "data_type": "string",
                "exhaustive": "true",
                "sample_data": [
                    "all",
                    "internet",
                    "voice"
                ],
                "question": "Choose one of the options below to check your balances"
            }
        }
    }
    }



Third party integration/api conversation
----------------------------------------

yet to be written


NL to SQL
----------

yet to be written


Explaining Json and Its Characteristics
-----------------------------------------

yet to be written



Example code
=============


.. code:: python	

	def get_current_goal(self):
       	nlu.AppointmentReqSet = []
        s = []
        print('GOAL HISTORY',State.GoalHistory)
		current_goal=None
        if unique.public_id in State.GoalHistory:
            if len(State.GoalHistory[unique.public_id])>0:
                if  State.GoalHistory[unique.public_id][-1] in dataflag.keys():
                    current_goal =  State.GoalHistory[unique.public_id][-1]
                    for i in dataflag[current_goal]['slots'].keys():
                        nlu.AppointmentReqSet.append(i)
                    State.saveSlots(data= current_goal ,goal_slots = nlu.AppointmentReqSet)
                if State.GoalHistory[unique.public_id][-1] in api_data.keys():
                    current_goal =  State.GoalHistory[unique.public_id][-1]
		print('current goal',current_goal,'current goal')
        return current_goal
        


Major modules
==============

- `nlu`: user input goes into nlu.
- `nlg`: from nlu it goes into nlg and nlg returns the bot answer.


.. highlight:: rst

.. role:: python(code)
    :language: python

.. role:: latex(code)
    :language: latex

Now here are latex command :latex:`\\setlength` and python command
:python:`import`, created by ``:python:`import```.  Here is a
:literal:`literal`, which stays a literal, and
:code:`.. highlight:: rst` makes code role look as it looks.

Some :red:`colored text` at last!

.. role:: red

This is :magenta:`just` a test …
:ref:`Example code`