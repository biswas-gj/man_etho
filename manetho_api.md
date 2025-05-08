
# Manetho API Reference (Markdown Edition)

> **Base URL:** `https://api.manetho.io/v1`   |   **Version:** 1.0.0   |   All responses in **JSON**, timestamps in **ISO‑8601 UTC**.

---

## 1  Cost‑Efficient Architecture

| Layer / Service | Primary (Managed) | Cost (USD) | Fallback / Self‑Host | Rationale |
|-----------------|-------------------|------------|----------------------|-----------|
| **Generative AI** | OpenAI GPT‑3.5‑Turbo | 0.002 / 1k tokens | Llama 2 7B on GPU spot instances | Route large batch jobs to Llama; burst queries to GPT for quality |
| **Embeddings & Vector DB** | OpenAI Embeddings | 0.0004 / 1k tokens | Qdrant on t4g.small (ARM) | Store cold vectors on Qdrant to cut 55 % cost |
| **Object Storage** | DigitalOcean Spaces (S3‑compatible) | 5 / 250 GB‑mo | MinIO on Hetzner | Swap to MinIO if egress > 180 GB/mo |
| **Auth & Users** | Auth0 Free Tier (7 k MAU) | Free | Keycloak (Docker) | Zero‑cost until scale; seamless JWT compatibility |
| **Real‑Time Chat** | Socket.IO self‑host | ~10 $/mo infra | — | Single‑node; horizontal scale behind ALB |
| **Analytics/BI** | Metabase OSS on PostgreSQL | infra‑only | — | Postgres replica; no SaaS fees |

*Hybrid routing + autoscaling keeps **third‑party spend < 30 %** of total infra.*

---

## 2  Authentication

All protected routes require the header:

```
Authorization: Bearer <accessToken>
```

### 2.1  Sign‑Up `POST /auth/signup`

| Field      | Type   | Required |
|------------|--------|----------|
| fullName   | string | ✔ |
| email      | email  | ✔ |
| password   | string | ✔ |

#### Request
```http
POST /auth/signup HTTP/1.1
Content-Type: application/json

{
  "fullName": "Ada Lovelace",
  "email":    "ada@example.com",
  "password": "Str0ngP@ssw0rd!"
}
```

#### 201 Created
```json
{
  "id": "8f14e45f-ea48-4bb1-bc02-4fea8c737df1",
  "fullName": "Ada Lovelace",
  "email": "ada@example.com",
  "accessToken": "eyJhbGci...",
  "refreshToken": "df1fb2e0-bb56-4e77-86ba-79ab0407a1af",
  "expiresIn": 900
}
```

---

### 2.2  Login `POST /auth/login`

| Field   | Type | Required |
|---------|------|----------|
| email   | email | ✔ |
| password| string | ✔ |

#### Request / Response

```http
POST /auth/login
{
  "email": "ada@example.com",
  "password": "Str0ngP@ssw0rd!"
}
```

```json
// 200 OK
{
  "accessToken":  "eyJhbGci...",
  "refreshToken": "df1fb2e0-bb56-4e77-86ba-79ab0407a1af",
  "expiresIn":    900
}
```

---

### 2.3  Refresh Token `POST /auth/refresh`

```http
POST /auth/refresh
{
  "refreshToken": "df1fb2e0-bb56-4e77-86ba-79ab0407a1af"
}
```

```json
// 200 OK
{
  "accessToken":  "newAccessToken...",
  "refreshToken": "newRefresh...",
  "expiresIn":    900
}
```

---

### 2.4  Logout `POST /auth/logout`

```http
POST /auth/logout
Authorization: Bearer <accessToken>

{
  "refreshToken": "df1fb2e0-bb56-4e77-86ba-79ab0407a1af"
}
```

`204 No Content` on success.

---

## 3  Payments

### 3.1  Create Intent `POST /payments/intents`

| Field  | Type | Required | Notes |
|--------|------|----------|-------|
| amount | int  | ✔ | Smallest currency unit (e.g. **50 00** = $50) |
| currency | string | ✔ | ISO 4217 (USD, BDT) |
| method | enum | ✔ | `card`, `bkash`, `nagad` |

```http
POST /payments/intents
Authorization: Bearer <accessToken>

{
  "amount": 5000,
  "currency": "USD",
  "method": "card"
}
```

```json
// 201 Created
{
  "paymentId": "pi_3Kk123",
  "clientSecret": "pi_3Kk123_secret_4H9x...",
  "status": "requires_confirmation"
}
```

---

### 3.2  Retrieve Intent `GET /payments/{paymentId}`

