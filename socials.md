# Socials API description
This is an API which extract data from socials website and pushes it to bigquery. currently we are fetching data from two social sites
* Linkedin
* Facebook

**There are 2 possible endpoints for this service. These endpoints are triggered through cloud sheduler HTTP post method. Payload consists of :**

* service name i.e linkedin or facebook
* history: this defines the type of load(historical or incremental). It is true for historical and false for incremental  

**Endpoint for linkedin**

## /linkedindata

#### POST
### Summary:

This is an API which extract data from api.linkedin.com website and pushes it to bigquery
##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
{"medianame": "linkedin", "history": "False"}
```
#### Responses

| Code | Description | Sample respose in BigQuery notification table |
| ---- | ----------- | ----------- |
| 200 | Successful loading of linkedin data to Bigquery | {'JobID': 167312,'Source': 'Facebook','Subject': 'TA App Job success','Message':      'Facebook data loaded successfully for date: 1/2/2019 Successfully inserted 10 rows in Bigquery table xyz'} |
| 400 | Failure in  loading of linkedin data to Bigquery | {'JobID': 167312,'Source': 'Facebook','Subject': 'TA App Job failure','Message': 'cannot cast str into int'} |

> **NOTE:**  Response obtained from the app engine service is always 'OK', 200. the actual status of the jobs are stored in the notifications     table in BigQuery for monitoring purpose


**Endpoint for facebook**

## /facebookdata

#### POST
#### Summary:

This is an API which extract data from api.facebook.com website and pushes it to bigquery

##### Payload Sample(This is the payload which is sent to trigger the app engine endpoint through scheduler through HTTP post request):
```sh
{"medianame": "facebook", "history": "False"}
```
#### Responses

| Code | Description | Sample respose in BigQuery notification table |
| ---- | ----------- | ----------- |
| 200 | Successful loading of facebook data to Bigquery | {'JobID': 167312,'Source': 'Linkedin','Subject': 'TA App Job success','Message':      'Facebook data loaded successfully for date: 1/2/2019 Successfully inserted 10 rows in Bigquery table xyz'} |
| 400 | Failure in  loading of facebook data to Bigquery | {'JobID': 167312,'Source': 'Linkedin','Subject': 'TA App Job failure','Message': 'cannot cast str into int'} |

> **NOTE:**  Response obtained from the app engine service is always 'OK', 200. the actual status of the jobs are stored in the notifications     table in BigQuery for monitoring purpose



**Internal code logic for linkedin**

#### Following parameters are to be passed to the function which fetches data for linkedin:
* Start date : This is the start date for which you want to pull the data
* End date : This is the end date for which you want to pull the data 
* Date index :This defines the time range for which the date is to be fetched. Basically splits the total data into small chunks
* accces tokens : This is required for authentication for api calls 


**Step 1:**
Retrieving all Active campaigns using an API call and use a get request with an access tokens passed in headers for eg:
```
  requests.get('https://api.linkedin.com/v2/adCampaignsV2?q=search'+'&search.sponsoredAccount.values[0]=urn:li:sponsoredAccount:507533261'+'&sort.field=ID&sort.order=DESCENDING',headers={"Authorization": "Bearer %s" % access_token})
