# Firefox TinyFish

A Firefox-derived browser project focused on goal-driven automation, with a local control API designed for an agent such as ChatGPT to operate the browser.

> Status: bootstrap repository. This repository is not yet a source-code fork of Mozilla Firefox. The official Firefox source is maintained at https://github.com/mozilla-firefox/firefox. GitHub's fork operation must be performed separately before this repository can contain the full Firefox source history.

## Goals

- Goal-based browsing: navigate a site, inspect the current page, interact with page elements, and verify results.
- Safe DOM actions: use locators and visible/accessible page semantics rather than brittle coordinates when possible.
- Localhost control API: expose a small, opt-in API for status, current URL, title, page text, DOM summary, navigation, click, type, keypress, scrolling, screenshots, and task state.
- Agent-friendly results: structured JSON responses, action outcomes, and explicit errors.
- Recovery and observability: bounded waits, retry policies, task trace, and clear completion/failure status.
- Local-only by default: bind control endpoints to loopback, require an authentication token for state-changing operations, and never expose browser control to the LAN by default.

## Planned architecture

1. **Firefox integration** — a Firefox extension or browser-side component for page inspection and user-approved actions.
2. **Native messaging / local bridge** — a locally installed companion process that communicates with the extension. Avoid exposing privileged browser APIs directly over unauthenticated HTTP.
3. **Control API** — localhost service on port 8900, with endpoints for health/status, goal submission, page state, navigation and interaction.
4. **Goal runner** — a bounded state machine: observe -> plan -> act -> verify -> recover or finish.
5. **Verification** — actions report observed outcomes; the runner does not mark a task successful merely because a click or navigation command was issued.

## Initial API contract (proposed)

| Endpoint | Purpose |
| --- | --- |
| GET /health | Bridge health and version |
| GET /status | Browser connection and current tab state |
| POST /goal | Submit a goal and bounded execution options |
| GET /task/:id | Read task status, events and result |
| POST /navigate | Navigate the active tab to an allowed HTTP(S) URL |
| GET /page | Return URL, title and readable page text |
| GET /dom | Return a bounded DOM/locator summary |
| POST /click | Click a uniquely resolved element |
| POST /type | Type into a uniquely resolved field |
| POST /key | Send an allowlisted key |
| POST /scroll | Scroll the active page |
| GET /screenshot | Capture the visible page where supported |

These endpoints are a design contract, not implemented functionality yet.

## Safety and security

- Loopback bind only (127.0.0.1), not 0.0.0.0.
- Require a random bearer token for write/control endpoints.
- Do not allow arbitrary JavaScript evaluation by default.
- Restrict navigation to HTTP and HTTPS; block local files and browser-internal pages unless explicitly implemented with safeguards.
- Require explicit confirmation for purchases, sending messages, account changes, destructive actions, and other consequential operations.
- Bound task duration, number of actions, response sizes and retries.
- Redact credentials, cookies and sensitive form values from task logs.
- Make browser-control and data extraction visible to the user.

## Development plan

- [ ] Create/verify the actual upstream Firefox fork under uhackman-cpu.
- [ ] Choose an integration point compatible with Firefox's current architecture.
- [ ] Implement a minimal page-state bridge and schema-tested local API.
- [ ] Add goal runner with observe/act/verify loop and bounded retries.
- [ ] Add unit/integration tests and CI.
- [ ] Document build steps and release artifacts.

## Upstream

- Firefox source: https://github.com/mozilla-firefox/firefox
- This repository currently contains project design/bootstrap documentation only. It should not be represented as a working Firefox build or a completed fork.