```http
GET /payments/pi_3Kk123
Authorization: Bearer <accessToken>
```

```json
// 200 OK
{
  "paymentId": "pi_3Kk123",
  "amount": 5000,
  "currency": "USD",
  "status": "succeeded",
  "method": "card"
}
```

---

### 3.3  Webhook `POST /payments/webhook`

*Public endpoint consumed by the payment gateway.*

`200 OK` when signature verified; otherwise `400`.

---

### 3.4  Subscription Endpoints

| Verb | Path | Purpose |
|------|------|---------|
| **GET** | `/subscriptions/plans` | List available plans |
| **POST** | `/subscriptions` | Subscribe to a plan |
| **GET** | `/subscriptions/status` | Current subscription |

All require authentication; payloads follow Stripe‑style primitives.

---

## 4  Learning Services

### 4.1  AI Chat `POST /chat`

| Field    | Type          | Required | Description |
|----------|---------------|----------|-------------|
| message  | string        | ✔ | User question |
| context  | ChatMessage[] | ✖ | Prior history |

```http
POST /chat
Authorization: Bearer <accessToken>

{
  "message": "Explain Maxwell's equations in simple terms"
}
```

```json
// 200 OK
{
  "reply": "Maxwell's equations describe how electric and magnetic fields ...",
  "citations": [
    { "title": "Griffiths — EM (4th ed.)", "page": 300 }
  ],
  "tokensUsed": 512,
  "costUSD": 0.001
}
```

---

### 4.2  Flashcards `POST /flashcards/generate`

```http
POST /flashcards/generate
Authorization: Bearer <accessToken>

{
  "notes": "Photosynthesis converts light energy...",
  "language": "en"
}
```

```json
{
  "cards": [
    { "q": "Define photosynthesis", "a": "Process by which..." },
    { "q": "Where does photosynthesis occur?", "a": "Chloroplasts" }
  ],
  "created": "2025-05-09T12:31:00Z"
}
```

---

### 4.3  Routine Planner

| Verb | Path | Notes |
|------|------|-------|
| **GET**  | `/routine` | List routines |
| **POST** | `/routine` | Create |
| **PATCH**| `/routine/{id}` | Partial update |
| **DELETE** | `/routine/{id}` | Remove |

Create example:

```json
{
  "title": "Math Revision",
  "startTime": "2025-05-10T18:00:00Z",
  "duration": 60,
  "days": ["Mon", "Wed", "Fri"]
}
```

---

### 4.4  Mind Map `POST /mindmaps/generate`

```json
{
  "notes": "Newton's laws describe the relationship..."
}
```

```json
{
  "mindMapUrl": "https://cdn.manetho.io/maps/abc123.svg",
  "nodes": [ ... ],
  "edges": [ ... ]
}
```

---

### 4.5  Practice Tests

| Verb | Path | Purpose |
|------|------|---------|
| **POST** | `/practice-tests` | Create mock exam |
| **GET**  | `/practice-tests/{id}` | Details (questions) |
| **POST** | `/practice-tests/{id}/submit` | Submit answers |

---

### 4.6  Analytics `GET /analytics/summary`

Returns study streak, flashcard stats, quiz scores, and personalized insights.

---

### 4.7  Community

| Verb | Path | Notes |
|------|------|-------|
| **GET** | `/threads` | Public threads |
| **POST**| `/threads` | Create thread |
| **GET** | `/threads/{id}/messages` | List messages |
| **POST**| `/threads/{id}/messages` | Post message |
| **GET** | `/users/{id}/messages` | 1‑to‑1 DM history |
| **POST**| `/users/{id}/messages` | Send DM |

---

## 5  Standard Error Format

```json
{
  "status": 400,
  "error":  "ValidationError",
  "message": "password must be ≥ 8 characters"
}
```

| Code | Meaning |
|------|---------|
| 400 Bad Request | Payload malformed / validation failure |
| 401 Unauthorized | Missing / invalid access token |
| 403 Forbidden | Insufficient privileges |
| 404 Not Found | Resource id does not exist |
| 409 Conflict | Duplicate resource (e.g., email) |
| 500 Internal | Unhandled server error |

---

### 6  Rate Limits & Quotas

| Plan      | Requests / min | Chat tokens / month |
|-----------|----------------|---------------------|
| Free      | 100            | 50 k |
| Pro       | 600            | 1 M |
| Institution | 1000         | 10 M |

429 Too Many Requests → `Retry-After` header (seconds).

---

*Last updated: 2025‑05‑09*