```                
Here's a sample response we get from this call:
```json
{'targetingCriteria': {'include': {'and': [{'or': {'urn:li:adTargetingFacet:interfaceLocales': ['urn:li:locale:en_US']}}, {'or': {'urn:li:adTargetingFacet:locations': ['urn:li:country:us']}}, {'or': {'urn:li:adTargetingFacet:audienceMatchingSegments': ['urn:li:adSegment:2839623']}}]}}, 'format': 'STANDARD_UPDATE', 'servingStatuses': ['STOPPED', 'CAMPAIGN_GROUP_START_DATE_HOLD'], 'type': 'SPONSORED_UPDATES', 'locale': {'country': 'US', 'language': 'en'}, 'version': {'versionTag': '1'}, 'objectiveType': 'LEAD_GENERATION', 'associatedEntity': 'urn:li:organization:166486', 'runSchedule': {'start': 1571788800000}, 'optimizationTargetType': 'MAX_LEAD', 'targeting': {'includedTargetingFacets': {'audienceMatchingSegments': ['urn:li:adSegment:2839623'], 'locations': ['urn:li:country:us'], 'interfaceLocales': [{'country': 'US', 'language': 'en'}]}}, 'offsitePreferences': {'publisherRestrictionFiles': {'exclude': []}, 'iabCategories': {'exclude': []}}, 'changeAuditStamps': {'created': {'time': 1571849531000}, 'lastModified': {'time': 1571849531000}}, 'campaignGroup': 'urn:li:sponsoredCampaignGroup:606984493', 'dailyBudget': {'amount': '50', 'currencyCode': 'USD'}, 'creativeSelection': 'OPTIMIZED', 'costType': 'CPM', 'unitCost': {'amount': '2', 'currencyCode': 'USD'}, 'name': 'Lead generation - Oct 23, 2019', 'offsiteDeliveryEnabled': False, 'id': 156075923, 'audienceExpansionEnabled': True, 'account': 'urn:li:sponsoredAccount:503260043', 'status': 'DRAFT'}
```

Store this result in an array named campaign data and retrieve the campaign ids from this data through id feild in response json and convert this in to a pandas dataframe by providing proper column names



**Step 2:**
creating an list of all creative ID's and raw data of creatives of each of the campaigns by using following api call:
```
  requests.get('https://api.linkedin.com/v2/adCreativesV2?q=search'+'&search.campaign.values[0]=urn:li:sponsoredCampaign:'+str(campaign_ids[i]), headers={"Authorization": "Bearer %s" % TOK_rw_ads})
```
This gives the creative IDs and creative data for that particular campaign. loop through campaign ids to get for all campaigns
Here's a sample output after appending data into array:
```
creative_ids:
[3767183, 980217817, 2361276]

