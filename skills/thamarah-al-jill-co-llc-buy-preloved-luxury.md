---
name: Buy pre-loved luxury from AMUSED Co
description: >-
  Discover, cart, and check out authenticated pre-owned luxury items from the AMUSED Co
  store using its Universal Commerce Protocol (UCP) MCP surface, with mandatory buyer
  approval at payment.
api: mcp/thamarah-al-jill-co-llc-mcp.yml
operations: [search_catalog, create_cart, create_checkout, update_checkout, complete_checkout]
source: https://amusedco.com/llms.txt
method: generated
---

# Buy pre-loved luxury from AMUSED Co (UCP / MCP)

AMUSED Co is an authenticated pre-owned luxury marketplace (bags, clothing) built on
Shopify and exposed to agents via the Universal Commerce Protocol. Use the MCP endpoint
at `https://amusedco.com/api/ucp/mcp` (JSON-RPC over HTTP). All tool names below are the
documented UCP shopping flow from the store's `/llms.txt`.

## Preconditions
- Discover capabilities first: `GET https://amusedco.com/.well-known/ucp`.
- Read-only product browsing needs no auth (e.g. `GET /products/{handle}.json`).
- Transacting requires the buyer to be reachable for **explicit payment approval**.

## Steps
1. **Discover** — `GET /.well-known/ucp` and confirm `dev.ucp.shopping` is offered over MCP.
2. **Search** — call `search_catalog` with the buyer's intent. Pass buyer context
   (`context.address_country`, `context.currency`) for accurate pricing/availability.
3. **Cart** — call `create_cart` to add the chosen item(s).
4. **Checkout** — call `create_checkout` to start the purchase from the cart.
5. **Fulfill** — call `update_checkout` to set shipping address and method.
6. **Complete** — call `complete_checkout` **only after obtaining the buyer's explicit,
   contemporaneous consent to the final amount**. Never complete payment without it.

## Rules grounded in the store's published conventions
- **Buyer approval is mandatory** at payment. If you cannot get contemporaneous approval,
  do not call `complete_checkout` — route through Shop Pay via the Shop skill
  (`https://shop.app/SKILL.md`) instead.
- **Respect rate limits** — back off on HTTP `429` from the MCP endpoint.
- Authentication for customer-account operations uses Shopify Customer Accounts OIDC
  (see `authentication/thamarah-al-jill-co-llc-authentication.yml`).
