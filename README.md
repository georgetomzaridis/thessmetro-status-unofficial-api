
# Thessaloniki Metro Network Status Unofficial API

On November 30, 2024, the Thessaloniki Metro officially opened its doors to the public, launching a real-time status tracking system available through the thessmetro.gr website. This unofficial API aggregates live data directly from the official platform and offers valuable insights into the operation of the entire metro network.

## Disclaimer
This project is under active development. I have placed some soft-caching mechanisms to prevent spamming. The creator is not responsible for any malicious action that could damage his own systems or those of third parties (Thema / Metro Thessaloniki Infrastructure). In case of a legal problem, the creator upon request has the right to terminate the operation of the service at any time, without further notice. Only for personal use, not commercial.


## About this version
#### Current Version: v1.0 (08/02/2025)
- Fetch Operation Titles (el/en)
- Fetch Operation Descriptions (el/en)
- Fetch Home Page Announcements (el/en)
- Fetch Elevator Status for each available station on the metro network (el/en)

## Tech Stack
- Node.JS (https://nodejs.org/en)
- Express (https://expressjs.com/)
- Axios (https://axios-http.com/docs/intro)
- Cheerio (https://cheerio.js.org/)
- Redis (https://redis.io/)

## API Reference

#### Domain

```https
  https://thessmetro-status-api.georgetomzaridis.eu/status
```

#### Authentication / Authorazation

You don't need anything, in order to access the API endpoints. It's open for public usage.

#### Caching 
We cache the data returned from thessmetro.gr for 5 minutes in Redis. During this time, the cache is publicly available for each request. Once the cache expires, the server will fetch fresh data directly from the thessmetro.gr website with the next request.
In the API response, the "updatedAt" field indicates the last time the data was updated, while the "nextUpdateAt" field shows when the next update (fetching fresh data from thessmetro.gr) will be available.
WARNING: Some data may be outdaded

#### Content Language

All the content / data is both on greek and english language.

### API Call

``` https
  GET https://thessmetro-status-api.georgetomzaridis.eu/status
```

##### Example Response
``` json
{
    "statusCode": 200,
    "message": "Success",
    "requestedAt": "2025-02-08T03:17:37.176Z",
    "data": {
        "updatedAt": "2025-02-08T03:06:39.212Z",
        "nextUpdateAt": "2025-02-08T03:26:39.212Z",
        "isNormalOperation": true,
        "operationTitles": {
            "el": "Κανονική Λειτουργία",
            "en": "Normal Operation"
        },
        "operationDescriptions": {
            "el": "Τα δρομολόγια διεξάγονται κανονικά σε όλο το μήκος της Γραμμής 1.",
            "en": "The services are running normally along the entire length of the Line 1."
        },
        "homeAnnouncement": {
            "el": "Υπενθυμίζουμε στο επιβατικό κοινό ότι τα εισιτήρια είναι επαναφορτιζόμενα. Για την καλύτερη εξυπηρέτησή σας αλλά και για την προστασία του περιβάλλοντος, μπορείτε να τα επαναφορτίζετε στα αυτόματα μηχανήματα έκδοσης εισιτηρίων για τις επόμενες διαδρομές σας. Σας ευχαριστούμε για τη συνεργασία!",
            "en": "We remind passengers that tickets are rechargeable. For your convenience and to protect the environment, you can recharge them at the automatic ticket machines for your future journeys. Thank you for your cooperation!"
        },
        "elevatorStatus": {
            "el": [
                {
                    "stationName": "Νέος Σιδηροδρομικός Σταθμός",
                    "isWorking": true,
                    "statusDescription": "Οι ανελκυστήρες λειτουργούν κανονικά"
                },
                {
                    "stationName": "Δημοκρατίας",
                    "isWorking": true,
                    "statusDescription": "Οι ανελκυστήρες λειτουργούν κανονικά"
                }
            ],
            "en": [
                {
                    "stationName": "New Railway Station",
                    "isWorking": true,
                    "statusDescription": "The elevators are working properly"
                },
                {
                    "stationName": "Dimokratias",
                    "isWorking": true,
                    "statusDescription": "The elevators are working properly"
                },
            ]
        }
    }
}

```
| Languages | Code |
| :-------- | :------- |
| `Greek` | `el` |
| `English` | `en` |

| Field | Type     | Notes                |
| :-------- | :------- | :------------------------- |
| `statusCode` | `integer` | HTTP Status Code |
| `message` | `string` | Request fullified message |
| `requestedAt` | `string` | Request DateTime as ISOString |
| `data` | `array` | Data payload (can be []) |
| `data.updatedAt` | `string` | Last fresh fetch from thessmetro.gr DateTime as ISOString |
| `data.nextUpdateAt` | `string` | Cache expiring DateTime as ISOString |
| `data.isNormalOperation` | `boolean` | Is the metro system operates normally? |
| `data.operationTitles` | `object` | el,en Opearation Titles |
| `data.operationTitles.el` | `string or null` | Operation Title in greek language (can be null) |
| `data.operationTitles.en` | `string or null` | Operation Title in english language (can be null) |
| `data.operationDescriptions` | `object` | el,en Opearation Descriptions |
| `data.operationDescriptions.el` | `string or null` | Operation Description in greek language (can be null) |
| `data.operationDescriptions.en` | `string or null` | Operation Description in english language (can be null) |
| `data.homeAnnouncement` | `object` | el,en Home Announcement |
| `data.homeAnnouncement.el` | `string or null` | Home Announcement in greek language (can be null) |
| `data.homeAnnouncement.en` | `string or null` | Home Announcement in english language (can be null) |
| `data.elevatorStatus` | `object` | el,en Statuses of all the station elevators (can be []) |
| `data.elevatorStatus.el` | `array` | List of stations for elevators statuses in greek language (see below) |
| `data.elevatorStatus.en` | `array` | List of stations for elevators statuses in english language (see below) |


| elevatorStatus | Type | Notes |
| :-------- | :------- | :------- |
| `stationName` | `string or null` | `Station name` |
| `isWorking` | `boolean` | `Elevators operational in this station?` |
| `statusDescription` | `string or null` | `Description to explain if this station have operational elevators` |

### API Status Codes
| Code | What does this mean? |
| :-------- | :------- |
| `200` | `Everything went well, you are happy` |
| `503` | `Service Unavailable, maybe we cannot fetch data from thessmetro.gr or something is broken` |
| `500` | `Something went wrong, maybe we cannot fetch data from thessmetro.gr or something is broken`|

#### Example Error Responses
In general if you are not getting a json that contains status / error fields you are fine :)
``` json
{
    "statusCode": 503,
    "message": "Service Temporary Unavailable, please try again later",
    "requestedAt": "2025-02-08T03:51:47.956Z",
    "data": {}
}

{
    "statusCode": 500,
    "message": "Service Temporary Unavailable, please try again later",
    "requestedAt": "2025-02-08T03:51:47.956Z",
    "data": {}
}


```

## Bugs/Suggestions

For bugs/suggestions please send me an email: georgetomzaridis@gmail.com
