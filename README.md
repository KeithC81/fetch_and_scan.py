
# fetch_and_scan.py

Targeted JavaScript Fetcher & Recon Scanner
Scope-aware | Noise-controlled | Built for Manual Hunters
fetch_and_scan.py is a precision-focused JavaScript fetcher and pattern scanner designed for bug bounty reconnaissance and manual API discovery.
It integrates cleanly into modern recon workflows and avoids the noise explosion caused by blind mass-downloading tools.

### The tool:

 • Fetches JavaScript from scoped URL lists 

 • Verifies real JS content before saving 

 • Deduplicates via content hashing 

 • Scans for high-value patterns (tokens, endpoints, GraphQL, secrets) 

 • Supports advanced filtering and custom headers 

 • Integrates seamlessly with Burp, ZAP, or custom pipelines 

##

## ✨ Features

### JavaScript Collection

 • Fetch JS from CSV or plain-text lists
 • Validate content-type before saving
 • Skip HTML/CSS/JSON responses
 • Hash-based deduplication
 • Retry with exponential backoff
 • Threaded downloader

### Advanced Scope Control:

 • Regex-based include filtering
 • Regex-based exclude filtering
 • Glob-style post-filtering via --grep
 • Custom header injection
 • Proxy support
 • Optional TLS disable
 
 ##

### Filtering order:



 include → exclude → grep → fetch → scan 














### Secret & Endpoint Detection
 Scans JavaScript for: 

 • API keys
 • Bearer tokens / JWTs
 • GraphQL queries & mutations
 • REST endpoints
 • Hard-coded credentials
 • Private keys
 • Service API paths
 • WebSocket endpoints
 • Internal IPs
 • Debug flags
 
 Pattern engine is easily extendable inside the script. 

##

## 📦 Output Structure


After execution:



 js_src/ 
    saved JavaScript files

 fetch_map.txt 
    URL → saved path → status

 findings.txt 
    Pattern matches

    













##

 Example fetch_map.txt 


~~~ bash
https://<TARGET>/static/app.js    js_src/4a7b2c.js    200
https://<TARGET>/index            (empty)             SKIP_NOT_JS:200;ctype=text/html
~~~













 Example findings.txt 


~~~ bash
js_src/4a7b2c.js    122    jwt_token    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
js_src/4a7b2c.js    310    graphql_mutation    mutation updateUser(...)
~~~











##

## 🚀 Installation

 Requirements: 

• Python 3.8+
 • requests
 • tqdm
 
Install dependencies: 


~~~ bash
pip3 install requests tqdm
~~~












 Optional: 


~~~
chmod +x fetch_and_scan.py
~~~











##

## 📁 Input Formats

 Supports: 

CSV


~~~ CSV
url,status
https://<TARGET>/app.js,200
https://<TARGET>/lib/main.js,200
~~~













 Plain Text 


~~~ bash
https://<TARGET>/app.js
https://<TARGET>/scripts/vendor.js
~~~











##

## ⚙️ Basic Usage

 Fetch and scan all JS from input list: 


~~~ bash
python3 fetch_and_scan.py -i js_urls.txt
~~~











##

## 🎯 Scope Filtering

 Only process URLs matching a regex: 


~~~ bash
--include 'https://<TARGET_REGEX>/'
~~~












 **Exclude known noisy hosts:**


~~~ bash
--exclude 'cdn|static|analytics'
~~~











##

## 🔍 Using --grep (Glob Filtering)

**Applied after include/exclude filtering.**

 Examples: 
*Only URLs under /api/:*


~~~ bash
--grep '*/api/*'
~~~












**Only GraphQL-related paths:**


~~~ bash
--grep '*graphql*'
~~~












**Multiple filters allowed:**


~~~ bash
--grep '*/api/*' --grep '*/admin/*'
~~~











##

## 🔐 Custom Headers

**Inject custom headers for authenticated JS fetching:**


~~~ bash
--header 'Authorization: Bearer <TOKEN>'
--header 'X-Custom-Header: <VALUE>'
~~~












**Multiple headers supported.**

##

## 🌐 Proxy Support

**Route traffic through Burp or ZAP:**


~~~ bash
--proxy http://127.0.0.1:8080
~~~












**Disable TLS verification if required:**


~~~ bash
--insecure
~~~











##

## ⚡ Performance Tuning

Threads:


~~~ bash
-w 6
~~~












**Delay between requests:**


~~~ bash
--delay 0.2
~~~












**Example balanced configuration:**


~~~ bash
-w 5 --delay 0.15
~~~











##

## 🧪 Advanced Usage Examples

### 1️⃣ Classic Bug Bounty JS Recon (Proxy Enabled)


~~~ shell
python3 fetch_and_scan.py \
  -i scoped_js_list.txt \
  --include 'https://<TARGET_REGEX>/' \
  --grep '*/api/*' \
  --proxy http://127.0.0.1:8080 \
  --insecure \
  -w 6 \
  --delay 0.2
~~~











##

## 2️⃣ GraphQL-Focused Discovery


~~~ shell
python3 fetch_and_scan.py \
  -i js_urls.txt \
  --include 'https://<TARGET_REGEX>/' \
  --grep '*graphql*'
~~~











##

## 3️⃣ Token Hunting Across Large JS Set


~~~ bash
python3 fetch_and_scan.py \
  -i full_domain_js.txt \
  --grep '*auth*' \
  --grep '*token*'
~~~











##

## 4️⃣ Authenticated JS Fetch (Protected Apps)


~~~
python3 fetch_and_scan.py \
  -i private_js_list.txt \
  --include 'https://<TARGET_REGEX>/' \
  --header 'Authorization: Bearer <ACCESS_TOKEN>' \
  --header 'X-Requested-With: XMLHttpRequest'
~~~











##

## 🧠 Recon Methodology Integration

### Modern workflow:

1.  Crawl

~~~~1
Use:
• katana
 • gospider
 • Burp site map export
 • browser DevTools
~~~~
2. Extract JS URLs
~~~~2
From crawler output → feed into fetch_and_scan.
~~~~
3.  Run Scoped Scan
~~~~3
Apply:
• include boundaries
 • exclude noise
 • grep targeting
~~~~

4.  Review findings.txt
~~~~4
Look for:
• JWT tokens
 • GraphQL schemas
 • Internal REST endpoints
 • Debug features
 • Hardcoded credentials
~~~~
5.  Manual Deep Dive
~~~~5
Feed interesting endpoints into:
• Burp Repeater
 • Custom GraphQL probes
 • API fuzzers
 • Banner scanning pipeline
~~~~

###
###

## 🔬 Why This Tool Exists

**Most JS scanners:**
◇ Download everything

◇ Generate massive noise

◇ Lack scope control

◇ Don't integrate cleanly with manual workflows


### fetch_and_scan.py is built for:

 ✔ Precision
 ✔ Control
 ✔ Recon discipline
 ✔ Human-driven investigation

###
###

## ⚠️ Operational Safety

*Always:*
 • Respect program scope
 • Respect rate limits
 • Avoid over-threading
 • Use delay for fragile targets
 • Do not mass-scan third-party CDNs
 
*This tool is designed for authorized testing only.*
