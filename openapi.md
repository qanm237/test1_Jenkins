# Apple Analytics API
This is an API which extract  data from analytics.itunes.apple.com website and pushes it to bigquery

## Version: 1.0.0

**Contact information:**  
Dev Team  
you@your-company.com  

**License:** [Apache 2.0](http://www.apache.org/licenses/LICENSE-2.0.html)

### /load_itunesdata

#### GET
##### Summary:

Loads data from itunesNodejs-Server  to Bigquery tables

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Successful loading of Itunes data to Bigquery |
| 400 | Failure in  loading of Itunes data to Bigquery |

### /installs_uninstalls

#### GET
##### Summary:

Pulls installs and uninstalls data from apple analyitics website

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Successful |
| 400 | Failure |

### /appunits

#### GET
##### Summary:

Pulls appunits data from apple analyitics website

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Successful |
| 400 | Failure |

### /impressions

#### GET
##### Summary:

Pulls impressionsTotal and impressionsTotalUnique data  from apple analyitics website

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Successful |
| 400 | Failure |

### /productpageview

#### GET
##### Summary:

Pulls pageViewCount and pageViewUnique data from apple analyitics website

##### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Successful |
| 400 | Failure |
