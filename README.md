# Maxwell monitor customer portal API

## Endpoints

Endpoints are described in the attached swagger file.

## Obtaining tokens

1. Request admin account for your company.
2. Create new token pair in the configuration section of the portal.
3. Save both tokens for later use. Tokens cannot be displayed later.

## Security

Maximum expiration periods for the tokens are different based on the token capabilities. A token limited only to sensor data can have unlimited validity. This may change in future versions upon prior notice. Therefore we recommend using expiring tokens both for security and future compatibility.

Second use of a refresh token invalidates all access and refresh tokens in the family. In such scenario, please generate new token pair in the web interface.

Token family is invalidated when the user who created the initial token pair is deleted. In such scenario, please generate new token pair in the web interface.

## Authentication

Working example is implemented in `portal_curl` script. Requires `curl` and `jq` to be instaled on the system. Fill in your token pair into the variables at the beginning of the file.

Validity of both tokens can be checked locally. The part of the token between the two dots is a base64 encoded JSON, expiration time is a unix timestamp stored in the `exp` key.

To obtain new access token make request with refresh token to the authorization URL `/api/v1/refresh_access_token`. Response contains new refresh token and access token.
```
curl -X POST -L -H "Authorization: $refresh_token" -H "Accept: application/json" "$authorization_url"
```

For data requests replace refresh token with access token and request the desired url
```
curl -L -H "Authorization: $access_token" -H "Content-Type: application/json" -H "Accept: application/json" $api_url
```

## Rate limiting

Number of requests in a time period is limited. When the limit is reached the server responds with code `429 Too Many Requests` and adds other details to the response headers.
```
RateLimit-Limit: 60
RateLimit-Remaining: 0
RateLimit-Reset: 1730386320
```

## Examples

Request sensor information with the latest value
```
./portal_curl -X POST -d '{"sensor_ids":[123456]}' https://portal.dc7bycecolo.com/api/v1/sensors
```
