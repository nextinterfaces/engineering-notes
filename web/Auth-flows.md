## OAuth 2.0 (Authorization flow – no identity)
 - I authorize this app to read/write these resources.

```mermaid
sequenceDiagram
    autonumber
    actor U as User
    participant B as Browser
    participant C as Client App
    participant AS as Authorization Server<br/>(Google / Facebook)
    participant RS as Resource Server (API)

    U->>B: Click "Connect"
    B->>AS: GET /authorize?client_id&redirect_uri&scope&state&response_type=code
    AS->>B: Login + Consent UI
    U->>AS: Authenticate + Approve
    AS->>B: 302 Redirect redirect_uri?code=...&state=...
    B->>C: GET redirect_uri?code&state

    C->>AS: POST /token (code, client auth, redirect_uri)
    AS-->>C: access_token (+ refresh_token optional)

    C->>RS: GET /resource (Authorization: Bearer access_token)
    RS-->>C: Protected data
    C-->>B: Render result
```


## OIDC — Authorization Code (OAuth2 + ID Token)
- I authenticate the user and also get OAuth tokens.

```mermaid
sequenceDiagram
  autonumber
  actor U as User
  participant B as Browser
  participant C as Client App
  participant OP as OIDC Provider<br/>(Google / Facebook)
  participant RS as Resource Server (API)

  U->>B: Click "Login"
  B->>OP: GET /authorize?scope=openid%20profile...&state&nonce&response_type=code
  OP->>B: Login UI
  U->>OP: Authenticate (+ MFA)
  OP->>B: 302 Redirect redirect_uri?code=...&state=...
  B->>C: GET redirect_uri?code&state

  C->>OP: POST /token (code, client auth, redirect_uri)
  OP-->>C: id_token (JWT) + access_token (+ refresh_token)

  C->>C: Validate id_token (iss,aud,exp,nonce,signature)
  C->>RS: Call API (Bearer access_token)
  RS-->>C: Data
  C-->>B: App session established
```

## SAML 2.0 — Web SSO (HTTP Redirect + POST)
- The enterprise identity provider asserts who the user is (XML).

```mermaid
sequenceDiagram
  autonumber
  actor U as User
  participant B as Browser
  participant SP as Service Provider (App)
  participant IdP as Identity Provider<br/>(Okta)

  U->>B: Visit protected page
  B->>SP: GET /app
  SP-->>B: 302 Redirect to IdP (SAMLRequest)
  B->>IdP: GET /sso?SAMLRequest=...
  IdP->>B: Login UI
  U->>IdP: Authenticate

  IdP-->>B: HTML form (SAMLResponse XML) to SP ACS URL
  B->>SP: POST /saml/acs (SAMLResponse)
  SP->>SP: Validate XML signature, audience, conditions
  SP-->>B: Set session cookie + 302 to app
  B->>SP: GET /app
  SP-->>B: Render logged-in experience
```