creative_data:
[{'reference': 'urn:li:share:565858', 'variables': {'data': {'com.linkedin.ads.SponsoredUpdateCreativeVariables': {'activity': 'urn:li:activity:865876969', 'share': 'urn:li:share:6579845647539810304', 'directSponsoredContent': True}}}, 'changeAuditStamps': {'created': {'time': 1568757455000}, 'lastModified': {'time': 1568829436000}}, 'review': {'reviewStatus': 'AUTO_APPROVED'}, 'campaign': 'urn:li:sponsoredCampaign:154790903', 'servingStatuses': ['RUNNABLE'], 'id': 7698709, 'type': 'SPONSORED_STATUS_UPDATE', 'version': {'versionTag': '4'}, 'status': 'ACTIVE'}]
```
Normalise the creative data and convert it in to a pandas dataframe by providing proper column names




**Step 3:**
Retrieving creative metrics for each creative id using an API:
```
https://api.linkedin.com/v2/adAnalyticsV2?q=analytics'+'&pivot=CREATIVE'+'&dateRange.start'+'&dateRange.end'+'+ '&creatives[0]=urn:li:sponsoredCreative:', headers={"Authorization":"Bearer %s" % TOK_ads_reporting}
```

Here's a sample of the creative metrics:
```json
[{ "reference": "urn:li:share:23854823649689", "variables": { "data": { "ebjdvwqghdkvjcm": { "activity": "urn:li:activity:38293746", "share": "jwdvhvndcsa:838716471", "directSponsoredContent": "TRUE" } } }, "changeAuditStamps": { "created": { "time": 1568757455000 }, "lastModified": { "time": 1568757455000 } }, "review": { "reviewStatus": "PENDING" }, "campaign": "urn:li:sponsoredCampaign:154790903", "servingStatuses": ["UNDER_REVIEW"], "id": 86358731, "type": "SPONSORED_STATUS_UPDATE", "version": { "versionTag": "1" }, "status": "ACTIVE" }]
```




Normalise the creative metrics and convert it in to a pandas dataframe by providing proper column names



**Step 4:**
Applying transformations and creating calculated feilds(cpc,cpm etc) and then joining the creative and campaign data to create intermediate table. This intermediate table is then joined with metrics 




**Step 5:**
Modifying the final dataframe by performing following operations:
* Modifying NaN to None and transforming float values to int values
* Modifying NaN to None for columns which has list of items
* Modifying inf and NaN values for calculated metrics to None
* Renaming column names for consistency with facebook
* replacing columns with biq query naming limitations
* Transformations according to bq schema for dataframe columns
* Transforming facetname and facetvalues



**Step 6:**
Loading data in gcs bucket and pushing it to Bigquery
### Response
Here's the sample JSON output which is dumped in to BigQuery:
```json
{ "account_id": "10122500651584", "account_name": "dsvd", "campaign_name": "jhfjhegqwjh", "campaign_id": "432432564279", "adset_name": "wfbwkfbw,jbf,", "adset_id": "087664653889", "ad_name": "Video sbfdjsb,sdf", "ad_id": "6129859948570", "buying_type": "AUCTION", "spend": "0.27", "objective": "VIDEO_VIEWS", "clicks": "0", "impressions": "53", "cpc": null, "cpm": "5.09434", "cpp": "5.09434", "ctr": "0", "frequency": "1", "unique_clicks": "0", "unique_ctr": "0", "unique_inline_link_click_ctr": null, "unique_inline_link_clicks": null, "unique_link_clicks_ctr": null, "cost_per_inline_link_click": null, "cost_per_inline_post_engagement": null, "reach": "53", "inline_link_clicks": null, "inline_link_click_ctr": null, "cost_per_estimated_ad_recallers": "0.27", "ad_status": "active", "cost_per_unique_click": null, "cost_per_unique_inline_link_click": null, "outbound_clicks": [ { "action_type": null, "value": null } ], "outbound_clicks_ctr": [ { "action_type": null, "value": null } ], "cost_per_10_sec_video_view": [ { "action_type": "video_view", "value": "0.038571" } ], "cost_per_action_type": [ { "action_type": "video_view", "value": "0.027" }, { "action_type": "post_engagement", "value": "0.027" }, { "action_type": "page_engagement", "value": "0.027" } ], "cost_per_outbound_click": [ { "action_type": null, "value": null } ], "cost_per_unique_action_type": [ { "action_type": "video_view", "value": "0.027" }, { "action_type": "post_engagement", "value": "0.027" }, { "action_type": "page_engagement", "value": "0.027" } ], "cost_per_unique_outbound_click": [ { "action_type": null, "value": null } ], "unique_outbound_clicks_ctr": [ { "action_type": null, "value": null } ], "unique_outbound_clicks": [ { "action_type": null, "value": null } ], "actions": [ { "action_type": "video_view", "value": "10" }, { "action_type": "post_engagement", "value": "10" }, { "action_type": "page_engagement", "value": "10" } ], "video_10_sec_watched_actions": [ { "action_type": "video_view", "value": "7" } ], "video_30_sec_watched_actions": [ { "action_type": "video_view", "value": "1" } ], "video_avg_time_watched_actions": [ { "action_type": "video_view", "value": "4" } ], "video_p100_watched_actions": [ { "action_type": "video_view", "value": "1" } ], "video_p25_watched_actions": [ { "action_type": "video_view", "value": "7" } ], "video_p50_watched_actions": [ { "action_type": "video_view", "value": "5" } ], "video_p75_watched_actions": [ { "action_type": "video_view", "value": "1" } ], "video_p95_watched_actions": [ { "action_type": "video_view", "value": "1" } ], "video_play_actions": [ { "action_type": "video_view", "value": "47" } ], "video_thruplay_watched_actions": [ { "action_type": "video_view", "value": "5" } ], "website_ctr": [ { "action_type": null, "value": null } ], "relevance_score": { "status": "NOT_ENOUGH_IMPRESSIONS" }, "dates": "2019-09-15", "date_start": "2019-06-17T06:00:00", "date_stop": "2019-10-31T23:00:46" }
```


**Internal code logic for facebook**

##### Following parameters are to be passed to the function which fetches data for facebook:**
* Start date : This is the start date for which you want to pull the data
* Ad status: This is the staus of ads(active, inactive, completed etc) 
* accces tokens : This is required for authentication for api calls 


**Step 1:**
Retrieving facebook ads data:
first get the ad id, campaign_id, campaign_name using request:
```
  requests.get('https://graph.facebook.com/v4.0//act_101234250026264/insights',
                                  params=params_ad_id)
