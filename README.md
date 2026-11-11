# Relicmark Ledger for Claude

**Status on 2026-11-11: loads and connects in Claude Code; no recorded price answer yet.** The files pass Claude Code's plugin validator, and Claude Code 2.1.295 loaded this plugin, listed its skill and connected to `https://relicmark.com/mcp/` (connector 2.5.0). Nobody has yet recorded a price question answered through it, so relicmark.com still lists Claude as coming soon. What follows describes what the plugin is built to do.

Ask Claude what a graded trading card is worth and get Relicmark's answer: a composite price built only from verified, source-linked sales of that exact card and grade, with the sale rows it rests on and a link to the public ledger page.

Relicmark keeps one ledger per card and grade, for example the 2019-20 Upper Deck Young Guns #201 Jack Hughes in PSA 10. There are hundreds of ledgers: mostly Upper Deck Young Guns hockey rookies in PSA 10, with some basketball, football and older baseball cards and a few Pokémon cards. Many of them have no qualifying sale yet; those show no price rather than a guess.

The composite is an estimate. It is not an appraisal, not a guarantee of what a card will sell for, and not authentication of any physical card, grade or owner.

## What is in this plugin

- A connection to Relicmark's public, read-only server at `https://relicmark.com/mcp/`. It needs no account, no sign-in and no key.
- One skill, `relicmark-ledger`, that tells Claude how to search, when to fetch, and how to word a price honestly.

The plugin holds no scripts, hooks or executables. It is three small JSON files (the plugin manifest, the marketplace manifest and the server address), one Markdown skill, the listing icon, this README and the license.

## Install

In Claude Code, add this repository as a marketplace, then install the plugin from it. This repository is `Conviviera/relicmark-claude-plugin`; if the short name asks for SSH access on your machine, give the full address instead, `https://github.com/Conviviera/relicmark-claude-plugin`.

```text
/plugin marketplace add Conviviera/relicmark-claude-plugin
/plugin install relicmark-ledger@relicmark
```

The same two steps from a shell:

```text
claude plugin marketplace add Conviviera/relicmark-claude-plugin
claude plugin install relicmark-ledger@relicmark
```

Then ask in plain words, for example "What is the Connor McDavid Young Guns PSA 10 worth on Relicmark?" You can also paste a relicmark.com card link or a ledger code such as RM-UD1920-YG201-PSA10.

## The two tools

**search** takes one card per query: a player or Pokémon name, with the season or year, set, card number and grade when you have them. It returns up to 20 ranked results, each with an id, a title and the ledger's address, plus the total number of matches. A ledger's title may end with a short preview of its composite. A grade in the query is matched exactly: if Relicmark has the card only at another grade, search returns no match and lists that other grade separately, so one grade's price is never passed off as another's. A near miss in spelling comes back as a close result, not as a match. Search also returns labelled research references, which are cards named in issuers' public filings. They are not ledgers and carry no price.

**fetch** takes an id from search, a relicmark.com card link or a ledger code, and returns that ledger's record:

- the composite: its value, confidence (high, medium, low or none), low and high, the number of counted sales, the date of the newest counted sale, and a status of ok, stale or insufficient;
- counts of the sale records on file, and how many were counted, pending, held, excluded, too old, outliers or not in US dollars;
- up to 30 sale rows, newest first, each with its date, venue, price, currency, status, whether it was counted, the reason if it was not, and a link to the page that reports it;
- asking prices when a ledger lists any (they are listings, not sales, and are never counted), corrections, and the ledger's code and address.

Both tools only read. Neither can add, save, change or delete anything.

## What the skill asks Claude to print

Claude Code does not draw the ledger card that some other hosts show, so the skill has Claude write the evidence out: one sentence with the estimate, its confidence, its range when there is one, the number of counted sales and the date of the newest one; a short table of the newest counted sales with a source link on every row; a line of counts; the reason for each held or excluded row among those returned (older rows are on the ledger page); and the ledger link.

## Limits

- Coverage is the published ledgers only. If Relicmark has no ledger for a card, Claude says so and does not substitute a price from somewhere else.
- One card and one exact grade per ledger. A PSA 10 ledger says nothing about a PSA 9.
- A composite that rests on a single sale has no range, and one whose newest counted sale is more than six months old is marked stale. A ledger with no qualifying sale in the last 18 months has no composite.
- Only verified US-dollar sales are counted. Pending, held and excluded rows are listed for transparency and never counted. A held row is one whose source has not given written permission to reuse its records.
- The connection can lag the website. The ledger page is the current record.
- It cannot see or change accounts, collections, watchlists or listings.
- It gives no buying, selling or investment advice, and it authenticates nothing.

## Privacy

The plugin itself collects nothing: it contains no code, stores nothing on your machine beyond its own files, and asks for no credentials. When Claude uses it, your search words or the ledger id are sent to `https://relicmark.com/mcp/` so the server can answer. That server is public and read only: it sets no cookies, discards any cookie or authorization header it is sent, and has no account to tie a request to. At the time of writing, like most web servers, it keeps a short-lived access log line for each request (network address, the address requested and the client's user agent, not the search words), which is deleted after about ten days, and a daily count of connector calls that holds no address. Relicmark's privacy policy is at https://relicmark.com/privacy/ and its terms are at https://relicmark.com/terms/. What Claude itself does with your conversation is covered by Anthropic's terms, not by this plugin.

## Support

Questions, corrections to a ledger, or a problem with the plugin: open an issue at https://github.com/Conviviera/relicmark-claude-plugin/issues. Setup notes for AI assistants are at https://relicmark.com/cards/ai/.

## License

MIT. See the LICENSE file. Copyright (c) 2026 Conviviera Corp. The license covers the files in this plugin; it does not cover the ledger data the server returns.
