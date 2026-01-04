# Bookmark Privacy: Understanding and mitigating background requests

Modern browsers, including Firefox, sometimes make automatic network requests for bookmarks. While convenient, these background requests can pose a privacy risk, allowing websites to infer your online activity even when you haven’t visited the page. This guide explains the risk and how to mitigate it.

> If you're a journalist, activist, or dissident in an oppressive regime: TAKE NOTE!

Your threat model must be realistic:

    If you're a journalist, activist, or dissident in an oppressive regime
    If you face ISP-level monitoring or deep packet inspection
    If your adversary has resources for large-scale data correlation

Then you need defense in depth:

    Tor Browser (not just Tor) for anonymity-critical activities
    Tails OS or Whonix for isolation
    Strict operational security (no mixing of identities)
    Understanding that metadata analysis can reveal patterns even when content is encrypted


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

## In-depth

A more thorough explaination for technical users.

### The Timing correlation attack

A **timing correlation attack** or **traffic analysis attack**. Here's how it works in the bookmark scenario:

### Without VPN (Normal browsing):
- 8:00 AM: Favicon request to `example.com` from IP `123.45.67.89`
- 8:05 AM: Favicon request to `news-site.com` from IP `123.45.67.89`
- 8:10 AM: Favicon request to `forum.org` from IP `123.45.67.89`

### With VPN:
- 8:00 AM: Favicon request to `example.com` from VPN IP `198.51.100.45`
- 8:05 AM: Favicon request to `news-site.com` from VPN IP `198.51.100.45`
- 8:10 AM: Favicon request to `forum.org` from VPN IP `198.51.100.45`

### The Problem:
An adversary monitoring at the ISP or national level sees:
- **The same sequence** of domains requested
- **The same timing pattern** (5-minute intervals)
- **The same behavioral fingerprint** (browser startup routine)

Even though the IPs are different, the **pattern is identical**. With enough data points, statistical correlation can link the two identities with high confidence.

### Why bookmarks make this worse

Bookmarks create a **persistent, predictable signature**:

1. **Consistency**: The same set of bookmarks generates the same requests
2. **Timing**: Browser startup, sync events, or periodic refreshes happen at predictable times
3. **Uniqueness**: Your specific combination of bookmarks is likely unique to you
4. **Involuntary**: These requests happen automatically, creating a reliable fingerprint

### Real-world example:

```
User has bookmarks: [A, B, C, D, E, F, G, H, I, J]

Week 1 (no VPN):
Mon 8:00 → A,B,C,D,E,F,G,H,I,J from IP 123.45.67.89
Tue 8:00 → A,B,C,D,E,F,G,H,I,J from IP 123.45.67.89

Week 2 (using VPN):
Mon 8:00 → A,B,C,D,E,F,G,H,I,J from IP 198.51.100.45
Tue 8:00 → A,B,C,D,E,F,G,H,I,J from IP 198.51.100.45

Correlation confidence: 99%+ (same 10 domains, same order, same timing)
```

### Additional de-anonymization vectors

Beyond favicons, other bookmark-related vectors include:

1. **DNS query patterns**: Even with encrypted DNS (DoH/DoT), the timing and frequency of queries can be correlated
2. **TLS SNI leakage**: Server Name Indication in TLS handshakes reveals destination domains (unless using ECH/eSNI)
3. **Traffic volume analysis**: Different sites have different resource sizes; analyzing encrypted traffic volume can identify sites
4. **Browser fingerprinting**: Favicon requests include User-Agent and other headers that narrow down your browser configuration

### Mitigation strategies for high-risk scenarios

If you're in an oppressive regime or facing sophisticated adversaries:

### 1. **Eliminate bookmark-based requests entirely**
```
Firefox about:config:
- browser.chrome.site_icons = false
- browser.chrome.favicons = false
- network.predictor.enabled = false
- network.http.speculative-parallel-limit = 0
- network.dns.disablePrefetch = true
- network.prefetch-next = false
```

### 2. **Use Tor Browser for sensitive activities**
- Tor Browser is specifically designed to resist traffic analysis
- All users look similar (unified fingerprint)
- No automatic favicon fetching
- Built-in protections against timing attacks

### 3. **Separate browsing contexts completely**
- **Never** mix VPN and non-VPN browsing on the same browser profile
- Use different browsers entirely: one for VPN, one for normal use
- Better: use separate VMs or live operating systems (Tails, Whonix)

### 4. **Randomize timing patterns**
- Don't browse at the same times every day
- Add noise by varying your online activity patterns
- Consider automated traffic generators to obscure real patterns

### 5. **Use disposable identities**
- Don't reuse bookmarks across different identities
- Create fresh browser profiles for each sensitive session
- Assume any persistent data (bookmarks, history, cookies) is linkable

### 6. **Network-level protection**
- Use bridges/obfuscation for VPN/Tor connections
- Multiple VPN hops (VPN → Tor, or VPN → VPN)
- Consider your entire network stack: DNS, SNI, traffic timing

### The hard truth

For users facing state-level adversaries with comprehensive surveillance:

**VPNs alone are insufficient.** They provide IP obfuscation, but traffic analysis can defeat this through:
- Timing correlation
- Traffic fingerprinting
- Cross-session linkage via behavioral patterns

**Your threat model must be realistic:**
- If you're a journalist, activist, or dissident in an oppressive regime
- If you face ISP-level monitoring or deep packet inspection
- If your adversary has resources for large-scale data correlation

Then you need **defense in depth**:
1. Tor Browser (not just Tor) for anonymity-critical activities
2. Tails OS or Whonix for isolation
3. Strict operational security (no mixing of identities)
4. Understanding that metadata analysis can reveal patterns even when content is encrypted

---
