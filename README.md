# Foxhole API — Quickstart Guide

This document is a concise, easy-to-follow description of the Foxhole API. It covers authentication, base URL, available endpoints, required/optional parameters, and trimmed examples so you can get productive quickly.

## Base URL
- https://foxhole.bot/api/v1

## Authentication
- All requests require an API key in the header:
  - Header: `X-API-Key: YOUR-API-KEY`

## Query Array Format
- For endpoints that accept arrays in query parameters, pass values as comma-separated lists.
- Examples: `screenName=elonmusk,VitalikButerin`, `userId=2259434528,902926941413453824`, `id=20,1950452968655851759`.

## Endpoints

1) Get Twitter users info
- GET /twitterUsers/info
- Purpose: Retrieve profile information for one or more Twitter users by screen name and/or user ID.
- Query parameters:
  - screenName (array, optional): Comma-separated screen names.
  - userId (array, optional): Comma-separated user IDs.
- Example:
  - curl -i -H "X-API-Key: YOUR-API-KEY" "https://foxhole.bot/api/v1/twitterUsers/info?screenName=elonmusk,VitalikButerin&userId=2259434528,902926941413453824"
- Sample response (trimmed):
  - [
    {"id":"44196397","name":"Elon Musk","screenName":"elonmusk","followersCount":226882974,"friendsCount":1215,"verified":true,"statusesCount":86597,"isKol":true,"createdAt":"2009-06-02T20:12:29.000Z"},
    {"id":"295218901","name":"vitalik.eth","screenName":"VitalikButerin","followersCount":5825700,"friendsCount":507,"verified":false,"statusesCount":21141,"isKol":true,"createdAt":"2011-05-08T16:03:03.000Z"}
  ]

2) Get stored tweets (optional time range)
- GET /twitterUsers/stored-tweets
- Purpose: Return stored tweets for the specified users, optionally filtered by a createdAt time window.
- Query parameters:
  - userId (array, required): Comma-separated user IDs.
  - createdAfter (string, optional): ISO 8601 timestamp.
  - createdBefore (string, optional): ISO 8601 timestamp.
- Example:
  - curl -i -H "X-API-Key: YOUR-API-KEY" "https://foxhole.bot/api/v1/twitterUsers/stored-tweets?userId=2259434528,902926941413453824&createdAfter=2025-08-11T12:00:00Z&createdBefore=2025-08-11T23:59:59Z"
- Sample response (trimmed):
  - [
    {"tweet":{"id":"1954949720553689519","text":"@EugeniaChele Where did I say anything about Weapons","favoriteCount":18,"createdAt":"2025-08-11T16:55:09.000Z"},"user":{"id":"2259434528","name":"Cobie","screenName":"cobie","followersCount":828309,"friendsCount":1207,"isKol":true}},
    {"tweet":{"id":"1954938403507839354","text":"Hash Global has released its latest research paper ...","favoriteCount":376,"viewCount":134752,"retweetCount":134,"createdAt":"2025-08-11T16:10:11.000Z"},"user":{"id":"902926941413453824","name":"CZ 🔶 BNB","screenName":"cz_binance","followersCount":10234543,"friendsCount":1877,"isKol":true}}
  ]

3) Get tweets by IDs
- GET /twitterUsers/tweets
- Purpose: Fetch tweets by their IDs.
- Query parameters:
  - id (array, required): Comma-separated tweet IDs.
- Example:
  - curl -H "X-API-Key: YOUR-API-KEY" "https://foxhole.bot/api/v1/twitterUsers/tweets?id=20,1950452968655851759"
- Sample response (trimmed):
  - [
    {"id":"20","userId":"12","text":"just setting up my twttr","favoriteCount":263467,"retweetCount":122875,"createdAt":"2006-03-21T20:50:14.000Z","updatedAt":"2025-09-30T07:32:36.746Z"},
    {"id":"1950452968655851759","userId":"851054131418505216","text":"这是马库斯·佩尔松。...","favoriteCount":3783,"viewCount":2776275,"createdAt":"2025-07-30T07:06:40.000Z","updatedAt":"2025-09-30T07:32:36.746Z"}
  ]

4) Create a keyword monitor
- POST /keywordMonitors
- Purpose: Create a new keyword monitor that searches for tweets containing specified keywords.
- Request body (JSON):
  - name (string, required)
  - keywords (array<string>, required)
- Example:
  - curl -X POST "https://foxhole.bot/api/v1/keywordMonitors" \
    -H "Content-Type: application/json" \
    -H "X-API-Key: YOUR-API-KEY" \
    -d '{"name":"uxlink tweets","keywords":["@uxlink"]}'
- Sample response (trimmed):
  - {"id":81,"handle":"uxlink-tweets","counter":4,"name":"uxlink tweets","keywords":["@uxlink"],"currentDelayMs":5000,"nextSearchAt":"2025-09-30T08:10:30.948Z","createdAt":"2025-09-30T08:10:30.948Z","updatedAt":"2025-09-30T08:10:30.948Z","status":"active","slug":"uxlink-tweets-4"}

5) Get a keyword monitor by slug
- GET /keywordMonitors/{slug}
- Purpose: Retrieve a single keyword monitor by its slug.
- Path parameters:
  - slug (string, required)
- Example:
  - curl -X GET "https://foxhole.bot/api/v1/keywordMonitors/uxlink-tweets" -H "X-API-Key: YOUR-API-KEY"
- Sample response (trimmed):
  - {"id":39,"name":"uxlink tweets","status":"paused","keywords":["@UXLINKofficial"],"createdAt":"2025-08-01T16:10:56.775Z","updatedAt":"2025-08-01T16:10:56.775Z","endAt":null,"tweetCount":10201,"userCount":3466,"slug":"uxlink-tweets"}

6) List users associated with a monitor
- GET /keywordMonitors/{slug}/users
- Purpose: List users who have tweeted content matched by the monitor.
- Path parameters:
  - slug (string, required)
- Example:
  - curl -X GET "https://foxhole.bot/api/v1/keywordMonitors/uxlink-tweets/users" -H "X-API-Key: YOUR-API-KEY"
- Sample response (trimmed):
  - [
    {"userId":"1001811143680241664","name":"Jer","screenName":"HelloRobinson","followersCount":2568,"friendsCount":1663,"kolFollowersCount":11,"tweetCount":1,"percentage":0},
    {"userId":"1002216461882900480","name":"Susie","screenName":"0x_susie","followersCount":1568,"friendsCount":778,"kolFollowersCount":3,"tweetCount":1,"percentage":0}
  ]

## Notes
- Timestamps are ISO 8601 (e.g., `2025-08-11T16:55:09.000Z`).
- Some fields may be null depending on the tweet or user context.

### What is kolFollowersCount?

We have labeled about 10,000 Twitter accounts as KOLs — accounts with large followings and strong reputations. When these KOLs follow an account or like a post, it increases the post’s perceived reliability.


### Futher information
Please visit [Foxhole Bot](https://foxhole.bot/discover) for more information.
