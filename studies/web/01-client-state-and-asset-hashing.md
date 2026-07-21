# Case Study: Analysis of Client-Side Token Generation and Obfuscation Techniques

> **Note:** This case study focuses on client-side state management and public asset analysis for research and educational purposes only.

---

## 1. Executive Summary

Modern web applications often use client-side scripts to generate dynamic tokens or identifiers before sending requests to backend services. This case study demonstrates how client-side JavaScript builds a unique payload by combining global state variables, static asset hashes, and simple character transformations. It also illustrates why obfuscation on the client side cannot replace true server-side security.

---

## 2. Step-by-Step Technical Breakdown

### Step 1: Downloading the Page and Extracting Initial State (`window.__INIT__`)

The application delivers its initial configuration inside a `<script>` tag rendered on the server side. This configuration contains metadata such as application version and user session hints.

In JavaScript, the state is stored in a global object:

```javascript
let t = window;
let n = `__INIT__`;
let appVersion = t[n].appVersion;
let uaData = t[n].ua;
```

In Python, we can extract and parse this initial JSON payload from the HTML tree using `BeautifulSoup`:

```python
class TargetAppData:
    def __init__(self, html: str):
        self.html: str = html
        self.script: dict[str, str] = {}

    def parse(self):
        soup = BeautifulSoup(self.html, "lxml")
        data_script = [s for s in soup.find_all("script") if "__INIT__" in s.text]
        if not data_script:
            raise ValueError("Could not find initialization script tag.")

        raw_json = data_script[0].text.replace("window.__INIT__ = ", "")
        self.script = json.loads(raw_json)
```

---

### Step 2: Extracting Static Asset Hashes (CSS Salt)

To make token generation dependent on the specific deployment build, the application reads the filename of its compiled stylesheet. Each build generates a unique hash in the stylesheet URL (for example, `/build/assets/main.a8f9d32b.css`).

The JavaScript implementation extracts this hash using a regular expression and reverses it:

```javascript
let headHtml = document.head.innerHTML;
let match = /\/build\/(?:assets\/)?main\.([^"]+?)\.css/g.exec(headHtml);
if (!match) return `dev`;

// Extract hash and reverse it
let reversedCssSalt = match[1].split(``).reverse().join(``);
```

The equivalent extraction logic in Python looks like this:

```python
build_style_asset = soup.find("link", attrs={"rel": "stylesheet"})
href = build_style_asset.get("href", "")
# Extract the hash segment before the extension and reverse it
reversed_build_asset_salt = href.split('.')[-2][::-1]
```

---

### Step 3: Decoding Obfuscated Constants

To obscure internal values, the client-side code uses a basic character code shift (Caesar cipher variant) on an array of integers.

The original JavaScript code decodes these ASCII codes by subtracting `5` from each value:

```javascript
let obfuscatedCodes = [90, 84, 94, 100, 81, 81, 74, 89, 100, 70, 83, 83, 84, 76, 100, 89, 84, 83, 100, 82, 78, 100, 74, 89, 70, 82, 100, 94, 87, 87, 84, 88];

let decodedString = obfuscatedCodes
    .map(code => String.fromCharCode(code - 5))
    .reverse()
    .join(``);
```

In Python, the same array transformation is written as follows:

```python
codes = [90, 84, 94, 100, 81, 81, 74, 89, 100, 70, 83, 83, 84, 76, 100, 89, 84, 83, 100, 82, 78, 100, 74, 89, 70, 82, 100, 94, 87, 87, 84, 88]

hardcoded_data = "".join([chr(c - 5) for c in codes])[::-1]
```

---

### Step 4: Constructing the Raw Payload String

Once all individual pieces are collected (domain name, reversed stylesheet salt, sliced User-Agent data, decoded static string, and version numbers), they are concatenated into a single string called `unique_id` (or `l` in minified JavaScript).

JavaScript construction:

```javascript
let domain = t.location.hostname;
let userAgentSalt = t[n].ua.split(``).reverse().join(``).substr(0, 10);
let teamId = `app-internal-prefix-${appVersion}`;

// Concatenate all parts into a single identifier string
let rawPayload = domain + reversedCssSalt + userAgentSalt + decodedString + teamId + appVersion;
```

Python equivalent:

```python
first_10_chars_ua = target_data.script.get("ua", "")[::-1][:10]
app_version = target_data.script.get("appVersion", "")
team_id = f"app-internal-prefix-{app_version}"

unique_id = f"{self.domain}{reversed_build_asset_salt}{first_10_chars_ua}{hardcoded_data}{team_id}{app_version}"
```

---

### Step 5: Final Encryption and Token Generation (`Class N`)

The final step involves passing the raw concatenated string (`unique_id`) through an encryption/encoding helper class, referenced as `N` in the minified client bundle. Class `N` handles the string transformation (such as character code shifting and encoding) to produce the authorization token used by client-side requests.

Instead of exposing the underlying cipher mechanics line-by-line, we can treat Class `N` as an abstract encryption interface. In Python, this final step is represented as a high-level wrapper call:

```python
class N:
    def encrypt(self, unique_id: str, salt: str) -> str:
        # Abstract wrapper representing client-side token encoding
        # Transforms the payload string using the provided salt
        return encrypted_token_string

# Generate the encrypted authorization token
token = N().encrypt(unique_id, first_10_chars_ua)
```

---

## 3. Engineering Key Takeaways

1. **Client-Side Exposure:** Any logic executed inside the client browser-including custom encoding wrappers or ASCII ciphers-is fully visible during dynamic or static inspection.
2. **Build-Coupled Tokens:** Binding tokens to static file hashes (such as CSS filenames) invalidates older client sessions upon new deployments, but does not prevent token generation by an inspected client.
3. **Security Recommendation:** Critical authentication and validation logic must always take place on the server side using secure, HTTP-only session identifiers and standard cryptographic protocols.