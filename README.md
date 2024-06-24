# How to Self Host Degabut

**Note**:

- This is a minimal example, with no SSL, rate limit, or CORS configuration on the NGINX server
- [Degabut](https://github.com/degabut/degabut) is at its early stage and unstable at the moment, the versioning isn't semantic, which means minor / patch version bump might contains breaking changes, but latest push on [Degabut Web](https://github.com/degabut/degabut-web) should be compatible with `latest` [Degabut image](https://hub.docker.com/r/suspiciouslookingowl/degabut) (`suspiciouslookingowl/degabut:latest`)

## Table of Contents

- [Bot Setup](#bot-setup)
- [Web Setup optional](#web-setup-optional)
- [OAuth URL](#oauth-url)
- [Running Multiple Bots](#running-multiple-bots)
- [Spotify Support](#spotify-support)
- [Discord Activity](#discord-activity)

## Bot Setup

1. Clone this repository (or <a href="https://github.com/degabut/examples/archive/refs/heads/main.zip" target="_blank">Download it</a>)

```
git clone https://github.com/degabut/examples
```

2. Copy `.env.example` to `.env`

```
cp .env.example .env
```

3. Modify `.env` value as needed, here are some descriptions about the environment variables

| Key                           | Description                                                                                                                                                         | Example                      |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------- |
| `BOT_TOKEN`                   | Your bot token, taken from [this page](https://discord.com/developers/applications) -> Select your app -> go to `Bot` -> get `TOKEN` value                          | `xxx.yyy.zzz`                |
| `DISCORD_OAUTH_CLIENT_ID`     | Your bot oauth client id, taken from [this page](https://discord.com/developers/applications) -> Select your app -> go to `OAuth2` -> get `CLIENT ID` value         | `777777777777777`            |
| `DISCORD_OAUTH_CLIENT_SECRET` | Your bot oauth client secret, taken from [this page](https://discord.com/developers/applications) -> Select your app -> go to `OAuth2` -> get `CLIENT SECRET` value | `jgj04835uefgjxdljb`         |
| `LAVA_HOST`                   | Lavalink host, **leave this value to default**                                                                                                                      | `degabut-lavalink` (default) |
| `LAVA_PASSWORD`               | [Long randomly generated password](https://www.lastpass.com/features/password-generator) for Lavalink                                                               | `l0nG-r4nD0M-P45SwOrD`       |
| `POSTGRES_DB`                 | Postgres database name that is created when the image first started, **you can leave this value to default**                                                        | `degabut` (default)          |
| `POSTGRES_USER`               | Postgres default user name, **you can leave this value to default**                                                                                                 | `degabut` (default)          |
| `POSTGRES_PASSWORD`           | [Long randomly generated password](https://www.lastpass.com/features/password-generator) for your database                                                          | `l0nG-r4nD0M-P45SwOrD`       |
| `POSTGRES_HOST`               | Postgres host, **leave this value to default**                                                                                                                      | `postgres:5432` (default)    |
| `JWT_PRIVATE_KEY`             | [Very long randomly generated string](https://www.lastpass.com/features/password-generator) for API authentication                                                  | `l0nG-r4nD0M-5tR1nG`         |

4. Copy `LAVA_PASSWORD` value from your `.env` file, and paste it to `lavalink/application.yml`, under `lavalink` -> `server` -> `password`, for example:

```yaml
lavalink:
  server:
    password: "l0nG-r4nD0M-P45SwOrD"
```

5. Run the containers

```
docker-compose up -d
```

6. Verify that your bot is running properly by, inviting your bot to your server, join and voice channel and send `!play never gonna give you up`

## Web Setup (optional)

While the bot is usable now, you can have a better experience by using the [web client](https://github.com/degabut/degabut-web)

1. Copy `.env.web.example` to `.env.web`

2. Modify `.env.web` value as needed, here are some descriptions about the environment variables

| Key                         | Description                                                               | Example                                        |
| --------------------------- | ------------------------------------------------------------------------- | ---------------------------------------------- |
| `VITE_API_BASE_URL`         | URL to your Degabut API                                                   | `http://yourhost.com/api`                      |
| `VITE_YOUTUBE_API_BASE_URL` | URL to your Degabut YouTube API                                           | `http://yourhost.com/api/youtube`              |
| `VITE_WS_URL`               | URL to WebSocket API                                                      | `ws://yourhost.com/ws`                         |
| `VITE_OAUTH_URL`            | Discord OAuth URL for Authentication, [more information here](#oauth-url) | `https://discord.com/api/oauth2/authorize?...` |

3. Uncomment under the `FOR WEB` comment on the `docker.compose.yml` file (enabling `dgb-nginx`, `dgb-youtube`, and `dgb-web-builder` services)

4. Run the containers again `docker-compose up -d`

5. Verify that the website is running properly by visiting `http://yourhost.com`

## OAuth URL

To generate OAUth URL:

1. Go to https://discord.com/developers/applications
2. Select your application
3. Select `OAuth2` -> `General`
4. Add redirects URL, if you are following this tutorial, add `http://yourhost.com/oauth`
5. Select `OAuth2` -> `URL Generator`
6. Check `Identify` scope and select the redirect URI with the one you just added, the generated link should look like

```
https://discord.com/api/oauth2/authorize?client_id=[your_client_id]&redirect_uri=[your_redirect_uri]&response_type=code&scope=identify
```

## Running Multiple Bots

If you want to run multiple bots, follow these steps:

1. Uncomment under the `FOR MULTI BOT` comment on the `docker.compose.yml` file (enabling `dgb-red` service)
2. Add the `RED_TOKEN` value on `.env` file with your second bot token
3. If you are using the web client, add the `VITE_APPLICATIONS` value on `.env.web`
4. Run the containers again `docker-compose up -d`

`VITE_APPLICATIONS` value is a minified JSON array string of this object:

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
    "apiBaseUrl": "http://yourhost.com/api",
    "wsUrl": "ws://yourhost.com/ws",
    "iconUrl": "/icons/colors/degabut-default.png"
  },
  {
    "name": "Redgabut",
    "apiBaseUrl": "http://yourhost.com/red-api",
    "wsUrl": "ws://yourhost.com/red-ws",
    "iconUrl": "/icons/colors/degabut-red.png"
  }
]
```

So the env value should be:

```
VITE_APPLICATIONS=[{"name":"Degabut","apiBaseUrl":"http://yourhost.com/api","wsUrl":"ws://yourhost.com/ws","iconUrl":"/icons/colors/degabut-default.png"},{"name":"Redgabut","apiBaseUrl":"http://yourhost.com/red-api","wsUrl":"ws://yourhost.com/red-ws","iconUrl":"/icons/colors/degabut-red.png"}]
```

Note:

- `Degabut Web` provides [5 icons](https://github.com/degabut/degabut-web/tree/main/public/icons/colors) with different colors that you can use for your bot icons (on `iconUrl` value), if you are following this example, it can be loaded from `http://yourhost.com/icons/colors/degabut-[color].png`

| Key                   | Icon                                                                                                                        |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `degabut-default.png` | <img src="https://raw.githubusercontent.com/degabut/degabut-web/main/public/icons/colors/degabut-default.png" width="32" /> |
| `degabut-red.png`     | <img src="https://raw.githubusercontent.com/degabut/degabut-web/main/public/icons/colors/degabut-red.png" width="32" />     |
| `degabut-orange.png`  | <img src="https://raw.githubusercontent.com/degabut/degabut-web/main/public/icons/colors/degabut-orange.png" width="32" />  |
| `degabut-green.png`   | <img src="https://raw.githubusercontent.com/degabut/degabut-web/main/public/icons/colors/degabut-green.png" width="32" />   |
| `degabut-blue.png`    | <img src="https://raw.githubusercontent.com/degabut/degabut-web/main/public/icons/colors/degabut-blue.png" width="32" />    |

## Spotify Support

Degabut by default only allows YouTube videos / playlists (and can't be disabled). Degabut `v5` adds Spotify support, but it is disabled by default, to enable it, simply pass

| Key                     | Description           |
| ----------------------- | --------------------- |
| `SPOTIFY_CLIENT_ID`     | Spotify Client ID     |
| `SPOTIFY_CLIENT_SECRET` | Spotify Client Secret |

to your `.env` file. Credentials can be obtained from [Spotify Developer Dashboard](https://developer.spotify.com/dashboard)

You can also add `VITE_SPOTIFY_INTEGRATION=true` to your `.env.web` file to enable Spotify integration on the web client (allows drag and drop Spotify links to the queue)

## Discord Activity

Discord Embedded Activity is [available as Public Developer Preview](https://discord.com/developers/docs/developer-tools/embedded-app-sdk). [Degabut Web Client](https://github.com/degabut/degabut-web) can be used as an embedded activity on Discord. To enable it:

1. Activate activity feature on your Discord application
2. Set this as the URL mappings configuration:

| Prefix                   | Target                    | Description                                          |
| ------------------------ | ------------------------- | ---------------------------------------------------- |
| `/_/gstatic/{subdomain}` | `{subdomain}.gstatic.com` | for google font                                      |
| `/_/ytimg/{subdomain}`   | `{subdomain}.ytimg.com`   | for youtube images                                   |
| `/_/scdn/{subdomain}`    | `{subdomain}.scdn.co`     | for spotify images if spotify integration is enabled |
| `/ws`                    | `ws://yourhost.com/ws`    | for websocket connection                             |
| `/api`                   | `http://yourhost.com/api` | for API connection                                   |
| `/` (**ROOT MAPPING**)   | `http://yourhost.com`     | root                                                 |

3. Pass `VITE_DISCORD_ACTIVITY_URL_MAPPINGS` to your `.env.web` file with the value of the URL mappings configuration in form of minified JSON array string for url patching, for example, your configuration:

```json
[
  {
    "prefix": "/_/gstatic/{subdomain}",
    "target": "{subdomain}.gstatic.com"
  },
  {
    "prefix": "/_/ytimg/{subdomain}",
    "target": "{subdomain}.ytimg.com"
  },
  {
    "prefix": "/_/scdn/{subdomain}",
    "target": "{subdomain}.scdn.co"
  },
  {
    "prefix": "/ws",
    "target": "yourhost.com/ws"
  },
  {
    "prefix": "/api",
    "target": "yourhost.com/api"
  }
]
```

The env value should be:

```
VITE_DISCORD_ACTIVITY_URL_MAPPINGS=[{"prefix":"/_/gstatic/{subdomain}","target":"{subdomain}.gstatic.com"},{"prefix":"/_/ytimg/{subdomain}","target":"{subdomain}.ytimg.com"},{"prefix":"/_/scdn/{subdomain}","target":"{subdomain}.scdn.co"},{"prefix":"/ws","target":"yourhost.com/ws"},{"prefix":"/api","target":"yourhost.com/api"}]
```
