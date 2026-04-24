# AstroPicture-of-the-Day
NASA Astronomy Picture of the Day (AI-assisted Coding, enhanced by Cloudflare Workers)
Search & Download Astronomy Picture of a specific day

Both Chinese Simplified and English are supported, all in one file.
## Installation
There is a static HTML file with everything needed enbedded. No need for installation.
## Deploy
### Request for an APIkey
https://api.nasa.gov/
### Configure Cloudflare Workers
1) Code:
```
export default {
  async fetch(q, e) {
    const h = {"Access-Control-Allow-Origin": "*"}, u = new URL(q.url), s = u.searchParams, i = s.get("img_url"), d = s.get("date");
    if (q.method == "OPTIONS") return new Response(null, {headers: h});
    if (i) return fetch(i).then(r => {
      const n = new Response(r.body, r);
      return n.headers.set("Access-Control-Allow-Origin", "*"), n.headers.set("Cache-Control", "public,max-age=604800"), n;
    }).catch(() => new Response("Error", {status: 500, headers: h}));
    if (d) return fetch(`https://api.nasa.gov/planetary/apod?api_key=${e.NASA_KEY||"DEMO_KEY"}&date=${d}`).then(r => r.json()).then(j => {
      if (j.media_type == "image" && s.get("use_proxy") != "false") j.proxy_url = `${u.origin}${u.pathname}?img_url=${encodeURIComponent(j.hdurl||j.url)}&use_proxy=true`;
      return new Response(JSON.stringify(j), {headers: {...h, "Content-Type": "application/json"}});
    }).catch(() => new Response("Error", {status: 500, headers: h}));
    return new Response(0, {status: 400, headers: h});
  }
};
```
2) Add variable: NASA_KEY (Type: Plaintext, Value: Your NASA apikey)
3) Set WORKER_BASE_URL in the HTML file to your Cloudflare Workers domain. (e.g. "https://project.username.workers.dev")
