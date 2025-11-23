# Locofly Ops – Picking & Putaway Web App

This is a small, in-house web app for **picking** and **putaway** that works
directly with your existing Node/Express backend:

- `GET /locations`
- `GET /inventory?location_id=...&query=...`
- `POST /inventory/adjust`

It uses the device camera and ZXing to scan barcodes and then adjusts inventory
quantities up (putaway) or down (picking).

## Files

- `index.html` – Single-page web app. Can be hosted on GitHub Pages or any static host.

## Configuration

Edit the `API_BASE` constant near the top of `index.html`:

```js
// const API_BASE = "https://locofly-api-xxxxxx.asia-south1.run.app";
const API_BASE = "http://localhost:8080";
```

Set it to the public URL of your Cloud Run service that serves the
`route.js` routes you shared earlier.

Example:

```js
const API_BASE = "https://locofly-api-123456-asia-south1.run.app";
```

Your backend must have CORS configured to allow the origin where you host this
HTML (e.g. your GitHub Pages domain).

## Usage

1. Open `index.html` in a browser on Android/iPhone (served from any static host).
2. Select a **location** from the dropdown (populated from `/locations`).
3. Choose **mode**:
   - **Picking** – decrements inventory (`delta` is negative)
   - **Putaway** – increments inventory (`delta` is positive)
4. Set **Quantity per scan** (usually 1).
5. Tap **Start Scanner**.
6. Scan a barcode:
   - The app calls `/inventory?location_id=...&query=<barcode>`
   - Picks the best match (exact barcode if available)
   - Sends `POST /inventory/adjust` with `{ location_id, items: [{ id, delta }] }`
   - Shows previous quantity, delta, and new quantity.
7. Check the **Event Log** at the bottom for a running history.

## Deploying on GitHub Pages

1. Create a new GitHub repository.
2. Add `index.html` and `README.md`.
3. Commit and push.
4. In repo settings, enable **GitHub Pages** → Source: `main` branch, root.
5. After it builds, open the GitHub Pages URL in your phone browser.

Make sure your backend CORS allows this origin.

## Notes

- This app does *not* implement pick-batch / order concepts. It is a
  **lightweight picking & putaway tool** layered on top of your current schema.
- All write operations go through `/inventory/adjust`, which uses your
  existing `quantity = quantity + delta` logic.
