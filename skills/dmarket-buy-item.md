---
name: Buy an item on the DMarket marketplace
description: Check balance, find a marketplace offer, and buy it on DMarket.
api: openapi/dmarket-trading-openapi.yml
operations: [getUserBalance, GetMarketplaceOffersV2, buyOffers]
---

# Buy an item on DMarket

Purchase an in-game item that is currently listed for sale on the DMarket marketplace.

## Authentication

Every request needs three headers (see `authentication/dmarket-authentication.yml`):

- `X-Api-Key` — your lowercase-hex public key from account settings.
- `X-Sign-Date` — current Unix timestamp in seconds (must be within 2 minutes of server time).
- `X-Request-Sign` — `dmar ed25519 ` + hex Ed25519 signature over `method + path(+query) + body + X-Sign-Date`.

Base URL: `https://api.dmarket.com`.

## Steps

1. **Check funds** — call `getUserBalance` (`GET /account/v1/balance`). Confirm the USD balance (`entity.Money`, amount in cents) covers the item price.
2. **Find an offer** — call `GetMarketplaceOffersV2` (`GET /marketplace-api/v2/offers`) filtered by game and item title. Capture the target offer's `OfferID` and current `price`.
3. **Buy** — call `buyOffers` (`PATCH /exchange/v1/offers-buy`) with the `OfferID` and the exact `price` you observed. The price must match or the request is rejected.

## Conventions & errors

- Pagination is cursor-based (`Cursor`/`cursor`, `Limit`/`limit`) — see `conventions/dmarket-conventions.yml`.
- No idempotency key: do not blindly retry `buyOffers` — re-check balance/offer state first.
- Errors return `{code, message}` (see `errors/dmarket-problem-types.yml`). Watch for `NotEnoughBalance`, `InsufficientFunds`, and `InvalidAmount`.
