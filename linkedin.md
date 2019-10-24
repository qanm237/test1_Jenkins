# Linkedin  API description
This is an API which extract data from api.linkedin.com website and pushes it to bigquery
## Requirements
**Complete these following requirements:**
* create a [developer profile](https://www.linkedin.com/developers/) 
* generate an [access token](https://docs.microsoft.com/en-us/linkedin/shared/authentication/authorization-code-flow)
## Code flow
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
[73122986, 528512552, 36652525]

creative_data:
[{'reference': 'urn:li:share:6579845647539810304', 'variables': {'data': {'com.linkedin.ads.SponsoredUpdateCreativeVariables': {'activity': 'urn:li:activity:6579845647938260992', 'share': 'urn:li:share:6579845647539810304', 'directSponsoredContent': True}}}, 'changeAuditStamps': {'created': {'time': 1568757455000}, 'lastModified': {'time': 1568829436000}}, 'review': {'reviewStatus': 'AUTO_APPROVED'}, 'campaign': 'urn:li:sponsoredCampaign:154790903', 'servingStatuses': ['RUNNABLE'], 'id': 73122986, 'type': 'SPONSORED_STATUS_UPDATE', 'version': {'versionTag': '4'}, 'status': 'ACTIVE'}]
```
Normalise the creative data and convert it in to a pandas dataframe by providing proper column names




**Step 3:**
Retrieving creative metrics for each creative id using an API:
```
https://api.linkedin.com/v2/adAnalyticsV2?q=analytics'+'&pivot=CREATIVE'+'&dateRange.start'+'&dateRange.end'+'+ '&creatives[0]=urn:li:sponsoredCreative:', headers={"Authorization":"Bearer %s" % TOK_ads_reporting}
```

Here's a sample of the creatives data:


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
## Response
Here's the sample JSON output which is dumped in to BigQuery:
```json
{
"account_id": "101234250026264",
"account_name": "Transamerica",
"campaign_name": "Retirement - App/Tim Howard Video Views - Jun-Dec19",
"campaign_id": "6129856732370",
"adset_name": "Retirement - App/Tim Howard Video Views - Jun-Oct19 - IG Soccer Fans",
"adset_id": "6129858974370",
"ad_name": "Video - Tim Howard - 15-Sec Phase 1",
"ad_id": "6129859948570",
"buying_type": "AUCTION",
"spend": "0.27",
"objective": "VIDEO_VIEWS",
"clicks": "0",
"impressions": "53",
"cpc": null,
"cpm": "5.09434",
"cpp": "5.09434",
"ctr": "0",
"frequency": "1",
"unique_clicks": "0",
"unique_ctr": "0",
"unique_inline_link_click_ctr": null,
"unique_inline_link_clicks": null,
"unique_link_clicks_ctr": null,
"cost_per_inline_link_click": null,
"cost_per_inline_post_engagement": null,
"reach": "53",
"inline_link_clicks": null,
"inline_link_click_ctr": null,
"cost_per_estimated_ad_recallers": "0.27",
"ad_status": "active",
"cost_per_unique_click": null,
"cost_per_unique_inline_link_click": null,
"outbound_clicks": [
    {
        "action_type": null,
        "value": null
    }
],
"outbound_clicks_ctr": [
    {
        "action_type": null,
        "value": null
    }
],
"cost_per_10_sec_video_view": [
    {
        "action_type": "video_view",
        "value": "0.038571"
    }
],
"cost_per_action_type": [
    {
        "action_type": "video_view",
        "value": "0.027"
    },
    {
        "action_type": "post_engagement",
        "value": "0.027"
    },
    {
        "action_type": "page_engagement",
        "value": "0.027"
    }
],
"cost_per_outbound_click": [
    {
        "action_type": null,
        "value": null
    }
],
"cost_per_unique_action_type": [
    {
        "action_type": "video_view",
        "value": "0.027"
    },
    {
        "action_type": "post_engagement",
        "value": "0.027"
    },
    {
        "action_type": "page_engagement",
        "value": "0.027"
    }
],
"cost_per_unique_outbound_click": [
    {
        "action_type": null,
        "value": null
    }
],
"unique_outbound_clicks_ctr": [
    {
        "action_type": null,
        "value": null
    }
],
"unique_outbound_clicks": [
    {
        "action_type": null,
        "value": null
    }
],
"actions": [
    {
        "action_type": "video_view",
        "value": "10"
    },
    {
        "action_type": "post_engagement",
        "value": "10"
    },
    {
        "action_type": "page_engagement",
        "value": "10"
    }
],
"video_10_sec_watched_actions": [
    {
        "action_type": "video_view",
        "value": "7"
    }
],
"video_30_sec_watched_actions": [
    {
        "action_type": "video_view",
        "value": "1"
    }
],
"video_avg_time_watched_actions": [
    {
        "action_type": "video_view",
        "value": "4"
    }
],
"video_p100_watched_actions": [
    {
        "action_type": "video_view",
        "value": "1"
    }
],
"video_p25_watched_actions": [
    {
        "action_type": "video_view",
        "value": "7"
    }
],
"video_p50_watched_actions": [
    {
        "action_type": "video_view",
        "value": "5"
    }
],
"video_p75_watched_actions": [
    {
        "action_type": "video_view",
        "value": "1"
    }
],
"video_p95_watched_actions": [
    {
        "action_type": "video_view",
        "value": "1"
    }
],
"video_play_actions": [
    {
        "action_type": "video_view",
        "value": "47"
    }
],
"video_thruplay_watched_actions": [
    {
        "action_type": "video_view",
        "value": "5"
    }
],
"website_ctr": [
    {
        "action_type": null,
        "value": null
    }
],
"relevance_score": {
    "status": "NOT_ENOUGH_IMPRESSIONS"
},
"dates": "2019-09-15",
"date_start": "2019-06-17T06:00:00",
"date_stop": "2019-10-31T23:00:46"
}
```


## Version: v1
