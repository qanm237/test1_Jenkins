# SFMC API description
This is an API which extract data from marketing cloud website and pushes it to bigquery

**There are 6 possible endpoints for this service. These endpoints are triggered through cloud sheduler HTTP post method. Below table describes endpoints:**

### /load_click_events

#### POST
##### Summary:

Method to retrieve data for click events. returns a json object with status whether the execution has been successful or failed 

##### Payload Sample:
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

##### Payload Sample:
```sh
{"load_type":"Historical","start_date":"2018-11-01","count":"5"}
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

##### Payload Sample:
```sh
{"load_type":"Incremental","start_date":"2019-07-11","count":"3"}
```
##### Responses

| Code | Description | Sample |
| ---- | ----------- | ----------- |
| 200 | Successful loading of sent events data to Bigquery | {'type':'sent_event', 'status': 'Success'} |
| 400 | Failure in  loading of sent events data to Bigquery | {'type':'sent_event', 'status': 'Fail', 'message': 'URL not found' |



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
