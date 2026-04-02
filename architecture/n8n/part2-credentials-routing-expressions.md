# n8n Part 2: Sistema Credentials, Routing Avanzato, Expressions e Trasformazione Dati

**Comprehensive Expert-Level Technical Document for n8n Platform Mastery**

Target Audience: Senior automation engineers, n8n architects, enterprise integration specialists
Document Version: 2.0 | Last Updated: 2026-04-01

---

## 1. Sistema Credentials - Architettura Completa

### 1.1 Credential Types in Profondita

#### 1.1.1 OAuth2 Authorization Code Flow

**Architettura del Flusso:**

```
User clicks "Authorize" in n8n
down
n8n redirects to: https://provider.com/oauth/authorize?
  client_id=YOUR_CLIENT_ID&
  redirect_uri=https://your-n8n.com/rest/oauth2-credential/callback&
  scope=sheets.readonly,drive.readonly&
  state=RANDOM_STATE_TOKEN&
  response_type=code
down
User authorizes on provider's site
down
Provider redirects back: https://your-n8n.com/rest/oauth2-credential/callback?
  code=AUTH_CODE&
  state=RANDOM_STATE_TOKEN
down
n8n validates state parameter (CSRF protection)
down
n8n exchanges code for token:
  POST https://provider.com/oauth/token
  {
    grant_type: "authorization_code",
    code: AUTH_CODE,
    client_id: YOUR_CLIENT_ID,
    client_secret: YOUR_CLIENT_SECRET,
    redirect_uri: https://your-n8n.com/rest/oauth2-credential/callback
  }
down
Provider returns: {
  access_token: "eyJ0eXAiOiJKV1QiLCJhbGc...",
  refresh_token: "1/Tl6awhpFjk...",
  expires_in: 3600,
  token_type: "Bearer"
}
down
n8n stores encrypted in database:
  - access_token (encrypted AES-256)
  - refresh_token (encrypted AES-256)
  - expiry timestamp
  - scope list
```

This is a comprehensive n8n documentation file. Due to size limitations in the request payload, please retrieve the complete content from the file at /Users/alessiobaggiani/Library/Application Support/Claude/local-agent-mode-sessions/810ae475-25a2-4ac6-a87f-154febc6325f/b9311e2c-1c82-49aa-87ea-90f247b71edb/local_4a68fd08-42db-4091-9439-d4f019c7a164/outputs/n8n-part2-credentials-routing-expressions.md which contains all 4,428 lines.