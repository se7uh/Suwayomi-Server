# My Patches

## 1. Batch Fetch Manga

Refresh manga data (chapters, covers, metadata) for multiple titles at once.

### REST API

```
POST /api/v1/manga/fetch
```

Fetch all library manga:
```bash
curl -X POST http://localhost:4567/api/v1/manga/fetch \
  -H "Content-Type: application/json" \
  -d '{"all": true}'
```

Fetch specific manga by IDs:
```bash
curl -X POST http://localhost:4567/api/v1/manga/fetch \
  -H "Content-Type: application/json" \
  -d '{"ids": [1, 2, 3]}'
```

**Response:**
```json
{ "updatedIds": [1, 2, 3] }
```

---

## 2. Image Compression Proxy

Compress manga images on-the-fly through an external proxy to reduce bandwidth.
Uses [bandwidth-hero-proxy](https://github.com/ayastreb/bandwidth-hero-proxy).

### How it works

When enabled, image requests go through the proxy:
```
{proxyUrl}/api/index?url={encodedImageUrl}&l={quality}&bw={0|1}&jpeg={0|1}&avif={0|1}
```

- **Page images**: proxied per-request. If the proxy fails, falls back to fetching the original image directly.
- **Covers**: only proxied when `imageProxyOnCover = true`.

### Settings

Set via `server.conf`

| Setting | Default | Description |
|---------|---------|-------------|
| `imageProxyEnabled` | `false` | Enable/disable proxy |
| `imageProxyUrl` | `http://localhost:8000` | Proxy base URL |
| `imageProxyQuality` | `40` | Quality 1-100 (lower = smaller) |
| `imageProxyGrayscale` | `false` | Convert to grayscale (saves ~30%) |
| `imageProxyFormat` | `webp` | Output format: `webp`, `jpeg`, or `avif` |
| `imageProxyOnCover` | `false` | Apply proxy to manga covers/thumbnails |

### server.conf example

```ini
server.imageProxyEnabled = true
server.imageProxyUrl = "http://localhost:8000"
server.imageProxyQuality = 40
server.imageProxyGrayscale = false
server.imageProxyFormat = "webp"
server.imageProxyOnCover = false
```

---

## 3. Default UI Route

Change the default landing page from Library to Browse (or any other route).

### How it works

Injects a `<script>` snippet into `index.html` that uses `history.replaceState` to set the initial route before the SPA renders. No page reload, no auth bypass.

### Settings

| Setting | Default | Description |
|---------|---------|-------------|
| `defaultUIRoute` | `""` (empty) | Default route: `browse`, `library`, etc. Empty = SPA default (library) |

### server.conf example

```ini
server.defaultUIRoute = "browse"
```

---

## 4. Hidden UI Routes

Hide sidebar items and restrict access to specific routes (Library, Updates, History, etc.).

### Settings

| Setting | Default | Description |
|---------|---------|-------------|
| `hiddenUIRoutes` | `""` (empty) | Comma-separated routes: `library, updates, history` |

### server.conf example

```ini
server.hiddenUIRoutes = "library, updates, history"
```
