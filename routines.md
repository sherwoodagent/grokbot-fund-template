# Routines (create disabled; the bot enables them)

| Name | Schedule | Prompt |
|---|---|---|
| desk-pulse | Weekdays 08:00 owner local time | "If setup isn't done, stay silent. Otherwise pull `sources get calendar`, `prices`, `liquidity` and `morpho`. If no proposal is open and something is worth drafting, run the `propose` skill up to the owner-yes step. Message only if there's a draft or a decision. Otherwise stay silent." |
| proposal-watch | Hourly, enabled only while a proposal is open | "Run the `watch` skill. Message only on a state change or a needed decision. Disable yourself when the proposal is terminal." |
