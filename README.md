# tgcom24-epg

An XMLTV guide for the Italian news channel **TGCOM24**, rebuilt every six hours by a GitHub Action.

Mediaset publishes no XMLTV feed for it. The schedule comes from the official DTT guide
**Tivù la Guida**, which exposes it as JSON — the same source the sister repos use, and sturdier
than parsing a guide page's HTML.

## Use it

| | |
|---|---|
| **EPG URL** | `https://raw.githubusercontent.com/sasagr/tgcom24-epg/main/tgcom24.xml` |
| **tvg-id** | `TGCOM24.it` |
| **Logo** | `https://raw.githubusercontent.com/tv-logo/tv-logos/main/countries/italy/tgcom24-it.png` |

The tvg-id must match exactly: XMLTV consumers tie a programme to a channel by the `channel=`
attribute, so a mismatch shows the channel with an empty guide rather than an error.

Seven days ahead, times in `Europe/Rome` with real offsets (`+0200` in summer, `+0100` in winter).

## How it works

`generate.py` asks the Tivù API for one day at a time (channel id **118**, listed there as
"TGCOM24 HD"), de-duplicates events that repeat across day windows, and writes `tgcom24.xml`. It is
stdlib-only, so the Action needs no `pip install`.

The workflow commits the file only when it changes, and can be run by hand from the Actions tab.

## If the guide goes empty

Almost always one of two things:

- **The Tivù channel id moved.** Fetch the API for today and look for a channel whose name contains
  "TGCOM" — if the id is no longer 118, change `TIVU_CHANNEL_ID` in `generate.py`.
- **The app's tvg-id no longer matches `CHANNEL_ID`.** They have to be the same string.

The script exits non-zero when it produces no programmes, so a broken run shows as a failed Action
rather than quietly publishing an empty guide.
