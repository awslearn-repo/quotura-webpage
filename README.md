# Quotura Billing Pages

This repository serves the minimal public pages that the Quotura Chrome extension relies on to complete Stripe checkout and billing flows. The extension is the only supported entry point for upgrades, and every page encourages users to return to it once Stripe finishes.

## Deployed Routes

| Path | Purpose |
| --- | --- |
| `/` | Static notice that upgrades start inside the extension. Provides install/open links and support contact. |
| `/upgrade` | Opened by the extension with `plan` and `token` parameters. Shows the selected plan, calls the checkout session API, and redirects immediately to Stripe. |
| `/billing` | Opened by the extension to manage an existing subscription. Calls the billing portal API and redirects to Stripe. |
| `/success` | Landing page after a successful Stripe checkout. Instructs the user to reopen the extension. |
| `/cancel` | Landing page when a Stripe flow is cancelled. Recommends relaunching the extension to try again. |

Each page prominently links back to the extension and to `myquest.ai@gmail.com`.

## Chrome Extension Deep Link

- Extension ID: `mlkcccmkkcdhalkjnlabofccobpdjbio`
- Deep link format: `chrome-extension://mlkcccmkkcdhalkjnlabofccobpdjbio/main.html`
- The extension should place this link in a toolbar button so users can reopen it from the success and cancel pages.

## Stripe API Integration

Both the upgrade and billing pages rely on Quotura’s backend to create Stripe sessions. The extension must supply a JWT via query params or injected globals so the pages can add the `Authorization` header required by the backend.

### Required Headers

```
Authorization: Bearer <JWT from the Quotura extension>
Content-Type: application/json
```

### Checkout Session (`/upgrade`)

- **Endpoint:** `POST https://quotura.imaginetechverse.com/api/create-checkout-session`
- **Request body:**

```json
{
  "plan": "monthly"
}
```

Accepted plan values: `"monthly"` or `"yearly"` (default).

- **Successful response:**

```json
{
  "url": "https://checkout.stripe.com/c/pay/cs_test_123"
}
```

- **Error response:**

```json
{
  "error": "Plan is inactive"
}
```

On success we immediately `window.location.replace(data.url)`. Errors display inline instructions with retry and extension deep links.

### Billing Portal Session (`/billing`)

- **Endpoint:** `POST https://quotura.imaginetechverse.com/api/billing/create-portal-session`
- **Request body:** `{}` (empty object)
- **Successful response:** same `{ "url": "<stripe_portal_url>" }`
- **Error response:** `{ "error": "<message>" }`

The billing page follows the same loading > redirect > error-handling pattern as the checkout page.

### JWT Discovery

The pages look for an auth token in this order:

1. `token`, `jwt`, or `auth` query parameters provided by the extension
2. Window globals: `window.__QUOTURA_JWT__`, `window.__QUOTURA_TOKEN__`, `window.QUOTURA_AUTH_TOKEN`
3. `sessionStorage.getItem("quoturaAuthToken")`

If no token is found, the pages show an actionable error asking the user to reopen the extension.

## Redirect Destinations in the Backend

The backend is configured with:

- `SUCCESS_URL = https://quotura.imaginetechverse.com/success`
- `CANCEL_URL = https://quotura.imaginetechverse.com/cancel`
- `PORTAL_RETURN_URL = https://quotura.imaginetechverse.com/billing`

If any of these ever change, let the backend team know so they can update the environment variables accordingly.

## Local Development Notes

- The site is static; open the HTML files directly or serve with any static server.
- To simulate the extension, append `?plan=monthly&token=dummy` to `/upgrade`. Replace `dummy` with a valid JWT when testing against the live backend.
- The pages guard against missing auth by showing clear errors and providing support/deep links even when tests are run in isolation.

## Support

If anything looks off in production, email `myquest.ai@gmail.com` or contact the Quotura extension team.

