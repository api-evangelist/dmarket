---
name: Deposit and list an inventory item for sale on DMarket
description: Sync Steam inventory, deposit an asset to DMarket, and confirm it is available to list.
api: openapi/dmarket-trading-openapi.yml
operations: [UserInventorySync, GetUserInventoryV2, DepositAssets, GetDepositStatus]
---

# Sell inventory on DMarket

Move an item from your linked game inventory into DMarket so it can be listed for sale.

## Authentication

Signed-request headers `X-Api-Key`, `X-Sign-Date`, `X-Request-Sign` on every call (see `authentication/dmarket-authentication.yml`). Base URL `https://api.dmarket.com`.

## Steps

1. **Sync** — call `UserInventorySync` (`POST /marketplace-api/v1/user-inventory/sync`) to pull the latest items from the linked Steam inventory.
2. **List inventory** — call `GetUserInventoryV2` (`GET /marketplace-api/v2/user/inventory`) to enumerate items; capture the `AssetID` of the item to sell.
3. **Deposit** — call `DepositAssets` (`POST /marketplace-api/v1/deposit-assets`) with the asset(s). Capture the returned `DepositID`.
4. **Confirm** — poll `GetDepositStatus` (`GET /marketplace-api/v1/deposit-status/{DepositID}`) until the deposit completes before creating offers.

## Conventions & errors

- The game must be linked to your account (`GameIsNotLinkedToYourAccount`) and assets must not be duplicated (`DuplicatedAssets`).
- Watch for `InventoryItemsNotFound`, `InvalidTransferAssetCount`, and `UnsupportedInventorySyncType` in the `{code, message}` envelope (`errors/dmarket-problem-types.yml`).