```                
Here's a sample response we get from this call:
```
campaign_ids = ['23423434', '830986953086']
ad_ids = ['6762285278', '34234992113', '454356464', '243554534', '738473094809']
    
```
Then retrieving performance data for all ad id's :
```
requests.get('https://graph.facebook.com/v4.0//' + str(ad_ids[i]) + '/insights', params=params
```

Here's a sample response we get from this call:
```
[{ "account_id": "62536235821", "account_name": "ndmbqmnsbdnmqs", "campaign_name": "sbxkqsb", "campaign_id": "126531285", "adset_name": "anmmsbx,sanx,masx", "adset_id": "125873687216", "ad_name": ",dbqjdliq;msmwjx", "ad_id": "76123921788", "date_start": "2019-09-15", "date_stop": "2019-09-15", "buying_type": "AUCTION", "spend": "0.27", "objective": "VIDEO_VIEWS", "clicks": "0", "impressions": "53", "reach": "53", "cpm": "5.09434", "cpp": "5.09434", "ctr": "0", "cost_per_10_sec_video_view": [{ "action_type": "video_view", "value": "0.038571" }], "cost_per_action_type": [{ "action_type": "video_view", "value": "0.027" }, { "action_type": "post_engagement", "value": "0.027" }, { "action_type": "page_engagement", "value": "0.027" }], "cost_per_estimated_ad_recallers": "0.27", "cost_per_thruplay": [{ "action_type": "video_view", "value": "0.054" }], "cost_per_unique_action_type": [{ "action_type": "video_view", "value": "0.027" }, { "action_type": "post_engagement", "value": "0.027" }, { "action_type": "page_engagement", "value": "0.027" }], "estimated_ad_recallers": "1", "frequency": "1", "actions": [{ "action_type": "video_view", "value": "10" }, { "action_type": "post_engagement", "value": "10" }, { "action_type": "page_engagement", "value": "10" }], "unique_clicks": "0", "unique_ctr": "0", "video_10_sec_watched_actions": [{ "action_type": "video_view", "value": "7" }], "video_30_sec_watched_actions": [{ "action_type": "video_view", "value": "1" }], "video_avg_time_watched_actions": [{ "action_type": "video_view", "value": "4" }], "video_p100_watched_actions": [{ "action_type": "video_view", "value": "1" }], "video_p25_watched_actions": [{ "action_type": "video_view", "value": "7" }], "video_p50_watched_actions": [{ "action_type": "video_view", "value": "5" }], "video_p75_watched_actions": [{ "action_type": "video_view", "value": "1" }], "video_p95_watched_actions": [{ "action_type": "video_view", "value": "1" }], "video_play_actions": [{ "action_type": "video_view", "value": "47" }], "video_thruplay_watched_actions": [{ "action_type": "video_view", "value": "5" }], "relevance_score": { "status": "wdjhwdjfhlwdkj" } }
```

**Step 2:**

Data fetched above did not have a start and dates date, hence fetching campaign data to get start and end dates of campaign
```
  requests.get('https://graph.facebook.com/v3.3/' + account_id + '/campaigns',
                        params=params_ad_id)
```

Here's a sample output:
```
[{'name': 'Post: "jhsjhxshbmxjb,jsnx"', 'id': '5265w6', 'effective_status': 'ACTIVE', 'configured_status': 'ACTIVE', 'status': 'ACTIVE', 'objective': 'nbqsb,', 'created_time': '2019-09-26T07:06:15-0700', 'start_time': '2019-09-26T07:06:18-0700', 'stop_time': '2019-09-30T07:06:15-0700', 'updated_time': '2019-09-26T07:06:15-0700', 'buying_type': 'AUCTION'}]
```
append the start and end dates and this data is streamed to Bigquery

