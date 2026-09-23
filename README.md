# pi-share-viewer

A self-hosted replacement for `https://pi.dev/session/`, the page that renders
sessions shared with pi's `/share` command.

Live at: https://bpo-ha.github.io/pi-share-viewer/

## Use it

```sh
export PI_SHARE_VIEWER_URL="https://bpo-ha.github.io/pi-share-viewer/"
```

`/share` then prints `https://bpo-ha.github.io/pi-share-viewer/#<gistId>`. The session
itself still lives in a secret gist on your GitHub account.

## Why

`/share` (without Radius) uploads the session as a secret gist and links to a viewer
page. The gist ID sits in the URL fragment, which browsers never send to the server,
but the viewer's JavaScript reads it. Whoever serves the viewer can change that
JavaScript at any time, so they are trusted every time anyone opens a link.

This page removes that third party and adds a Content-Security-Policy that limits
network access to `api.github.com` and `gist.githubusercontent.com`. The sandboxed
`srcdoc` iframe that renders the transcript inherits the same policy, so neither the
page nor the shared transcript can send the gist ID or its contents anywhere else.

## Behaviour

- `#GIST_ID` loads `session.html` from the gist; `#GIST_ID/other.html` loads another file.
- `#GIST_ID&leafId=…&targetId=…` deep links work, and per-message share links point back here.
- Gists over 1 MB are fetched from their `raw_url`.
- Unauthenticated GitHub API calls are limited to 60 per hour per IP.

## Test

Serve locally with `python3 -m http.server`, then open `http://localhost:8000/#<gistId>`.
