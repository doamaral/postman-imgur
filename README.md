# postman-imgur
Using imgur public API to try some Postman's features including:
 - Environment and Collections Variables
 - Pre-requests and Tests scripts
 - Dynamic Variables
 - Oauth2 Authentication and refreshing its tokens when expired automatically using pre-request scripts
 - Fancy reports using HTMLEXTRA

## Overview

### Pre requisites
- Node.js >= v10
- Newman: `npm install -g newman`
- HTMLExtra: `npm install -g newman-reporter-htmlextra`

## Running Tests using Newman

Download or Clone the project. Once you cd to the project folder:

```
newman run "imgur.postman_collection.json" -e "Imgur - PRD.postman_environment.json" --iteration-count 1 --reporters cli,htmlextra
```

where:
 - `--iteration-count`: is the number of iterations (repetitions)
 - `--reporters`: the test execution outputs
   - cli: the prompt/terminal
   - html: default newman's html report generator
   - htmlextra: a fancy report template

### Variables

#### Environment
- `baseUrl`: https://api.imgur.com
- `version`: api version, initially equals to 3
- `clientId` and `clientSecret`: obtained when you subscribe your app at imgur service
- `accessToken` and `refreshToken`: used to store its respectives values when we get it

#### Collection

- `expiresIn`: To control the Token Expiration
- `tokenTimestamp`: Set the Time of the Token creation
- `prefixList`: List of preffixes that can be used when creating a album
- `newAlbumHashes`: Got after the execution of the POST request to record the Album Id after its creation
- `randomAlbumName`: Dynamically obtained using a sample from `prefixList`, a fixed string `" album "` and a Dynamic Variable from postman (neat!)

## Oauth2 authentication
In this solution, I'll need to get manually the first pair of access and refresh token. After that, I'll refresh automatically when access token expires.

Right clicking at the collection, go to Edit at the context menu, and then to the Authentication tab.

![](_img/oauth_config.png)

Select `Oauth 2.0` as the type of authentication. After that, click on `Get New Access Token` to setup all info, then `Request Token`:

```
Callback URL: https://www.getpostman.com/oauth2/callback
Auth URL: https://api.imgur.com/oauth2/authorize
Access Token URL: https://api.imgur.com/oauth2/authorize
```

![](_img/oauth_get_token.png)

At this moment, you're going to be requested to loggin to imgur and if everythig is ok, you're going to get a Token ready to be used.

![](_img/oauth_use_token.png)

> Note: all requests that need to authenticate, could use `Inherit auth from parent` at its Auth tab. Although, for our purpose, choose the `Bearer Token` option, and fill the Token field with the Enviroment variable `{{accseeToken}}`. This way, every time we change the variable value, the token will be changed in every request.

> Note 2: Either choosing `Inherit auth from parent` or `Bearer Token` an `Authorization` entry will be added to request Header automatically.

### Using the first gotten token
After configuring and getting correctly the first pair of tokens, copy and paste its values to its respectives environment variables.

![](_img/env_variables.png)

Try to run some simple request to see if your basic setup is functioning

To be continued...

## References

- [Imgur apiDocs](https://apidocs.imgur.com/)
- [How to Automate OAuth2 Token Renewal in Postman](https://medium.com/@allen.helton/how-to-automate-oauth2-token-renewal-in-postman-864420d381a0)
- [Postman Sandbox API reference](https://learning.postman.com/docs/postman/scripts/postman-sandbox-api-reference/#pmsendrequest)
- [How to persist an OAuth2 token (or use a refresh token) in Postman collections?](https://stackoverflow.com/questions/35070891/how-to-persist-an-oauth2-token-or-use-a-refresh-token-in-postman-collections)
- [Dynamic variables in Postman](https://learning.postman.com/docs/writing-scripts/script-references/variables-list/)
- [Dynamic variables](https://postman-quick-reference-guide.readthedocs.io/en/latest/dynamic-variables.html)
