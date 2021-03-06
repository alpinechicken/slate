---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the **bloc** API. You can use this to interact with an exchange which will improve your life and maybe save the universe.

Language bindings are in Python, JavaScript, and shell. You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

A version of the server is running on [blocmarket](https://blocmarket.heroku.com/). Go break things there. The [github](https://github.com/alpinechicken/blocmarket/) has more demonstrations and examples.

# Authentication

Authentication is handled through signatures on markets and trades. The server will only accept correctly signed markets and orders using a registered key. There is no need to authenticate.

<aside class="notice">
Signatures are obtained through the <code>createUser</code> endpoint.
</aside>

# Creators

Three things can be created: users, markets, and trades. Adjusting/settling markets or removing trades uses the same functions.

## Create user


```python

import requests
import json
url = 'https://blocmarket.herokuapp.com/createUser'
headers = {'content-type': 'application/json'}
content = {}
response = requests.post(url, data=json.dumps(content), headers=headers)

```
```javascript

var data = {};
var options = {
  'method' : 'post',
  'contentType': 'application/json',
  // Convert the JavaScript object to a JSON string.
  'payload' : JSON.stringify(data),
};

  var response = UrlFetchApp.fetch('https://blocmarket.herokuapp.com/createUser', options);
  var w = JSON.parse(response.getContentText());
```


```shell
curl --header "Content-Type: application/json" -X POST http://blocmarket.herokuapp.com/createUser

```

> The above command returns JSON structured like this:

```json

  {'signingKey': 'ece2efc138c8298d43caba1315ceda614e20644c74d46fed37871c47ea19afdf',
 'traderId': '1',
 'verifyKey': '9690a2e12971ae452d68bf3d08405090d45791533cf80740fd186aea4b6773fc'}

```

This endpoint creates a new user and registers the `verifyKey` with the server.

### HTTP Request

`POST https://blocmarket.herokuapp.com/createUser`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
None | none | none


<aside class="success">
The verify key is registered with the server so messages can be verified as belonging to that trader. The signing key is not stored.
</aside>

<aside class="warning">
The signing key is not stored on the server. It cannot be recovered.
</aside>

## Create market

> Create a new market change bounds on an existing market:

```python

import requests
import json
sk = 'ece2efc138c8298d43caba1315ceda614e20644c74d46fed37871c47ea19afdf'
vk = '9690a2e12971ae452d68bf3d08405090d45791533cf80740fd186aea4b6773fc'
tId = 2

url = 'https://blocmarket.herokuapp.com/createMarket'
headers = {'content-type': 'application/json'}
content_makemarket = {"signingKey": sk,
                    "traderId": tId, 
                    "verifyKey": vk,
                    "marketRootId": 1, 
                    "marketBranchId": 1, 
                    "marketMin": 0,
                    "marketMax":10000}
response = requests.post(url, data=json.dumps(content), headers=headers)

```

```javascript

var signingKey = 'ece2efc138c8298d43caba1315ceda614e20644c74d46fed37871c47ea19afdf';
var verifyKey = '9690a2e12971ae452d68bf3d08405090d45791533cf80740fd186aea4b6773fc';
var traderId = 2
var data = {"signingKey": signingKey,
                    "traderId": traderId, 
                    "verifyKey": verifyKey,
                    "marketRootId": 1, 
                    "marketBranchId": 1, 
                    "marketMin": 0,
                    "marketMax":10000};
var options = {
  'method' : 'post',
  'contentType': 'application/json',
  // Convert the JavaScript object to a JSON string.
  'payload' : JSON.stringify(data);
};
  var response = UrlFetchApp.fetch('https://blocmarket.herokuapp.com/createMarket', options);
  var w = JSON.parse(response.getContentText());

```


```shell

curl --data '{"signingKey": "ece2efc138c8298d43caba1315ceda614e20644c74d46fed37871c47ea19afdf",
"traderId": 1, 
"verifyKey": "9690a2e12971ae452d68bf3d08405090d45791533cf80740fd186aea4b6773fc",
"marketRootId": 1, 
"marketBranchId": 5, 
"marketMin": 0, 
"marketMax": 1}' 
--header "Content-Type: application/json" -X POST http://blocmarket.herokuapp.com/createMarket

```


> The above command returns JSON structured like this:

```json

{'allChecks': "{'inputChk': True, 'marketLimitChk': False, 
    'traderIdChk': True, 'marketId': '1', 
    'marketRangeChk': True, 'marketIndChk': True, 
    'sigChk': True, 'chainChk': True, 
    'ownerChk': True, 'timeChk': True}”,
 'checks': 'False',
 'marketBranchId': 1,
 'marketId': 1,
 'marketMax': 0,
 'marketMin': 10000,
 'marketRootId': 1,
 'traderId': 2}

```

This creates a new market if one does not exist with this `marketRootId`/`marketBranchId` combination. If the market exists, this will update the maximum/minimum within the existing bounds.


### HTTP Request

`POST https://blocmarket.herokuapp.com/createMarket`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
`signingKey` | none | Private key
`verifyKey` | none | Pubic key
`traderId` | none | Trader Id
`marketRootId` | none | Market root Id
`marketBranchId` | none | Market branch Id
`marketMin` | none | Minimum settlement value
`marketMax` | none | Maximum settlement value


## Create trade

> Create a new trade (open order)

```python

sk = '0cca0a3739eba76cc78823d9f6e898379014d8c53172f5e45c171a3c54a9f477'
vk = 'cdcfb59431b2579a681cee65656cbed6f8d578d5cc30d3e759e86c1d3e3529ef'
tId = 2

url = 'https://blocmarket.herokuapp.com/createTrade'
headers = {'content-type': 'application/json'}
content_maketrade = {"signingKey": sk,
                     "traderId": int(tId),
                     "verifyKey": vk,
                     "marketId": mkId,
                     "price": 5500,
                     "quantity":1}
response = requests.post(url, data=json.dumps(content_maketrade), headers=headers)
```

```javascript

var signingKey = '0cca0a3739eba76cc78823d9f6e898379014d8c53172f5e45c171a3c54a9f477'
var verifyKey = 'cdcfb59431b2579a681cee65656cbed6f8d578d5cc30d3e759e86c1d3e3529ef'
var traderId = 2

var data = {"signingKey": signingKey,
                     "traderId": traderId,
                     "verifyKey": verifyKey,
                     "marketId": 1,
                     "price": 5500,
                     "quantity":1};
var options = {
  'method' : 'post',
  'contentType': 'application/json',
  // Convert the JavaScript object to a JSON string.
  'payload' : JSON.stringify(data)
};
  var response = UrlFetchApp.fetch('https://blocmarket.herokuapp.com/createTrade', options);
  var w = JSON.parse(response.getContentText());
```


```shell
curl --data '{"signingKey": "ece2efc138c8298d43caba1315ceda614e20644c74d46fed37871c47ea19afdf",
"traderId": 1, 
"verifyKey": "9690a2e12971ae452d68bf3d08405090d45791533cf80740fd186aea4b6773fc",
"marketRootId": 1, 
"marketBranchId": 5, 
"marketMin": 0, 
"marketMax": 1}' 
--header "Content-Type: application/json" -X POST http://blocmarket.herokuapp.com/createMarket


```


> The above command returns JSON structured like this:

```json

{

'allChecks': "{'inputChk': True, 'traderIdChk': True, 
'marketChk': True, 'sigChk': True, 
'chainChk': True, 'timeChk': True, 
'colChk': True}",
 'marketId': 1,
 'price': 5500,
 'quantity': 1,
 'traderId': 1}

```

Signed trade is added the signature is valid



### HTTP Request

`POST https://blocmarket.herokuapp.com/createTrade`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
`signingKey` | none | Private signature key
`verifyKey` | none | Pubic signature key
`traderId` | none | Trader Id
`marketId` | none | Market Id
`price` | none | price
`quantity` | none | quantity


<aside class="success">
The verify key is saved so messages from that trader can be verified. The signing key is not stored.
</aside>

# Views

Views return market and order book information. 


## View market bounds

View all markets with current maximum/minimum values.

```python
import json
import requests
url = 'https://blocmarket.herokuapp.com/viewMarketBounds'
headers = {'content-type': 'application/json'}
content = {}
response = requests.post(url, data=json.dumps(content), headers=headers, stream=True)

```

```javascript
var data = {};

var options = {
  'method' : 'post',
  'contentType': 'application/json',
  'payload' : JSON.stringify(data),
};

  var response = UrlFetchApp.fetch('https://blocmarket.herokuapp.com/viewMarketBounds', options);
  var json = JSON.parse(JSON.parse(response.getContentText() ));
```

```shell
curl --data {} --header "Content-Type: application/json" -X POST http://blocmarket.herokuapp.com/viewMarketBounds --output mb.txt
```


> The above command returns JSON structured like this:

```json

'{"marketId":{"0":1,...},
"marketRootId":{"0":1,...},
"marketBranchId":{"0":1,...},
"marketMin":{"0":0.0,...},
"marketMax":{"0":10000.0,...},
"marketDesc":{"0":"Market root : 3, branch: 1",...}}'


```



### HTTP Request

`POST https://blocmarket.herokuapp.com/viewMarketBounds`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
          |         |

<aside class="info">
This takes into account the whole market table. Settled markets have the same marketMin and marketMax.
</aside>


## View order book



```python
import json
import requests
url = 'https://blocmarket.herokuapp.com/viewOrderBook'
headers = {'content-type': 'application/json'}
content = {'marketId': 1}
response = requests.post(url, data=json.dumps(content), headers=headers, stream=True)

```

```javascript
var data = {"marketId": 1};

var options = {
  'method' : 'post',
  'contentType': 'application/json',
  'payload' : JSON.stringify(data),
};

  var response = UrlFetchApp.fetch('https://blocmarket.herokuapp.com/viewOrderBook', options);
  var json = JSON.parse(JSON.parse(response.getContentText() ));
```

```shell
curl --data {"marketId": 1} --header "Content-Type: application/json" -X POST http://blocmarket.herokuapp.com/viewOrderBook --output ob.txt
```


> The above command returns JSON structured like this:

```json

  '{"marketId":{"1":1...}
    "price": {"1":5000...}
    "quantity": {"1":1...}
    "traderId": {"1":1...}
    "timeStampUTC = {"1": 1548760422106 ...}

```

This endpoint creates a new user and registers the `verifyKey` with the server.



### HTTP Request

`POST https://blocmarket.herokuapp.com/viewOrderBook`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
marketId | none | Market Id


<aside class="warning">
This will return the unedited order book which will probably be very big and should only be used for broadcasting or checking logic.
</aside>

<aside class="warning">
JSON returned to JavaScript must be parsed twice because JavaScript doesn't understand the json version of pandas dataframes unless you tell it twice.
</aside>


## View open trades

View unmatched trades.

```python
import json
import requests
url = 'https://blocmarket.herokuapp.com/viewOpenTrades'
headers = {'content-type': 'application/json'}
content = {'marketId': 1}
response = requests.post(url, data=json.dumps(content), headers=headers, stream=True)

```

```javascript

var data = {"marketId": 1};
var options = {
  'method' : 'post',
  'contentType': 'application/json',
  'payload' : JSON.stringify(data),
};

  var response = UrlFetchApp.fetch('https://blocmarket.herokuapp.com/viewOpenTrades', options);
  var json = JSON.parse(JSON.parse(response.getContentText()));

```

```shell
curl --data {"marketId": 1} --header "Content-Type: application/json" -X POST http://blocmarket.herokuapp.com/viewOpenTrades --output ob.txt
```


> The above command returns JSON structured like this:

```json

  '{"marketId":{"1":1...}
    "quantity": {"1":1...}
    "traderId": {"1":1...}
    "timeStampUTC = {"1": 1548760422106 }


```

This endpoint returns all open trades.

### HTTP Request

`POST https://blocmarket.herokuapp.com/viewOpenTrades`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
marketId | none | Market Id


<aside class="success">
The UTC timestamp is the number of thousandths of a second since Jan 1 1970.
</aside>



## View matched trades


```python
import json
import requests
url = 'https://blocmarket.herokuapp.com/viewMatchedTrades'
content = {'marketId': 1}
headers = {'content-type': 'application/json'}
response = requests.post(url, data=json.dumps(content), headers=headers, stream=True)

```

```javascript

var data = {"marketId": 1};
var options = {
  'method' : 'post',
  'contentType': 'application/json',
  'payload' : JSON.stringify(data),
};

  var response = UrlFetchApp.fetch('https://blocmarket.herokuapp.com/viewMatchedTrades', options);
  var json = JSON.parse(JSON.parse(response.getContentText() ));
```


```shell
curl --data {"marketId": 1} --header "Content-Type: application/json" -X POST http://blocmarket.herokuapp.com/viewMatchedTrades --output ob.txt
```


> The above command returns JSON structured like this:

```json

  '{"marketId":{"1":1...}
    "price": {"1":5000...}
    "quantity": {"1":1...}
    "traderId": {"1":1...}
    "timeStampUTC = {"1": marketId | none | Market Id }


```

This endpoint returns all matched trades.

### HTTP Request

`POST https://blocmarket.herokuapp.com/viewMatchedTrades`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
marketId | none | Market Id


<aside class="success">
The UTC timestamp is the number of thousandths of a second since 1 Jan 1970.
</aside>

## View tick history

Pulls BID/ASK/TRADE data from a particular market, excluding own crossing trades. Time is given as a UTC timestamp.

```python
import json
import requests
import time
url = 'https://blocmarket.herokuapp.com/viewTickHistory'
headers = {'content-type': 'application/json'}
content = {'marketId': 1, 'startTime': time.time() - 4*60*60*24, 'endTime': time.time() }
response = requests.post(url, data=json.dumps(content), headers=headers, stream=True)

```

```javascript

var data = {"marketId": 1, 'startTime': 1548760422106 , 'endTime': 1548770422106};
var options = {
  'method' : 'post',
  'contentType': 'application/json',
  'payload' : JSON.stringify(data),
};

  var response = UrlFetchApp.fetch('https://blocmarket.herokuapp.com/viewTickHistory', options);
  var json = JSON.parse(JSON.parse(response.getContentText() ));
```


```shell
curl --data {"marketId": 1, 'startTime': 1548760422106 , 'endTime': 1548770422106} --header "Content-Type: application/json" -X POST http://blocmarket.herokuapp.com/viewTickHistory --output ob.txt
```


> The above command returns JSON structured like this:

```json

  '{"tickType":{"1":"BUY"...}
    "tradeId":{"1": 11087}
    "xTradeId": {"1": 11097}
    "marketId": {"1": 2}
    "price": {"1":5000...}
    "quantity": {"1":1...}
    "traderId": {"1":1...}
    "iMatched" {"1": True}
    "timeStampUTC = {"1": 1548760422106 }


```

This endpoint returns all market BID/ASK/TRADE ticks in the time range. The *xTradeId* field is the crossing trade (if trade is matched).

<aside class="info">
Crossing trades are guessed by looking at all trades between *startTime* and *endTime*. If you need to be sure, get the whole order book with **viewOrderBook()**.
</aside>

### HTTP Request

`POST https://blocmarket.herokuapp.com/viewTickHistory`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
marketId | none | Market Id
startTime | none | Start time for ticks (unix time)
endTime | none | End time for ticks (unix time)

<aside class="success">
The UTC timestamp is the number of thousandths of a second since 1 Jan 1970.
</aside>




## View trade summary


```python
import json
import requests
url = 'https://blocmarket.herokuapp.com/viewTradeSummary'
headers = {'content-type': 'application/json'}
content = {'traderId': 2}
response = requests.post(url, data=json.dumps(content), headers=headers, stream=True)

```

```javascript
var data = {"traderId": 2};
var options = {
  'method' : 'post',
  'contentType': 'application/json',
  'payload' : JSON.stringify(data),
};

  var response = UrlFetchApp.fetch('https://blocmarket.herokuapp.com/viewTradeSummary', options);
  var json = JSON.parse(JSON.parse(response.getContentText() ));

```


```shell
curl --data {"traderId": 2} --header "Content-Type: application/json" -X POST http://blocmarket.herokuapp.com/viewTradeSummary --output ts.txt
```


> The above command returns JSON structured like this:

```json

  '{"marketId":{"1":1...}
    "price": {"1":5000...}
    "quantity": {"1":1...}
    "traderId": {"1":1...}
    "iMatched": {"1": True...}
    "timeStampUTC = {"1": 1548760422106 }
    "marketMin": {"1":1000...}
    "marketMax": {"1":9000...}
    "marketMinOutcome": {"1", -4500 ...}
    "marketMaxOutcome": {"1", 3500 ...}}'


```

This endpoint returns all trades for a particular user and the payoff for the trade at the maximum and minimum value for the market.




### HTTP Request

`POST https://blocmarket.herokuapp.com/viewTradeSummary `

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
traderId | none | Trader Id


<aside class="success">
The UTC timestamp is the number of thousandths of a second since 1 Jan 1970.
</aside>



