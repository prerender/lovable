# Prerender.io – Lovable (Cloudflare Worker)

Cloudflare Workers that route bot traffic through Prerender.io for SEO rendering on Lovable apps.

## Which worker to use

| File | For whom |
|------|----------|
| `worker-subscriber.js` | Lovable **paid subscribers** with a custom domain set up in Lovable's dashboard |
| `worker-custom-domain.js` | **Free Lovable users** who proxy their `yourapp.lovable.app` through Cloudflare to serve it from a custom domain |

---

## worker-subscriber.js (paid subscribers)

For Lovable paid plan users who configured their custom domain directly in Lovable. Your domain already points to Lovable's servers — this worker sits in front and routes crawler traffic to Prerender.io. No proxying needed.

### Setup

1. Deploy `worker-subscriber.js` as a Cloudflare Worker on your custom domain
2. Set the following environment variable:

| Variable | Description |
|----------|-------------|
| `PRERENDER_TOKEN` | Your Prerender.io token |

---

## worker-custom-domain.js (free users / custom proxy)

For free Lovable users who want to serve their app from a custom domain by proxying through Cloudflare. All traffic is forwarded to your Lovable app URL, with bots routed to Prerender.io and canonical tags injected.

### Setup

1. Deploy `worker-custom-domain.js` as a Cloudflare Worker on your custom domain
2. Set the following environment variables:

| Variable | Description |
|----------|-------------|
| `LOVABLE_UPSTREAM` | Your Lovable app URL (e.g. `https://yourapp.lovable.app`) |
| `PRERENDER_TOKEN` | Your Prerender.io token |

### Authentication / OAuth

Lovable uses Supabase for authentication. When your app is served from a custom domain via this worker, the OAuth callback URL seen by the browser will be your custom domain (e.g. `https://example.com/auth/callback`), not the original Lovable URL.

For auth to work correctly you need to add your custom domain to the allowed redirect URLs in the Lovable dashboard:

1. Go to your Lovable dashboard
2. Navigate to **Cloud → Users → Authentication Settings → Advanced**
3. Add your custom domain to the **URI allow list** (e.g. `https://example.com/**`)

Without this step, Supabase will reject the OAuth callback and users will not be able to log in.

## Requirements

- Cloudflare Workers
- `worker-custom-domain.js` additionally requires `HTMLRewriter` support (available on all Cloudflare Workers plans)
