# polish-station-maintenance

Branded "Sorry, we're under maintenance" fallback for **polishstation.lk**, deployed as a Cloudflare Worker on the
route `*.polishstation.lk/*`.

- Unknown or mistyped subdomains get a 503 maintenance page (JSON for API requests) instead of a Cloudflare error.
- `www.polishstation.lk` → `polish-station-website` through service bindings, with the maintenance page if that Worker fails.

Every response carries `X-Polish-Station-Fallback` saying which branch answered.

```sh
node test/index.test.mjs        # local tests, no dependencies
npx wrangler deploy             # deploy (uses the existing wrangler login)
```

When a new app goes live on a subdomain of polishstation.lk, add it here and redeploy, or the route may answer it with
the maintenance page:

- a Worker (custom domain): add it to `SERVICE_HOSTS` and a matching `services` binding in `wrangler.jsonc`;
- a proxied record pointing at an origin server: add it to `ORIGIN_HOSTS`.

DNS-only (grey cloud) records never reach this Worker. Check with `curl -sI https://<host>/`: a `X-Polish-Station-Fallback`
header means this Worker answered.
