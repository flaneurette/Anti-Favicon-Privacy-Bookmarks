# Bookmark Privacy: Understanding and mitigating background requests

Modern browsers, including Firefox, sometimes make automatic network requests for bookmarks. While convenient, these background requests can pose a privacy risk, allowing websites to infer your online activity even when you haven’t visited the page. This guide explains the risk and how to mitigate it.

---

## 1. How Firefox makes background requests

Firefox may automatically fetch:

* Favicons: The small site icons next to bookmarks.
* Page metadata: Titles, descriptions, or thumbnails.
* Speculative connections: Preloading or preconnecting to sites for faster autocomplete in the URL bar.

These requests happen in the background, without you clicking the bookmark. They include information such as:

* Your IP address
* Browser User-Agent
* Timing of the request

---

## 2. Why this is a privacy risk

* Hidden tracking: Sites can log favicon requests and correlate them over time.
* Fingerprinting: Combined with IP and timing, these requests can identify you across sessions.
* Activity inference: Even if you never visit the main page, a website could detect when you’re online based on the timing of favicon requests.

### Simple example

> A server sees your IP request `/favicon.ico` every morning at 8 AM. It can infer that you’re likely online at that time, even if you never clicked the bookmark. Then they could determine your routine, and predict when you are online, and perhaps launch an attack if the site owner, or those who hacked it, are malicious actors. Remember: server logs are stored for weeks, months, sometimes years. This is valuable information to certain persons or groups.

### Extreme privacy risk example

> If a state actor or hacker group monitors ISP level data, ISP level packet sniffing, they might catch all `./favicon.ico` requests, of all your bookmarks, across different detection periods and correlate them, and then build a complete profile of what sites you have bookmarked. This can build a very solid user profile with all your bookmarks correlated inside a massive database: and technically reconstruct all your bookmarks into a very valuable profile. Although perhaps rare, it is a possibility that you should not forget.

It is even worse if you **sometimes**  use a VPN or proxy, and **sometimes** don't. Theoretically, technically, it can unmask and cause de-anonymization of VPN when correct statistics are applied, by statistically calculating the VPN "time" used, and the normal background "pinging" of the favicons. 

---

## 3. Mitigation strategies

You can reduce or eliminate these background requests by adjusting Firefox settings.

### A. Disable Favicon Fetching

1. Open Firefox and type `about:config` in the address bar.
2. Search for `browser.chrome.site_icons`.
3. Set it to `false`.
4. Set `browser.chrome.favicons` to false (additional protection)

This prevents Firefox from automatically fetching site icons for bookmarks.

---

### B. Disable speculative connections / prefetching

1. In `about:config`, search for `network.prefetch-next` and set it to `false`.
2. Search for `browser.urlbar.speculativeConnect.enabled` and set it to `false`.

Additional strategies:

Set `network.http.speculative-parallel-limit` to 0
Set `network.predictor.enabled` to false
Set `network.dns.disablePrefetch` to true

These settings stop Firefox from preloading sites or preconnecting to bookmarks you haven’t visited.

---

### C. Use privacy extensions

* uBlock Origin or NoScript can block unwanted requests.
* Privacy-focused browsers or containers reduce cross-site correlation.

---

### D. Consider using a VPN or Tor

Even with mitigation, some requests may slip through. Using a VPN or Tor can hide your real IP, further protecting privacy.

---

### E. Also consider using nextDNS

NextDNS also can block Mozilla/Chrome telemetry.

---

## Summary

* Firefox background requests for bookmarks are convenient but can leak privacy.
* Requests for favicons, metadata, and speculative connections can be monitored and correlated by site owners.
* Mitigation involves disabling favicon fetching, speculative connections, and prefetching, along with privacy extensions or a VPN.

> By following these steps, bookmarks become “offline-only” until you explicitly click them, minimizing background exposure.

---
