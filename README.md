[![](https://img.shields.io/github/issues/lucatnt/telegram-bot-amazon.svg)](https://github.com/LucaTNT/telegram-bot-amazon/issues) [![](https://img.shields.io/github/issues-pr-raw/lucatnt/telegram-bot-amazon.svg)](https://github.com/LucaTNT/telegram-bot-amazon/pulls) [![](https://img.shields.io/docker/pulls/lucatnt/telegram-bot-amazon.svg)](https://hub.docker.com/r/lucatnt/telegram-bot-amazon) [![](https://img.shields.io/docker/cloud/build/lucatnt/telegram-bot-amazon.svg)](https://hub.docker.com/r/lucatnt/telegram-bot-amazon) [![](https://img.shields.io/docker/image-size/lucatnt/telegram-bot-amazon/latest.svg)](https://hub.docker.com/r/lucatnt/telegram-bot-amazon)

This is a Telegram bot that, if made admin of a group, will delete any message
containing an Amazon link and re-post it tagged with the specified affiliate tag.

It can be either messaged directly, or added **as an administrator** to a group or supergroup.

If messaged directly, it replies with the affiliate link, while in a group it will delete any message containing an Amazon link and replace it with a new message, with a format that is customizable through the `GROUP_REPLACEMENT_MESSAGE` environment variables.

## Configuration

It requires two parameters through environment variables:

- `TELEGRAM_BOT_TOKEN` (required) is the token obtained from [@Botfather](https://t.me/botfather).
- `AMAZON_TAG` (required) is the Amazon affiliate tag to be used when rewriting URLs.

You can set two optional parameters through environment variables:

- `SHORTEN_LINKS`: if set to `"true"`, all the sponsored links generated by the bot will be passed through the bitly shortener, which generates amzn.to links.
- `BITLY_TOKEN` (required if `SHORTEN_LINKS` is `"true"`) is the [Generic Access Token](https://bitly.is/accesstoken) you can get from bitly.
- `AMAZON_TLD` is the Amazon TLD for affiliate links (it defaults to "com", but you can set it to "it", "de", "fr" or whatever).
- `GROUP_REPLACEMENT_MESSAGE` specifies the format for the message that gets posted to groups after deleting the original one. If not set, it will default to `Message by {USER} with Amazon affiliate link:\n\n{MESSAGE}`. In the following table you'll find variables you can use.
- `RAW_LINKS`: if set to `"true"` disables this bot's "URL beautifier" (which removes all the URL parameters aside from the ASIN and the affiliate tag) and just adds/replaces the tag to the URL. This allows to link to arbitrary pages on Amazon, even non-product ones (e.g. search pages, category pages, etc.)
- `CHECK_FOR_REDIRECTS`: if set to `"true"` the bot will look for redirects in the provided links. This allows it to find links to Amazon even if they are hidden behind URL shorteners. Please note that this check requires a bit more time than looking for "regular" Amazon URLs, since the bot needs to connect to each URL.
- `CHECK_FOR_REDIRECT_CHAINS`: if set to `"true"` the bot will perform a recursive search for redirects. This allows it to catch Amazon URLs even if they are hidden behind a chain of redirects. This will slow down the processing of redirects.
- `MAX_REDIRECT_CHAIN_DEPTH`: if `CHECK_FOR_REDIRECT_CHAINS` is enabled, it will limit the number of redirect levels the bot will try to go through. It default to 2, and it should be kept at a low number to avoid wasting time going through endless redirect chains a user might provide.
- `IGNORE_USERS`: a comma-separated list of usernames (starting with the "@" character) and numeric user IDs whose messages won't be acted upon by the bot, even if they contain matching Amazon links. A valid list would be `"@Yourusername,12345678,@IgnoreMeAsWell123"`. Numeric user IDs are useful for users who do not have Telegram user names defined. You can get yours by contacting [userinfobot](https://t.me/useridinfobot).

| String               | Replacement                                                                                                                |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `{USER}`             | The user that posted the message, as `@username` if they created a Telegram username, as `first_name last_name` otherwise. |
| `{ORIGINAL_MESSAGE}` | The user's original message, with no replacements (so it will contain the non-affiliated Amazon link).                     |
| `{MESSAGE}`          | The user's message, with the affiliated Amazon link the bot created.                                                       |

## Running the app

You can either run the app directly through NodeJS

    TELEGRAM_BOT_TOKEN=your-token AMAZON_TAG=your-tag node index.js

Or you can run it in Docker

    docker run -e TELEGRAM_BOT_TOKEN=your-token -e AMAZON_TAG=your-tag --init darkfant/refbottg

Note that the `--init` option is highly recommended because it allows you to stop the container through a simple Ctrl+C when running in the foreground. Without it you need to use `docker stop`.
