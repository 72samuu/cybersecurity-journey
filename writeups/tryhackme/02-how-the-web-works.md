# How the Web Works — TryHackMe module notes

**Platform:** TryHackMe · **Module:** How The Web Works · **Badges earned:** World Wide Web, Webbed
**Rooms:** DNS in Detail · HTTP in Detail · How Websites Work · Putting it all together

My own notes from the module. No flags or answers are included.

---

## 1. DNS in Detail

- DNS turns names (`tryhackme.com`) into IP addresses.
- **Domain hierarchy:** root (`.`) → TLD (`.com`, `.pt`) → second-level domain (`tryhackme`) → subdomain (`admin.tryhackme.com`).
- **Record types:**

| Record | Purpose |
|--------|---------|
| A | name → IPv4 |
| AAAA | name → IPv6 |
| CNAME | alias to another name |
| MX | mail server for the domain |
| TXT | free text — used for SPF, domain verification |

- **Resolution path:** local cache → recursive resolver (usually the ISP's) → root server → TLD server → authoritative server. Answers are cached for the record's **TTL**.

```bash
nslookup -type=MX example.com
dig example.com A
```

**Security angle:** subdomains and TXT records leak a lot during reconnaissance (forgotten `dev.` or `admin.` hosts, which mail and SaaS providers a company uses).

## 2. HTTP in Detail

- HTTP is a request/response protocol. HTTPS is HTTP inside TLS, so traffic is encrypted and the server is authenticated.
- **Request parts:** method, path, version, headers, optional body.
- **Methods:** `GET` (read), `POST` (send data / create), `PUT` (update), `DELETE` (remove).
- **Status codes:** `1xx` info · `2xx` success (`200 OK`, `201 Created`) · `3xx` redirect (`301`, `302`) · `4xx` client error (`400`, `401`, `403`, `404`, `405`) · `5xx` server error (`500`, `503`).
- **Important headers:** `Host`, `User-Agent`, `Cookie` / `Set-Cookie`, `Content-Type`, `Content-Length`, `Server`.
- **Cookies** keep state between requests (HTTP itself is stateless). A session cookie is often the only thing proving who you are.

```bash
curl -v http://example.com/           # see the raw request and response headers
curl -X POST -d "user=a&pass=b" http://example.com/login
```

**Security angle:** if an attacker steals a session cookie, they *are* the user — that's why `HttpOnly`, `Secure` and `SameSite` flags matter. The `Server` header can reveal software versions useful for finding known vulnerabilities.

## 3. How Websites Work

- **Front end:** HTML (structure), CSS (style), JavaScript (behaviour) — all of it is sent to the browser and readable by anyone.
- **Back end:** the server-side code and database the user can't see.
- **Sensitive data exposure:** developers sometimes leave comments, credentials or hidden links in HTML/JS. Always read the page source (`Ctrl+U`) and the browser dev tools.
- **HTML injection:** if user input is put into a page without being sanitised, an attacker can inject their own HTML — the same root cause as XSS.

**Security angle:** "never trust user input" — every input must be validated and output encoded.

## 4. Putting it all together

What happens when you open a website:

1. DNS lookup for the domain
2. TCP handshake with the server's IP (port 80/443), TLS handshake if HTTPS
3. The request may pass through a **load balancer**, a **CDN** (cached static files close to the user) and a **WAF** (filters malicious requests)
4. The **web server** (Apache, Nginx, IIS) handles it — possibly picking a site with **virtual hosts** based on the `Host` header
5. Back-end code runs and queries the **database**
6. The response travels back and the browser renders it

**Security angle:** each component is a target and a defence. A WAF can block attacks, but misconfigured virtual hosts or exposed admin panels can bypass it.

---

## What I took from it

Web attacks (OWASP Top 10) only make sense once you understand requests, cookies, DNS and where the data flows. This is my foundation for PortSwigger Web Security Academy next.
