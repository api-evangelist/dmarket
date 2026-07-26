---
name: Place a buy order (target) on DMarket
description: Check aggregated market prices, create a buy target for an item title, and manage open targets.
api: openapi/dmarket-trading-openapi.yml
operations: [MarketAPI_ListAggregatedPrices, CreateTargets, GetUserTargetsV2, DeleteTargets]
---

# Place a buy target on DMarket

A "target" is a standing buy order at a price you set for a given item title.

## Authentication

Signed-request headers `X-Api-Key`, `X-Sign-Date`, `X-Request-Sign` on every call (see `authentication/dmarket-authentication.yml`). Base URL `https://api.dmarket.com`.

## Steps

1. **Price the market** — call `MarketAPI_ListAggregatedPrices` (`POST /marketplace-api/v1/aggregated-prices`) for the item titles to see best offer/target prices.
2. **Create the target** — call `CreateTargets` (`POST /marketplace-api/v1/user-targets/create`) with the game, title, price (`entity.Money`, USD cents) and amount.
3. **List open targets** — call `GetUserTargetsV2` (`GET /marketplace-api/v2/user/targets`) to confirm; capture each `TargetID`.
4. **Cancel** — call `DeleteTargets` (`POST /marketplace-api/v1/user-targets/delete`) with the `TargetID`(s) to remove.

## Conventions & errors

- Cursor pagination on target lists (see `conventions/dmarket-conventions.yml`).
- Requires sufficient balance to back the target (`NotEnoughBalance`, `InsufficientFunds`); `TitleRequired`/`GameIDRequired` guard missing fields (`errors/dmarket-problem-types.yml`).
