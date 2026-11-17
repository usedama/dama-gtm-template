# DAMA Analytics tag for Google Tag Manager

This Google Tag Manager tag template loads the [DAMA](https://usedama.com) Web SDK. It sends page views, sessions, ecommerce events, custom events and customer details to DAMA.

Install it from the Community Template Gallery: in your web container, open **Templates → Tag Templates → Search Gallery** and search for **DAMA Analytics**.

## Before you start

You need these values from DAMA:

- **Organization ID.** In the DAMA panel, open your company and go to **Integration → SDK → Web**.
- **Tracking Token**, from DAMA.
- **Data location.** Choose **EU**, unless DAMA gave you a dedicated collection URL, for example `https://collect.yourbrand.com`. In that case, choose **Own collection URL** and enter it.

The tag loads the SDK from `https://cdn.usedama.com/dama.js`. The SDK's cookies are first-party cookies on your site.

## Set up

1. **Base tag.** Create a DAMA Analytics tag with **Tag type: Base tag**, fill in **DAMA Settings**, and fire it on **Initialization - All Pages**.
   - It loads the SDK, which sends `page_view` automatically, including page changes in single-page apps.
   - With **Send session_start** on (the default), it also sends `session_start` when a page load starts a new session. Retention Analysis needs this event.
2. **Ecommerce events.** Add one tag per event: View Item List, View Item, Add to Cart, Remove from Cart, View Cart, Begin Checkout, Add Payment Info and Purchase.
   - Fire each tag on the matching custom event trigger, for example `purchase`.
   - **Read ecommerce data from the data layer** (on by default) takes `currency`, `value`, `items` and `transaction_id` from your GA4 `ecommerce` object.
   - Turn it off to map the fields yourself.
3. **Customers.** Use **Identify Customer** after login or signup.
   - Begin Checkout, Add Payment Info and Purchase also accept **Customer Details**.
   - Email, phone and names are hashed with SHA-256 in the browser. DAMA never receives them in plain text.
4. **Custom events.** Use **Custom Event** with your own event name and parameters.

Event tags don't need tag sequencing. If an event fires while the base tag is still loading the SDK, the tag waits for that load and then sends the event.

## Consent

DAMA records the visitor's consent with each event as three flags: essential, ads and analytics. The **ads** flag decides who is uploaded when you send an audience to Google Ads.

| Consent setting | Ads | Analytics |
|---|---|---|
| Read from Google Consent Mode (default) | `ad_user_data` and `ad_personalization` granted | `analytics_storage` granted |
| Set manually | your variable returns `true` or `granted` | your variable returns `true` or `granted` |
| Don't send consent | not sent | not sent |

A consent type your site never sets counts as granted, the same as in Google's own tags.

The template does not block tracking. To fire DAMA only after consent, use **Consent Settings → Require additional consent** on the tags.

## Data the template reads and writes

| Permission | Used for |
|---|---|
| Injects scripts from `https://cdn.usedama.com/*` | loads the DAMA SDK |
| Reads, writes and calls the globals `damaDP`, `damaDP.q` and `damaDPInitInProgress` | sends events through the SDK |
| Reads the data layer keys `ecommerce` and `ecommerce.*` | GA4 ecommerce data |
| Reads the cookie `damadp_session_id` | detects a new session |
| Reads the consent states `ad_user_data`, `ad_personalization` and `analytics_storage` | consent flags |
| Template storage | shares the SDK load between the base tag and event tags on a page |
| Logs to the console | debug and preview mode only |

## Support

Open an issue in this repository, or see the [DAMA documentation](https://docs.usedama.com/setup/web).

## For maintainers: releasing a version

1. Commit the new `template.tpl` and push it to `main`.
2. Copy that commit's full SHA.
3. Add it as the first entry under `versions` in `metadata.yaml`, with `changeNotes`.
4. Commit and push that change. The gallery usually picks it up in 2 to 3 days.

For the first release, the gallery reads `metadata.yaml` when the repository is submitted. Replace the placeholder SHA before you click **Submit Template**.

To add a data location, for example US:

1. Add its address to `REGION_COLLECT_URLS` in the template code.
2. Add an option with the same value to the **Data location** field.
3. Release a new version.

Existing tags keep their location.
