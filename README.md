# LaunchLab — Shopify AI Product Studio
**Portfolio project by May Anne Guevarra**

A product automation dashboard that turns a product brief into reviewable content and creates a Shopify **draft** through a server-side API integration.

## Demo status
- **Working sample mode:** deterministic product copy, storefront preview, JSON payload inspector and browser-local mock draft receipts. No external API calls.
- **Implemented, not live-verified:** Claude generation and Shopify draft creation. Both require your own credentials and a development store.
- This is a custom automation studio, not a Shopify theme or checkout.
- Hosted preview (currently owner-private): https://launchlab-shopify-anne.russetspoonbill.chatgpt.site
- Other portfolio: https://yhampower.vercel.app and https://buildison-react.vercel.app

## Workflow
1. Enter the product name, vendor, type, verified features, target audience and tone.
2. Generate content in sample mode or via Claude.
3. Review the description, bullets, tags, SEO fields and JSON payload.
4. Create a mock draft, or confirm creation of one real DRAFT in a configured Shopify development store.
5. Verify the product in Shopify Admin. The app does not publish to a sales channel.

## Stack
| Tool | Role |
| --- | --- |
| HTML / CSS / JavaScript | Responsive dashboard, forms and storefront preview |
| Cloudflare Workers | Backend routes and server-only secrets |
| Claude Messages API | Product content generation with JSON validation |
| Shopify GraphQL Admin API | productCreate with status DRAFT |
| localStorage | Sample draft receipts only |

## Run locally
Requires Node.js and Cloudflare Wrangler.
```sh
git clone https://github.com/annevillanueva1122-design/shopify-ai-product-studio.git
cd shopify-ai-product-studio
npx wrangler dev
```
Open the localhost URL printed by Wrangler. Sample mode needs no credentials.

## Connect APIs
Copy `.dev.vars.example` to `.dev.vars` for local use and populate the values. Never commit `.dev.vars`, access tokens or API keys.

| Environment variable | Meaning |
| --- | --- |
| ANTHROPIC_API_KEY | Private Claude API key |
| ANTHROPIC_MODEL | Model ID available to your account |
| SHOPIFY_STORE_DOMAIN | your-store.myshopify.com (no https or path) |
| SHOPIFY_ADMIN_TOKEN | Valid Shopify Admin access token authorized for write_products |
| SHOPIFY_API_VERSION | Supported version; default 2026-10 |

Use a Shopify development store and Shopify's currently supported app authentication flow. For hosted Workers, configure secrets through your provider's secret settings. Authenticate/protect your hosted deployment before configuring real secrets: same-origin checks alone are not authentication. The provided public source does not include user authentication or durable rate limiting.

Select **Live** in the studio, generate, review and create a draft. Check Shopify Admin → Products before retrying any timed-out creation request: automatic duplicate protection is not implemented.

## API routes
`GET /api/status` reports configuration presence, not credential validity.

`POST /api/generate`
```json
{"name":"Terra Bottle","brand":"TERRA","category":"Drinkware","features":"750ml; stainless steel","audience":"Commuters","tone":"Minimal & thoughtful"}
```
Returns content with title, description, bullets, tags, seoTitle and seoDescription.

`POST /api/shopify/draft` accepts `{content, brand, category}` and returns the created product ID, title, status and handle.

## Code layout
- `worker.js`: self-contained UI and Worker API handlers; no runtime packages.
- `wrangler.toml`: local development / independent Worker deployment.
- `.dev.vars.example`: secret names with placeholders.
- `.gitignore`: excludes local secrets and generated state.

## Validation performed
JavaScript syntax and Worker artifact checks; mocked Claude success and malformed JSON rejection; missing-secret errors; cross-origin rejection; Shopify payload mapping, DRAFT status and HTML escaping.
Live Claude/Shopify calls and a full visual/browser QA pass have not been performed.

## Limitations
No checkout, payment, image generation, inventory, variants/prices, durable audit logs, idempotency, Meta/TikTok API launching or AI video editing. The bottle graphic is a CSS illustration and stays the same for all products. Generated product claims require human review.

## Demo walkthrough
Show the brief → generate → JSON → storefront preview → draft receipt. State explicitly when using sample mode. Once real credentials are connected and tested, show the actual Shopify draft in Admin.

## References
- https://shopify.dev/docs/api/admin-graphql/latest/mutations/productCreate
- https://platform.claude.com/docs/en/api/messages/create
