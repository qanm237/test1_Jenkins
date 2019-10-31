# Facebook API description
This is an API which extract data from api.facebook.com website and pushes it to bigquery


## Understanding the pipeline flow

Cloud scheduler is used to trigger the facebook service deployed in app engine through HTTP post request with following two parameters :
* service name i.e facebook
* history: this defines the type of load(historical or incremental). It is true for historical and false for incremental  



## Requirements
**Complete these following requirements:**
* [login](https://www.facebook.com/login/?next=https%3A%2F%2Fdevelopers.facebook.com%2Ftools%2Fexplorer%2F) to facebook
* generate an [access token](https://developers.facebook.com/docs/facebook-login/access-tokens). When someone connects with an app using Facebook Login and approves the request for permissions, the app obtains an access token that provides temporary, secure access to Facebook APIs.
## Code flow

**Following parameters are to be passed to the function which fetches data for facebook:**
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


## Version: v1
