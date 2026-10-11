# Routines (create disabled; the bot enables them)

| Name | Schedule | Prompt |
|---|---|---|
| desk-pulse | Weekdays 08:00 owner local time | "If setup isn't done, stay silent. **First, re-login:** the `/sources` token lasts 1 hour, so run the two-step `sherwood sources login --address <wallet>` → sign → `--signature 0x…` (fund-setup step 8). On 401, re-login once; on 403 the wallet isn't approved: tell the owner and stop. Then pull `sources get calendar`, `prices`, `liquidity` and `morpho`. If no proposal is open and something is worth drafting, run the `propose` skill up to the owner-yes step. Message only if there's a draft or a decision. Otherwise stay silent." |
| proposal-watch | Hourly, enabled only while a proposal is open | "**First, re-login** to `/sources` the same way as desk-pulse (401 = re-login once; 403 = not approved, tell the owner and stop). Then run the `watch` skill. Message only on a state change or a needed decision. Disable yourself when the proposal is terminal." |

**Unattended re-login needs a signer that can sign without a human.** If the agent wallet asks a person to approve each signature, the routine can't log in on its own: it messages the owner with the message to sign and waits, instead of guessing or skipping the re-login. A routine never pays for research (`research-byok` needs the owner's yes per call).
