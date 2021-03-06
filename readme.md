aerogear-push-quickstart-backend
================================

This backend application is a showcase / quickstart part of Aerogear's Unified Push facilities.
This Application shows how a business specific App can interact with [aerogear-unified-push-server](https://github.com/aerogear/aerogear-unified-push-server) . IT shows how it can use the  [aerogear-unified-push-java-client](https://github.com/aerogear/aerogear-unified-push-java-client) to send Unified Messages to the push server.

# Description of the Application

Prodoctor is a company in the health care industry, selling a revolutionary tensiometer. Their clients are doctors. Prodoctor has several sales agents all over the United States. At the headquarters, they have their "first line" sales departement, doing cold calls all along the day. As soon they have a concrete lead, they use their Prodoctor Admin app to filter out available sales Agents available in the lead area. They can then send them a push notification.

The sales agent receives the notification on their mobile device that a new lead is available. The agent will handle the lead by "accepting" the notifcation, informing the other agents that the lead has been processed.

In this highly competitive market of the tensiometers, be able to process a lead directly is for sure a competitve advantage.

## The client app

1. The client consist of a list of leads : a lead can be "open" or "in process", leads "in process" of other sales are not visible.

* optional : when the client tap a lead it appears on  a map

2. The client has a status that he can set: STANDBY | WITH_CLIENT |PTO 
3. The client has a location
4. The client has an alias

## Prodoctor Admin client

1. The admin client can create a new lead :

* A lead consist of a name and a location

2. The admin client can query for Sales Agents based on :

* Status
* Location

3. The admin client can assign a lead to a selection (1..n) of sales agents, this will send out the nofitications.

4. The admin client manage the Sales Agents DB.

# Installation

## Prerequisites

## aerogear-unified-push-server

Be sure to have a running instance of a Push Server. Intructions can be found [here](https://github.com/aerogear/aerogear-unified-push-server).

## aerogear-unified-push-java-client 

Prodoctor uses the  [aerogear-unified-push-java-client](https://github.com/aerogear/aerogear-unified-push-java-client) library. This one is not yet released on the central maven repos, so you will have to clone the repo and install it ``` mvn clean install ```


## Native clients

TODO

### Available aliases
When registring the device on the Push Server make sure to use one of the existing aliases (or you should create a new user on the Prodoctor Admin Page) :

* john
* jake
* maria
* bob

For all these aliases the password is ``` 123 ```

# Setting the PushApplicationId and the Push Server URL

Before building and deploying the app be sure to change the values in ``` org.aerogear.prodoctor.service.LeadSender ``` :

* ``` pushApplicationId ``` : See intructions [here](https://github.com/aerogear/aerogear-unified-push-server#register-push-app) to obtain one.

* ``` serverURL ``` : enters here the server location of your Push Server (if you are using a local one you can keep the default value).


# Deploying the app

``` mvn clean install jboss-as:deploy ```

# Prodoctor Admin Application

Browse to ``` localhost:8080/prodoctor ```

You should log in with john/123 , he has admin rights and create leads and send notification.

## Creating a lead

Use the form to create a new Lead

## Send a lead to Sale Agents

* Select a lead from the lead's list
* If needed enter the 2 available criterias ``` status ``` and/or ``` location ``` (you can also search without criteria).
* Select the agents you want to send the lead to.
* Push the button ``` Send Lead ```

# Prodoctor API

The native clients can access the following REST based services :

## ``` POST /login ``` 

Login service, mandatory for all further request.

``` 
curl -v -b cookies.txt -c cookies.txt -H "Accept: application/json" -H "Content-type: application/json"
-X POST -d '{"loginName": "john", "password":"123"}' http://localhost:8080/prodoctor/login 
```

It will return the user :

```
 {"id":"cb3c05aa-3fdd-4b4e-9b90-386fc7e5671a","enabled":true,"createdDate":1371215851063,"expirationDate":null,"partition":null,"loginName":"john","firstName":null,"lastName":null,"email":null,"status":"PTO","password":"123","location":"New York"}
```

## ``` POST /logout ``` 

Logout service.

``` 
curl -v -b cookies.txt -c cookies.txt -H "Accept: application/json" -H "Content-type: application/json"
-X POST -d '{"loginName": "john", "password":"123"}' http://localhost:8080/prodoctor/logout 
```

returns no data

## ``` GET  /leads ```

Obtain a list of leads.

```
 curl -v -b cookies.txt -c cookies.txt -H "Accept: application/json" -H "Content-type: application/json" 
-X GET http://localhost:8080/prodoctor/leads 
```

You will get a list of leads :

```
[{"id":39,"version":0,"name":"Doctor No","location":"New York","phoneNumber":"0612412121"}]
```

## ``` PUT /saleagents/{id} ```

Update a SaleAgent, the service will only update the status and the location for now.

```
 curl -v -b cookies.txt -c cookies.txt -H "Accept: application/json" -H "Content-type: application/json" -X PUT -d '{"id":"13bbaea3-9271-43f7-80aa-fb21360ff684","enabled":true,"createdDate":1371213256827,"expirationDate":null,"partition":null,"loginName":"john","firstName":null,"lastName":null,"email":null,"version":0,"status":"CHANGED","password":"123","location":"New York"}' http://localhost:8080/prodoctor/saleagents/13bbaea3-9271-43f7-80aa-fb21360ff684
```

returns no data

## ``` PUT /leads/{id} ```

Update a Lead, typically used if a Sale Agent wants to assign a lead to him.

```
curl -v -b cookies.txt -c cookies.txt -H "Accept: application/json" -H "Content-type: application/json" -X PUT -d '{"id":39,"version":0,"name":"Doctor No","location":"New York","phoneNumber":"121212121","saleAgent":"13bbaea3-9271-43f7-80aa-fb21360ff684"}' http://localhost:8080/prodoctor/leads/39
```

returns no data
