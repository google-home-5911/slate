---
title: Nationwide Life Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the working documentation for the Nationwide Conversational Assistant "Nationwide Life". The following sections 
describe the collective functionality of the application. This includes its technology stack, architecture, methods, and datebase structure,
and any other additional notes we found important to pass on to the next team of developers. 

# Authentication



# Architecture
<img src="images/archgen.png">

## General

## Dialogflow

## Heroku

## Sqlite3


<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Methods

Methods related to the app's immediate functional requirements are located in app.py. These methods are as follows:

## webhook


```python
from flask import Flask
from flask import request, redirect, session, url_for, make_response, render_template


@app.route('/webhook', methods=['POST'])
def webhook():
    req = request.get_json(silent=True, force=True)

    print("Requestzzz:")
    print(json.dumps(req, indent=4))

    res = processRequest(req)
    res = json.dumps(res, indent=4)

    r = make_response(res)
    r.headers['Content-Type'] = 'application/json'
    return r
```


This endpoint gets and parses the JSON generated from Dialogflow after an action is triggered. 


### HTTP Request

`POST http:/testnw2.herokuapp.com/webhook`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.


## processRequest




This endpoint processes the JSON request that we received from Dialogflow. In that JSON request exists a specific action that corresponds to what intent was triggered in Dialogflow. We map said action to a specific function within our webhook. 

<aside class="warning">If no function exists for the given function, nothing will happen.</aside>


### Parameters

Parameter | Description
--------- | -----------
req | The request JSON received from Dialogflow to be processed





## obtainThyCredentials

```python
 event = service.events().insert(calendarId='primary', body=event).execute()
```
Grants access to the Google Calendar API by storing an access token within the project directory. If no access token is found, it will 
run an authentication flow in order to obtain said token. See [Google API Client Secrets Flow](https://developers.google.com/api-client-library/python/guide/aaa_client_secrets) for more information. For the purposes of the demo, in order to obtain the access token for a given you, run the script under "shell" to the right in a command line. A browser window will appear for you to authenticate. 


<aside class="success">
After authenticating in the browser window once, you will not need to do so again unless you wish to authenticate another user. 
</aside>


## scheduleEvent
Maps to action "Appointment" in Dialogflow. Authenticates user. Schedules event on user's Google Calendar given 
parameters  <code>date</code> and <code>time</code>. Returns a return speech back to the users.

### Parameters
Parameter | Description
--------- | -----------
req | The request JSON. We obtain parameters <code>date</code> and <code>time</code> from the request.



## userInfo



Outputs the user's information: name, address, phone number, and beneficiary. Using the request <code>req</code> JSON,
we obtain the user's ID. Then we query the database using this ID to return the user's information. 


### Parameters

Parameter | Description
--------- | -----------
req | The request JSON. We obtain parameters <code>userID</code> from the request. 




## changeAddress


Updates the user's address. Using the request <code>req</code> JSON,
we obtain the user's ID. Then we query the database using this ID to update a user's address.  


### Parameters

Parameter | Description
--------- | -----------
req | The request JSON. We obtain parameters <code>userID</code> from the request. 




## changePhone


Updates the user's phone number. Using the request <code>req</code> JSON,
we obtain the user's ID. Then we query the database using this ID to update a user's phone number.  



### Parameters

Parameter | Description
--------- | -----------
req | The request JSON. We obtain parameters <code>userID</code> from the request. 




##changeBeneficiary


Updates the user's beneficiary. Using the request <code>req</code> JSON,
we obtain the user's ID. Then we query the database using this ID to update a user's beneficiary.  


### Parameters

Parameter | Description
--------- | -----------
req | The request JSON. We obtain parameters <code>userID</code> from the request. 


 
## changeChecking


Updates the user's checking account information. Using the request <code>req</code> JSON,
we obtain the user's ID. Then we query the database using this ID to update a user's checking account information.  



### Parameters

Parameter | Description
--------- | -----------
req | The request JSON. We obtain parameters <code>userID</code> from the request. 


<aside class="notice">
The <code>change</code> methods make permanent updates to the database, until the special <code>resetDB</code> method is called (see later).
</aside>



## totalPremiumAndDueDate

Retrieve the user's total amount due across all bills, and their due dates. 
Using the request <code>req</code> JSON,
we obtain the user's ID. Then we query the database using this ID to obtain the user's premium information.



### Parameters

Parameter | Description
--------- | -----------
req | The request JSON. We obtain parameters <code>userID</code> from the request. 



## confirmBalance



```python 
def confirmBalance(req):
    parameters = req.get("result").get("parameters")
    userID = int(parameters.get("userID"))
    balance = getCheckingAccountBalance(userID)
    prems_and_dates = getAmountDueAndDuedates(userID)
    total = 0
    for i in range(0, len(prems_and_dates)):
        total += prems_and_dates[i][1]
    if balance >= total:
        speech = "Yes, your checking account balance is "+ str(balance)+ " and it is enough to pay your premium."
        speech += "would you like to go ahead with the payment?"
    else:
        speech = "No, your checking account balance is "+ str(balance)+ " and it is not enough to pay your premium."
    return {
        "speech": speech,
        "displayText": speech,
        # "data": data,
        # "contextOut": [],
        "source": "Nationwide-financial-assistant"
    }
```

Retrieve a user's balance. Used to determine if they have enough funds in account to pay off a given premium
Using the request <code>req</code> JSON,
we obtain the user's ID. Then we query the database using this ID to obtain the user's balance



### Parameters

Parameter | Description
--------- | -----------
req | The request JSON. We obtain parameters <code>userID</code> from the request. 




