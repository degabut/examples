# How to Self Host Degabut

**Note**:
- This is a minimal example, with no SSL, rate limit, or CORS configuration on the NGINX server
- [Degabut](https://github.com/degabut/degabut) is at its early stage and unstable at the moment, the versioning isn't semantic, which means minor / patch version bump might contains breaking changes, but latest push on [Degabut Web](https://github.com/degabut/degabut-web) should be compatible with `latest` [Degabut image](https://hub.docker.com/r/suspiciouslookingowl/degabut) (`suspiciouslookingowl/degabut:latest`)

## Initial Setup

1. Clone this repository
```
git clone https://github.com/degabut/examples
```

2. Copy `.env.example` to `.env`
```
cp .env.example .env
```

3. Modify `.env` value as needed, here are some descriptions about the environment variables

|Key|Description|Example|
|---|---|---|
|`BOT_TOKEN`|Your bot token, taken from [this page](https://discord.com/developers/applications) -> Select your app -> go to `Bot` -> get `TOKEN` value|`xxx.yyy.zzz`
|`LAVA_HOST`|Lavalink host, __leave this value to default__|`degabut-lavalink` (default)|
|`LAVA_PASSWORD`|[Long randomly generated password](https://www.lastpass.com/features/password-generator) for Lavalink|`l0nG-r4nD0M-P45SwOrD`
|`POSTGRES_DB`|Postgres database name that is created when the image first started, __you can leave this value to default__|`degabut` (default)|`gjfgih045jxdfgnblcx`
|`POSTGRES_USER`|Postgres default user name, __you can leave this value to default__|`degabut` (default)|
|`POSTGRES_PASSWORD`|[Long randomly generated password](https://www.lastpass.com/features/password-generator) for your database|`l0nG-r4nD0M-P45SwOrD`|
|`POSTGRES_HOST`|Postgres host, __leave this value to default__|`postgres:5432` (default)|
|`JWT_PRIVATE_KEY`|[Very long randomly generated string](https://www.lastpass.com/features/password-generator) for API authentication|`Wp73fBb97mwbYfeArAstkVPj`
|`DISCORD_OAUTH_CLIENT_ID`|Your bot oauth client id, taken from [this page](https://discord.com/developers/applications) -> Select your app -> go to `OAuth2` -> get `CLIENT ID` value|`777777777777777`
|`DISCORD_OAUTH_CLIENT_SECRET`|Your bot oauth client secret, taken from [this page](https://discord.com/developers/applications) -> Select your app -> go to `OAuth2` -> get `CLIENT SECRET` value|`jgj04835uefgjxdljb`

4. Copy `LAVA_PASSWORD` value from your `.env` file, and paste it to `lavalink/application.yml`, under `lavalink` -> `server` -> `password`, for example:
```yaml
lavalink:
  server:
    password: "l0nG-r4nD0M-P45SwOrD"
```

5. Download [Lavalink.jar](https://github.com/lavalink-devs/Lavalink/releases) (v3.x.x) and put it inside the `lavalink` directory

6. Run the containers
```
docker-compose up -d
```

7. Verify that your bot is running properly by, inviting your bot to your server, join and voice channel and send `!play never gonna give you up`

## Web Setup (optional)

While the bot is usable now, you can have a better experience by using the [web client](https://github.com/degabut/degabut-web)

1. Copy `.env.web.example` to `.env.web`

2. Modify `.env.web` value as needed, here are some descriptions about the environment variables

|Key|Description|Example|
|---|---|---|
|`VITE_API_URL`|URL to your Degabut API|`http://example.com/api`|
|`VITE_YOUTUBE_API_URL`|URL to your Degabut YouTube API|`http://example.com/api/youtube`|
|`VITE_WS_URL`|URL to WebSocket API|`ws://example.com/ws`|
|`VITE_OAUTH_URL`|Discord OAuth URL for Authentication, [more information here](#oauth-url) |`https://discord.com/api/oauth2/authorize?...`|

3. Uncomment the `FOR WEB` on `docker.compose.yml` file (enabling `dgb-nginx`, `dgb-youtube`, and `dgb-web-builder` services)

4. Run the containers again `docker-compose up -d`

5. Verify that the website is running properly by visiting `http://example.com`

## Running Multiple Bots

If you want to run multiple bots, follow these steps:

1. Uncomment the `FOR MULTI BOT` on `docker.compose.yml` file (enabling `dgb-red` service)
2. Add the `RED_TOKEN` value on `.env` file with your second bot token
3. If you are using the web client, add the `VITE_DEGABUT_SERVERS` value on `.env.web`
4. Run the containers again `docker-compose up -d`

`VITE_DEGABUT_SERVERS` value is a minified JSON array string of this object:
```json
{
    "name": "Bot Name",
    "apiBaseUrl": "bot-api-base-url",
    "wsUrl": "bot-ws-url",
    "iconUrl": "url-or-path-to-icon"
}
```

If you are following this example, the value should be a minified JSON of:
```json
[
    {
        "name": "Degabut",
        "apiBaseUrl": "http://example.com/api",
        "wsUrl": "ws://example.com/ws",
        "iconUrl": "/icons/colors/degabut-default.png"
    },
    {
        "name": "Redgabut",
        "apiBaseUrl": "http://example.com/red-api",
        "wsUrl": "ws://example.com/red-ws",
        "iconUrl": "/icons/colors/degabut-red.png"
    }
]
```

So the env value should be:

```
VITE_DEGABUT_SERVERS=[{"name":"Degabut","apiBaseUrl":"http://yourserverdomainorip.com/api","wsUrl":"ws://yourserverdomainorip.com/ws","iconUrl":"/icons/colors/degabut-default.png"},{"name":"Redgabut","apiBaseUrl":"http://yourserverdomainorip.com/red-api","wsUrl":"ws://yourserverdomainorip.com/red-ws","iconUrl":"/icons/colors/degabut-red.png"}]
```

Note: 

- `Degabut Web` provides [5 icons](https://github.com/degabut/degabut-web/tree/main/public/icons/colors) with different colors that you can use for your bot icons (on `iconUrl` value), if you are following this example, it can be loaded from `http://yourserverdomainorip.com/icons/colors/degabut-[color].png`

|Key|Icon|
|---|---|
|`degabut-default.png`|<img src="https://raw.githubusercontent.com/degabut/degabut-web/main/public/icons/colors/degabut-default.png" width="32" />|
|`degabut-red.png`|<img src="https://raw.githubusercontent.com/degabut/degabut-web/main/public/icons/colors/degabut-red.png" width="32" />|
|`degabut-orange.png`|<img src="https://raw.githubusercontent.com/degabut/degabut-web/main/public/icons/colors/degabut-orange.png" width="32" />|
|`degabut-green.png`|<img src="https://raw.githubusercontent.com/degabut/degabut-web/main/public/icons/colors/degabut-green.png" width="32" />|
|`degabut-blue.png`|<img src="https://raw.githubusercontent.com/degabut/degabut-web/main/public/icons/colors/degabut-blue.png" width="32" />|

-  If you added `VITE_DEGABUT_SERVERS` value, `VITE_API_BASE_URL` will still be needed, and it will be used only for user's authentication

### OAuth URL

To generate OAUth URL:

1. Go to https://discord.com/developers/applications
2. Select your application
3. Select `OAuth2` -> `General`
4. Add redirects URL, if you are following this tutorial, add `http://example.com/oauth`
5. Select `OAuth2` -> `URL Generator`
6. Check `Identify` scope and select the redirect URI with the one you just added, the generated link should look like
```
https://discord.com/api/oauth2/authorize?client_id=[your_client_id]&redirect_uri=[your_redirect_uri]&response_type=code&scope=identify
```
