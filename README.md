# Grocery API Postman Tests

Postman test collection for the public [Simple Grocery Store API](https://simple-grocery-store-api.click), run automatically with Newman in GitHub Actions.

## What is tested

- **Status**: health check, plus negative cases (wrong method, wrong path, wrong letter case)
- **Products**: query parameters `results`, `category`, `available`, and the single product endpoint (`productId`, `product-label`), with positive and negative cases
- **Cart**: create cart, add item, get cart (positive flow, `cartId` and `itemId` are chained through environment variables)

Test design follows the BINMEN mnemonic (Boundary, Invalid, Nulls, Method, Empty, Negatives). Each request has its own assertions for status code and response content.

Work in progress: Cart negative cases, cart item update/delete, Orders and Auth are still being added.

## Known Issues found

Expected results come from the API documentation. Where the real API behaves differently, the request keeps its failing test and is marked `[Known Issue]` with an explanation in its Docs tab.

| Request | Expected | Actual |
|---|---|---|
| `GET /products?results=0` | 400 Bad Request | 200 OK, full product list |
| `GET /products?results=abc` | 400 Bad Request | 200 OK, full product list |
| `GET /products?results=` (empty) | 400 Bad Request | 200 OK, full product list |
| `GET /products?results=null` | 400 Bad Request | 200 OK, full product list |
| `GET /products?category=` (empty) | 400 Bad Request | 200 OK, full product list |
| `GET /products?available=` (empty) | 400 Bad Request | 200 OK, full product list |

Note: `results=21` and `results=-1` are correctly rejected with 400, so only some invalid values are validated.

## CI/CD

A GitHub Actions workflow runs the collection with Newman on every push and uploads an HTML report (`newman-report` artifact). The 6 Known Issue tests fail on purpose, so the run uses `--suppress-exit-code` to keep the pipeline green while the failures stay visible in the report.

The public demo API occasionally returns a temporary 500 error. This was observed once in CI and did not repeat on re-run.

## Run locally

```
npm install -g newman newman-reporter-htmlextra
newman run "Simple Grocery Store API.postman_collection.json" -e "Grocery API - Local.postman_environment.json" --env-var "baseUrl=https://simple-grocery-store-api.click" -r cli,htmlextra
```# Grocery-API-Postman-Tests
Postman API test collection for Simple Grocery Store API, run with Newman
