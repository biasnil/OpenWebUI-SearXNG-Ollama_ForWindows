## Known-Issues & Fixes  (Open WebUI ⇄ SearXNG stack)

| Symptom | Root Cause | Quick Fix |
|---------|------------|-----------|
| **SearXNG 500 error**<br/>Logs show `KeyError: 'default_doi_resolver'` | Newer SearXNG versions expect that key but the custom `settings.yml` copied by users often omits it. | Add a *doi-resolver* map **and** pick one as default:  <br/>```yaml<br/>doi_resolvers:<br/>  oadoi.org:  "https://oadoi.org/"<br/>  doi.org:    "https://doi.org/"<br/>default_doi_resolver: "oadoi.org"<br/>``` |
| **Restart loop** & log says `server.secret_key is not changed` | You’re still using the template’s placeholder key. | Set any long random string:<br/>```yaml<br/>secret_key: "REPLACE_WITH_RANDOM_STRING"<br/>``` |
| **`results: []` in JSON** (UI shows nothing) | Your minimal `settings.yml` overwrote SearXNG’s full engine list, so zero engines are enabled. | Start from the stock template (inside the image: `/usr/local/searxng/searx/settings.yml`) and merge your edits—never delete the `engines:` block. |
| **Open WebUI says “An error occurred while searching the web”** and logs show it calling `localhost:80` or a LAN IP | WebUI keeps an internal DB row for its Web-Search tool; if you change URLs via env/file *after* first launch, the old entry sticks. | 1 Stop & remove the container.<br/>2 Re-create with<br/>`WEBSEARCH_URL=http://searxng:8080` **or** mount a fixed `websearch.json`.<br/>3 Start again. |
| **Can’t edit files inside WebUI container (`read-only file system`)** | The image is read-only by design. | Mount your config files from the host using `volumes:` in `docker-compose.yml`. |

### Minimal working compose block

```yaml
services:
  searxng:
    image: searxng/searxng
    ports: ["8081:8080"]
    volumes:
      - ./searxng/settings.yml:/etc/searxng/settings.yml:ro
    restart: unless-stopped

  openwebui:
    image: ghcr.io/open-webui/open-webui:main
    ports: ["3000:8080"]
    environment:
      - WEBSEARCH_URL=http://searxng:8080
    restart: unless-stopped

Add the file into your Users\<name>\
```
then open powershell
add this code
cd C:\Users\<name> <------------ Change this \webai-stack
docker compose down -v               # stop & wipe anonymous volumes
docker compose up -d --build         # rebuild and start


> **Why mostly on Windows?**  
> Docker Desktop for Windows runs containers inside a hidden WSL 2 VM.  Paths, hostnames (`host.docker.internal`), and read-only filesystems behave differently from native Linux, so mounts and internal URLs that “just work” on Linux often need explicit fixes when the host OS is Windows.

Then after its all done
For Searxng Query URL (OpenWebui)
http://searxng:8080/search?q=<query>
