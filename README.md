# Canvas Bridge (wbapp)

Heroku relay + Chrome extension so an **iPad** can sync Canvas course data through a laptop logged into Canvas — **no Canvas access token**.

This repo hosts the relay that [wbapp](https://github.com/chughjug/wbapp) and the Chrome extension talk to.

```
iPad wbapp ──POST job──► this Heroku app ◄──poll── Chrome extension (Canvas cookies)
                │                                    │
                └──────── GET payload ◄── upload ────┘
```

## Deploy to Heroku

```bash
heroku create your-canvas-bridge   # or: heroku git:remote -a <existing-app>
git push heroku main
heroku open
curl https://YOUR-APP.herokuapp.com/health
```

Then set that URL as `VITE_CANVAS_BRIDGE_URL` on the wbapp Vercel project, and as the Bridge URL in the Chrome extension popup.

## Chrome extension

Load `chrome-extension/canvas-bridge` unpacked in `chrome://extensions`. Point it at this Heroku URL. Keep a Canvas tab open while syncing.

## API

| Method | Path | Who |
| --- | --- | --- |
| POST | `/v1/rooms` | Extension — create pair room |
| POST | `/v1/rooms/join` | iPad — `{ pairCode }` |
| POST | `/v1/rooms/:id/jobs` | iPad — `{ courseId? }` |
| GET | `/v1/rooms/:id/jobs/:jobId` | iPad — poll until `ready` |
| GET | `/v1/rooms/:id/jobs/waiting` | Extension |
| POST | `/v1/rooms/:id/jobs/:jobId/complete` | Extension — upload payload |

Single-dyno in-memory store (12h rooms, 30m jobs).

## Local

```bash
npm install
npm start   # :8787
npm test
```
