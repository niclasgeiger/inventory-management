# Known Issues

Found during Playwright smoke test on 2026-05-20.

## 1. Missing `/api/tasks` endpoint (functional)

**Error:** `GET http://localhost:8001/api/tasks` → 404  
**Source:** `client/src/App.vue:38`  
**Impact:** Fires on every page load; any tasks-related UI feature silently fails.  
**Fix:** Either implement `GET /api/tasks` in `server/main.py`, or remove the call from `App.vue` if tasks are not yet planned.

## 2. Missing favicon (cosmetic)

**Error:** `GET http://localhost:3000/favicon.ico` → 404  
**Fix:** Add a `favicon.ico` (or `.png`) to `client/public/`.
