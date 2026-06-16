# JumpCloud Power OIDC Verifier

**A zero-dependency, single-file OIDC verification and debugging suite built specifically for JumpCloud SSO proof-of-concept engagements.**

🌐 **[Open the tool](https://roshansham.github.io/jc-power-oidc-verifier/)** — no install, no server, just open and run.

---

## What it does

Simulates your application's OIDC client to walk the full OpenID Connect authorization code flow against a JumpCloud SSO app, section by section, with JumpCloud-specific remediation hints at every failure point.

### Verification sections (14 steps)

| # | Section | What it checks |
|---|---------|----------------|
| 0 | Config Validation | Fields, auth type consistency, masked clientId |
| 1 | Discovery Document | All required endpoints, origin validation |
| 2 | JWKS | Key count, algorithm types |
| 3 | Auth URL | Construction, PKCE S256 challenge, state/nonce |
| 4 | Code Exchange | Authorization code → token set |
| 5 | Token Response | access_token format (Opaque vs JWT), refresh token presence |
| 5a | Access Token | Format inspection |
| 6 | ID Token | Signature alg, kid matching, clock skew (RFC 9068), issuer normalisation |
| 6a | Standard Claims | Sub, exp, iat, nonce, AMR |
| 6b | Profile Claims | Email, name, given_name, preferred_username |
| 6c | Custom Claims | Application-specific attribute mappings |
| 6c-auth | Authorizations | JumpCloud role/permission claim |
| 7 | UserInfo Endpoint | Claim consistency with ID Token |
| 8 | Refresh Token | Token exchange cycle |
| 9 | Refreshed ID Token | Sub consistency, new iat/exp |
| 10 | UserInfo After Refresh | Continued access |
| 11 | Token Rotation | Old refresh token rejection |
| 12 | End Session | Logout URL construction with id_token_hint |
| 13 | Summary | Completion with score inputs |

### Key features

- **OIDC Readiness Health Score** — 100-point scoring across 5 categories (Discovery, Token Security, Claims, Refresh Lifecycle, MFA & Logout) with animated ring gauge and category bars
- **Exportable POC Report** — one-click HTML report download with score breakdown, claim tables, and metadata; clean enough to share with a prospect IT team
- **Scope Coverage Analyser** — maps requested scopes to expected claims, shows what's present vs missing, with JumpCloud-specific remediation notes per missing claim
- **Live Token Countdown** — topbar strip showing real-time remaining lifetime for the ID token, access token, and refresh token, ticking down every second from the actual issued-at timestamps rather than a frozen snapshot. The refresh token figure is visually marked (dashed) as a JumpCloud-default estimate, since the token response never returns its actual expiry
- **Discovery Inspector** — collapsible pre-auth view of all advertised endpoints, grant types, algorithms, scopes, and claims with colour-coded chip UI
- **Token Introspection** — calls the introspection endpoint with the access token, verifies `active=true` and `sub` consistency with the ID Token. If JumpCloud doesn't advertise an `introspection_endpoint` for the tenant (common — it isn't published for every SSO app), the button disables itself and explains why rather than failing silently
- **Token Revocation** — revokes the access token, confirms invalidation by calling UserInfo (expects 401), and surfaces the result in its own dedicated Results tab
- **Multi-Run Claim Diff** — save two runs (different users, different groups) and get a side-by-side claim diff table showing added, removed, and changed values

### Security properties

- No dependencies, no CDN, no external scripts — runs entirely from disk or any static host
- Client secret never written to localStorage or preset storage
- Action registry pattern — no JS injected into onclick attributes
- Discovery endpoint origin validation — all endpoints must resolve to `oauth.id.*.jumpcloud.com`
- Redirect URL origin validation before code extraction
- `data:` and `javascript:` URI rejection in redirect handling
- jwt.io confirmation gate before sending tokens to a third party
- Content Security Policy meta tag with locked connect-src, frame-ancestors, and form-action

---

## Usage

1. Download `index.html` (or open the [live site](https://roshansham.github.io/jc-power-oidc-verifier/))
2. Open in any modern browser
3. Enter your JumpCloud SSO app credentials from the OAuth tab
4. Select your region (US / EU / IN)
5. Click **Run Verification**
6. After completion, use **Export Report** to generate a shareable HTML report

### For PKCE / public client apps

Switch Auth Method to **PKCE** — no client secret required. The tool generates a cryptographically random verifier and SHA-256 challenge using WebCrypto.

### Preset management

Save configurations per customer or SSO app using the Presets tab. Secrets are deliberately excluded from preset storage.

---

## Version history

| Version | Changes |
|---------|---------|
| v3.1 | Replaced the static token-expiry timeline with a live topbar countdown; dedicated Revoke result tab; corrected Introspect-disabled messaging; light theme is now the default regardless of OS preference |
| v3.0 | Health score, report export, scope analyser, token timeline, discovery inspector, introspection, revocation, multi-run diff |
| v2.3 | Security hardening: CSP, origin validation, action registry, jwt.io gate, secret exclusion from storage |
| v2.2 | Section 12 end session test, authorizations claim section, clock skew corrected to RFC 9068 300s, dynamic step total |
| v2.1 | Initial public build |

---

## Built for

JumpCloud Solutions Engineers running OIDC SSO proof-of-concept engagements. Not an official JumpCloud product.

---

*Single-file, zero-dependency, zero-telemetry. Everything runs in your browser.*
