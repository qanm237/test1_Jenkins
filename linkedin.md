# Linkedin  API description
This is an API which extract data from api.linkedin.com website and pushes it to bigquery
Once you've
[registered your client](https://www.linkedin.com/developers/) it's easy
to start requesting data from Linkedin.
All endpoints are only accessible via https and are located at
`api.linkedin.com`.
You're best off using an access_token for the authenticated user for each
endpoint, though many endpoints don't require it.
In some cases an access_token will give you more access to information, and
in all cases, it means that you are operating under a per-access_token limit
vs. the same limit for your single client_id.
## Requirements
**Complete these following requirements:**
* create a [developer profile](https://www.linkedin.com/developers/) 
* generate an [access token](https://docs.microsoft.com/en-us/linkedin/shared/authentication/authorization-code-flow)

## Response
Here's the sample JSON :
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
