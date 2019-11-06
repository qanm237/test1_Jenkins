# SFMC API description
This is an API which extract data from marketing cloud website and pushes it to bigquery

**There are 6 possible endpoints for this service. These endpoints are triggered through cloud sheduler HTTP post method.Payload consists of :**

* load_type :This is the type of load. It can be "Incremental" or "historical"
* start_date :start date for which you want to pull data.
* count :variable used to prevent the deletion of the tables in bigquery due to parallel processing. it has two values:
  * 1 : which signifies that the table should be deleted and created again and updated
  * 2 : which signifies that the data should be apended in already exsisting table



**Endpoints**

### /load_click_events

#### POST
##### Summary:

Method to retrieve data for click events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
{"load_type":"Incremental","start_date":"2019-01-01","count":"2"}
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of click events data to Bigquery | {'type':'click_event', 'status': 'Success'} |
| 400 | Failure in  loading of click events data to Bigquery | {'type':'click_event', 'status': 'Fail', 'message': 'URL not found' |

### /load_bounce_events

#### POST
##### Summary:

Method to retrieve data for bounce events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
{"load_type":"Historical","start_date":"2018-11-01","count":"2"}
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of bounce events data to Bigquery | {'type':'bounce_event', 'status': 'Success'} |
| 400 | Failure in  loading of bounce events data to Bigquery | {'type':'bounce_event', 'status': 'Fail', 'message': 'URL not found' |

### /load_sent_events

#### POST
##### Summary:

Method to retrieve data for sent events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
{"load_type":"Incremental","start_date":"2019-07-11","count":"2"}
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of sent events data to Bigquery | {'type':'sent_event', 'status': 'Success'} |
| 400 | Failure in  loading of sent events data to Bigquery | {'type':'sent_event', 'status': 'Fail', 'message': 'URL not found' |

### /load_open_events

#### POST
##### Summary:

Method to retrieve data for open events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
{"load_type":"Incremental","start_date":"2019-01-01","count":"2"}
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of open events data to Bigquery | {'type':'open_event', 'status': 'Success'} |
| 400 | Failure in  loading of open events data to Bigquery | {'type':'open_event', 'status': 'Fail', 'message': 'URL not found' |

### /load_unsub_events

#### POST
##### Summary:

Method to retrieve data for unsub events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
{"load_type":"Incremental","start_date":"2019-01-01","count":"2"}
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of unsub events data to Bigquery | {'type':'unsub_event', 'status': 'Success'} |
| 400 | Failure in  loading of unsub events data to Bigquery | {'type':'unsub_event', 'status': 'Fail', 'message': 'URL not found' |

### /load_notsent_events

#### POST
##### Summary:

Method to retrieve data for notsent events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
{"load_type":"Incremental","start_date":"2019-01-01","count":"2"}
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of notsent events data to Bigquery | {'type':'NotSentevent', 'status': 'Success'} |
| 400 | Failure in  loading of notsent events data to Bigquery | {'type':'NotSentevent', 'status': 'Fail', 'message': 'URL not found' |

### /load_subscriber_events

#### POST
##### Summary:

Method to retrieve data for subscriber events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
{"load_type":"Incremental","start_date":"2019-01-01","count":"2"}
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of Subscriber events data to Bigquery | {'type':'Subscriber', 'status': 'Success'} |
| 400 | Failure in  loading of Subscriber events data to Bigquery | {'type':'Subscriber', 'status': 'Fail', 'message': 'URL not found' |

### /load_journey_events

#### POST
##### Summary:

Method to retrieve data for journey events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
No Payload
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of journey events data to Bigquery | {'type':'journeyevent', 'status': 'Success'} |
| 400 | Failure in  loading of journey events data to Bigquery | {'type':'journeyevent', 'status': 'Fail', 'message': 'URL not found' |

### /load_data_extension

#### POST
##### Summary:

Method to retrieve data for data_extension events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
No Payload
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of data_extension events data to Bigquery | {'type':'Data Extension Object', 'status': 'Success'} |
| 400 | Failure in  loading of data_extension events data to Bigquery | {'type':'Data Extension Object', 'status': 'Fail', 'message': 'URL not found' |


### /load_data_extension_field

#### POST
##### Summary:

Method to retrieve data for data_extension_field events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
No Payload
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of data_extension_field events data to Bigquery | {'type':'data extension field', 'status': 'Success'} |
| 400 | Failure in  loading of data_extension_field events data to Bigquery | {'type':'data extension field', 'status': 'Fail', 'message': 'URL not found' |


### /load_email_send_definition

#### POST
##### Summary:

Method to retrieve data for email_send_definition events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
No Payload
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of email_send_definition events data to Bigquery | {'type':'email send definition', 'status': 'Success'} |
| 400 | Failure in  loading of email_send_definition events data to Bigquery | {'type':'email send definition', 'status': 'Fail', 'message': 'URL not found' |

### /load_linksend_event

#### POST
##### Summary:

Method to retrieve data for linksend_event events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
No Payload
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of linksend events data to Bigquery | {'type':'linksend', 'status': 'Success'} |
| 400 | Failure in  loading of linksend events data to Bigquery | {'type':'linksend', 'status': 'Fail', 'message': 'URL not found'|


### /create_task_queue_historical

#### POST
##### Summary:

Method to create task queue for running historical for events in parallel
:return: Json object with status whether the execution has been successful or failed
Object_type should be similar to object name written in sfmc marketing cloud api website
load_type should be historical
start date should be in format YYYY-MM-DD
##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
{"obj_name":"click_event","load_type":"Historical","start_date":"2016-12-01","count":"2"}
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of linksend events data to Bigquery | {'type':'create_task_queue_historical', 'status': 'Success', "method": "post", "load_type": "Historical","object_type":"click_events","start_date":"2016-12-01"} |
| 400 | Failure in  loading of linksend events data to Bigquery | {'type':'create_task_queue_historical', 'status': 'Success', "method": "post", "load_type": "Historical","object_type":"click_events","start_date":"2016-12-01", "message":"URL not found"} |

> This method is currently not in use and is depreceated. For Historical load endpoints for respective events are used by passing load_type as    "Historical" and start date as "2016-12-01"




### /load_send

#### POST
##### Summary:

Method to retrieve data for sends. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
{"load_type":"Incremental"}
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of send data to Bigquery | {'type':'send', 'status': 'Success'} |
| 400 | Failure in  loading of send data to Bigquery | {'type':'send', 'status': 'Fail', 'message': 'URL not found' |

### /load_triggeredsenddefinitions

#### GET
##### Summary:

Method to retrieve data for triggeredsenddefinitions. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
No Payload
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of triggeredsenddefinitions data to Bigquery | {'type':'triggeredsenddefinitions', 'status': 'Success'} |
| 400 | Failure in  loading of triggeredsenddefinitions data to Bigquery | {'type':'triggeredsenddefinitions', 'status': 'Fail', 'message': 'URL not found' |


### /load_triggeredsendsummary

#### GET
##### Summary:

Method to retrieve data for triggeredsendsummary. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
No Payload
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of triggeredsendsummary data to Bigquery | {'type':'triggeredsendsummary', 'status': 'Success'} |
| 400 | Failure in  loading of triggeredsendsummary data to Bigquery | {'type':'triggeredsendsummary', 'status': 'Fail', 'message': 'URL not found' |




| Endoint | Description | Payload from scheduler |
| ------ | ------ | ------ |
| load_click_events | Method to retrieve data for click events | load_type, start_date, count |
| load_bounce_events | Method to retrieve data for bounce event | load_type, start_date, count | 
| load_triggeredsenddefinitions | Method to retrieve data for triggeredsend definitions | No payload |
| load_sent_events | Method to retrieve data for sent events | load_type, start_date, count |
| load_send | Method to retrieve data for sends | load_type |
| load_triggeredsendsummary |  Method to retrieve data for triggeredsendsummary | No payload |

Sample Payload(click events):
```sh
{"load_type":"Incremental","start_date":"2019-01-01","count":"2"}
```
All these endpoints returns a json object with status whether the execution has been successful or failed 

 
## HISTORICAL LOAD LOGIC:

**Process:**
* First the start date is taken and an end date is calculated by adding 3 months into it
* Then a token is generated which has validity for 20mins and is used for authentication while calling a SOAP API
* Then the SOAP API is called and it returns the data for a single month.
  This  process is repeated 3 times to get data for 3 months and this entire process again for 3 times for 3 different account ids   “3 * account IDS”
* Response obtained from SOAP API only fetches 2500 records at a time and follows a paging concept which is indicated in response as “more data available”
  Request-id obtained in the response is passed to move to the next page and fetch further records
* If PII(personal info like email etc) is present then it is passed into DLP and then stored into  GCS and which doesn’t consist of PII is passed to Custom
* Next the file data from GCS is send to the bigquery in staging tables where the duplicate  records are deleted and then send to the main table which stores the metadata like Account id, last updated date,current_rows,previous_rows, last_run etc

**Template being send while using historical load:**
* Header : token for authentication
* Body : filter for variable start and end dates, property tags used to define the required fields while fetching data 
* Footer : footer closes the parent xml

**Sample :**
```
  <Envelope xmlns="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><Header><fueloauth>{{access_token}}</fueloauth></Header><Body><RetrieveRequestMsg xmlns="http://exacttarget.com/wsdl/partnerAPI"><RetrieveRequest><ObjectType>ClickEvent</ObjectType><Properties>SubscriberKey</Properties><Properties>Client.ID</Properties><Properties>BatchID</Properties><Filter xsi:type="SimpleFilterPart"> <Property>EventDate</Property>
  <SimpleOperator>between</SimpleOperator><DateValue>2016-12-01T00:00:00</DateValue> <DateValue>2016-12-31T00:00:00</DateValue></Filter></RetrieveRequest></RetrieveRequestMsg></Body></Envelope>

```

## INCREMENTAL LOAD LOGIC :
* Get the latest date using metadata table and calculate the date of week before
* Then send the SOAP request for the date greater than the date for last week. Then same as that of historical load

**TEMPLATE SAMPLE FOR INCREMENTAL LOAD:**

```
  <Envelope xmlns="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><Header><fueloauth>{{access_token}}</fueloauth></Header><Body><RetrieveRequestMsg xmlns="http://exacttarget.com/wsdl/partnerAPI"><RetrieveRequest><ObjectType>ClickEvent</ObjectType><Properties>SubscriberKey</Properties><Properties>Client.ID</Properties><Properties>BatchID</Properties><Filter xsi:type="SimpleFilterPart"> <Property>EventDate</Property>
  <SimpleOperator>greaterthan</SimpleOperator><DateValue>2016-12-01T00:0</DateValue></Filter></RetrieveRequest></RetrieveRequestMsg></Body></Envelope>

```

## Response from SOAP request
Here's the sample response of clickevents:
```
{"MID": "754747332", "PartnerKey": "", "SendID": "74732", "SubscriberKey": "ghchjgwdvjxv", "EventDate": "2019-01-04T09:20:08", "EventType": "Click", "TriggeredSendDefinitionObjectID": "", "BatchID": "1", "URLID": "8787263", "URL": "https://3jrfdkgjvkxhwvxhwdxvmwxmwdbxbcvwbc,"}

```


## Version: v1
