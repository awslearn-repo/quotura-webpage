# Quotura Access Pages

This repository serves the minimal public pages that the Quotura Chrome extension relies on to communicate access details. The extension is the only supported entry point, and every page encourages users to return to it. Quotura is fully free and has no paid plans.

## Deployed Routes

| Path | Purpose |
| --- | --- |
| `/` | Marketing site with install links and support contact. |
| `/plans` | Access overview showing guest vs logged-in features. |
| `/upgrade` | Legacy entry point from older upgrade flows; now explains that no payment is required. |
| `/billing` | Legacy entry point from older billing flows; now explains that no billing portal is needed. |
| `/success` | Confirmation page that reminds users to reopen the extension and sign in. |
| `/cancel` | Safe exit page when no changes are needed. |

Each page prominently links back to the extension and to `myquest.ai@gmail.com`.

## Chrome Extension Deep Link

- Extension ID: `mlkcccmkkcdhalkjnlabofccobpdjbio`
- Deep link format: `chrome-extension://mlkcccmkkcdhalkjnlabofccobpdjbio/main.html`
- The extension should place this link in a toolbar button so users can reopen it from the success and cancel pages.

## External Integrations

These pages are static and no longer create Stripe sessions. Any legacy upgrade or billing entry points simply guide users back to the extension.

## Local Development Notes

- The site is static; open the HTML files directly or serve with any static server.
- Extension deep links only work when the Quotura extension is installed locally.
- Pages are content-only and do not call external APIs.

## Support

If anything looks off in production, email `myquest.ai@gmail.com` or contact the Quotura extension team.

