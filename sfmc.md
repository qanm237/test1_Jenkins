# SFMC API description
This is an API which extract data from marketing cloud website and pushes it to bigquery

**There are two types of load types:**
* Historical load
* Incremental load

## HISTORICAL LOAD LOGIC:

Takes 3 parameters: 
* Load-type 
* Start-date
* Count

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


## Version: v1
