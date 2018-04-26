# RestAPI

Our REST API is a web-based API using a Websocket connection and was developed with algorithmic trading in mind. 

Developers and investors can create custom trading applications, integrate into our platform, back test strategies and build robot trading. Calls can be made in any language that supports a standard HTTP. 

We utilize the new OAuth 2.0 specification for authentication via token. This allows for a more secure authorization to access your application and can easily be integrated with web applications, mobile devices, and desktop platforms

With the use of the socket.io library, the API has streaming capability and will push data notifications in a JSON format. Your application will have access to our real-time streaming market data, subscribe in real time access to trading tables and place live trades.

## How to start:
1.	A FXCM account. You can apply for a demo account [here](https://www.fxcm.com/uk/algorithmic-trading/api-trading/) 
2.	A persistent access token. You can generate one from the <a href="https://tradingstation.fxcm.com/">Trading Station web</a>. Click on User Account > Token Management on the upper right hand of the website. For Live account, please send your username to api@fxcm.com, we will need to enable Rest API access. For demo account, Rest API access was enabled by default.
3.	Download Rest API word documents at <a href="https://apiwiki.fxcorporate.com/api/RestAPI/Socket%20REST%20API%20Specs.pdf">here</a>
4. Documents in Swagger format at <a href="https://fxcm.github.io/rest-api-docs/#">here</a> 
5. Start coding.  You will need to reference the <a href="https://socket.io/docs/client-api/">socket.io library</a> in your code. 
   a.	Using Javascript, click <a href="https://www.npmjs.com/package/socket.io">here</a>
   b.	 Using Python, click <a href="https://pypi.python.org/pypi/socketIO-client">here</a>
6. Sample code for Python at <a href="https://github.com/fxcm/fxcm-api-rest-python3-example">here</a> 
7. Sample code for Java Script at <a href="https://github.com/fxcm/fxcm-api-rest-nodejs-example">here</a>
8. Sample code in Netbeans Maven project at [here](https://apiwiki.fxcorporate.com/api/RestAPI/JavaRestClient.zip)

## How to connect:
Clients should establish a persistent WebSocket connection using socket.io library. All non-solicited updates will be sent over this connection. Client requests are to be sent via normal HTTP messages. Every HTTP message must contain following parameters

| Header | Description | Values | Req’d |
| --- | --- |--- |--- |
| HTTP version | Version of HTTP used | HTTP/1.1 | Y |
| User-Agent | Identification of the client software | xxxxxx_software | Y |
| Accept | Acceptable response MIME type | application/json | Y |
| Content-Type | Media type of the request | application/x-www-form-urlencoded | Y |
| Authorization | Authorization string containing “Bearer “, ID of socket.io connection and persistent token| 'Bearer ' + socket_id + api_token | Y |

      Sample Request
      GET /socket.io/?access_token=cj5wedhq3007v61fe935ihqed&EIO=3&transport=polling&t=Lsd_lZY&b64=1 
      HTTP/1.1 
      User-Agent: node-XMLHttpRequest 
      Accept: */* 
      Host: api.fxcm.com 
      Connection: close

## What 't' means
"t" is the table id: 

| t | table |
| --- | --- |
| 1 | Open Position |
| 2 | Closed Position |
| 3 | Order |
| 5 | Summary |
| 6 | Account |

## Subscribe vs snapshot:
FXCM Rest API provides two ways to deliever data. susbcribe vs snapshot.

After susbcribe, data will be pushed to your socket whenever there is an update. You can susbcribe Market data stream /susbcribe or live table update /trading/susbcribe. You can also unsubscribe.
You can request a snapshot of trading tables via /trading/get_model. 

      Model choices: 'Offer', 'OpenPosition', 'ClosedPosition', 'Order', 'Summary', 'LeverageProfile', 'Account', 'Properties'.   

## OrderID vs TradeID:
OrderID and TradeID are different.
In Market order, an order id is created straightaway and it is in callback immediately. 

      {"response":{"executed":true},"data":{"type":0,"orderId":81712802}}

A trade id is not generated until after order is executed. You have to subscribe the order table and listing the live update and look up the trade id. You will not get trade id in snapshot, because that information was gone when you submit the request. 

      Examples:
      Subscribing for Orders table:
      POST /trading/subscribe
      models=Order
      
      Placing Market order:
      POST /trading/open_trade
      account_id=1537581&symbol=EUR%2FUSD&is_buy=false&rate=0&amount=5&order_type=AtMarket&time_in_force=GTC

      Response from server:
      {"executed":true}{"type":0,"orderId":390285837}

      Received Order record from /trading/subscribe with order_id and trade_id:
      {"t":3,"ratePrecision":5,"orderId":"390285837","tradeId":"170162801","time":"04252018120716391","accountName":"01537581","accountId":"1537581","timeInForce":"GTC","expireDate":"","currency":"EUR/USD","isBuy":false,"buy":0,"sell":1.21818,"type":"OM","status":2,"amountK":5,"currencyPoint":0.5,"stopMove":0,"stop":0,"stopRate":0,"limit":0,"limitRate":0,"isEntryOrder":false,"ocoBulkId":0,"isNetQuantity":false,"isLimitOrder":false,"isStopOrder":false,"isELSOrder":false,"stopPegBaseType":-1,"limitPegBaseType":-1,"range":0,"action":"I"}


Furthermore, a single market order can have many TradeIDs, if they are partial fills or closing of other orders. in this case, its more approriate to provide the OrderID which ties back to that spcific market order request, from there you can join this OrderID to any associated.

In entry order, an order ID is in callback function. You can also see it on order table sanpshot. but you will not get TradeID until order been executed. 


## Real Case Study:

1. Learn how to run BT backtest on FXCM historical data via RestAPI at <a href="https://apiwiki.fxcorporate.com/api/StrategyRealCaseStudy/RestAPI/BT strategy on FXCM data.zip">here</a>. 
What is <a href="http://pmorissette.github.io/bt/">bt?</a> 
2. Learn how to run QSTrader on FXCM data via RestAPI at <a href="https://apiwiki.fxcorporate.com/api/StrategyRealCaseStudy/RestAPI/QSTrader on FXCM data.zip">here</a>. 
what is <a href="https://www.quantstart.com/qstrader">QSTrader?</a>
3. Building/back testing RSI strategy via RestAPI at <a href="https://apiwiki.fxcorporate.com/api/StrategyRealCaseStudy/RestAPI/RsiStrategy.zip">here</a>.
4. Building/back testing Moving Average Crossover strategy via RestAPI at [Here](https://apiwiki.fxcorporate.com/api/StrategyRealCaseStudy/RestAPI/Moving_Average_Crossover_Strategy.zip)
5. One video demonstrate how to backtest strategies in Visual Studio via FXCM data On QuantConnect LEAN platform at <a href="https://www.youtube.com/watch?v=m6llfznP4d4">here</a>

## Note:
o	This is for personal use and abides by our [EULA](https://www.fxcm.com/uk/forms/eula/)

o	For more information, you may contact us: api@fxcm.com

