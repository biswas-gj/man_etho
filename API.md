# Manetho API Reference — v1

cost-efficient API for Manetho’s AI-powered learning platform.

---

## 1. Cost-Efficiency Strategy

| Service                   | Primary Provider             | Fallback / Self-Host      | Estimated Unit Cost            |
|---------------------------|------------------------------|---------------------------|--------------------------------|
| **Generative AI**         | OpenAI GPT-3.5 Turbo         | Self-hosted Llama 2 (7B)  | $0.002 / 1K tokens             |
| **Embeddings & Search**   | OpenAI Embeddings            | Qdrant on-prem            | $0.0004 / 1K tokens            |
| **Storage & Uploads**     | DigitalOcean Spaces          | MinIO                     | $5 / 250 GB-mo                 |
| **Chat & Community**      | Socket.IO (self-hosted)      | —                         | Infra cost only                |
| **Auth & Users**          | Auth0 free tier              | Keycloak                  | Free up to 7K MAU              |
| **Analytics & BI**        | Metabase OSS on PostgreSQL   | —                         | Infra cost only                |

> **Note:** Hybrid routing (e.g. batch jobs → Llama 2, cold vectors → Qdrant) keeps third-party spend < 30% of total infra cost.

---

## 2. Base URL & Versioning

**Base URL**  
```
https://api.manetho.io/v1
```

All endpoints are versioned under `/v1`.  
Timestamps use **ISO 8601 UTC**.

---

## 3. Authentication

### 3.1 Obtain Tokens

```http
POST /auth/login
Content-Type: application/json
```
**Request Body**
```json
{ "email": "you@example.com", "password": "••••••••" }
```
**200 OK**
```json
{
  "accessToken": "eyJ…",
  "refreshToken": "eyJ…",
  "expiresIn": 3600
}
```

### 3.2 Refresh Token

```http
POST /auth/refresh
Content-Type: application/json
```
**Request Body**
```json
{ "refreshToken": "eyJ…" }
```
**200 OK**
```json
{ "accessToken": "new…", "expiresIn": 3600 }
```

> **Header (all protected):**  
> `Authorization: Bearer <accessToken>`

---

## 4. Error Responses

| HTTP Code | Reason       | Body                             |
|---------:|--------------|----------------------------------|
| **400**  | Bad Request  | `{ "error": "Validation failed" }` |
| **401**  | Unauthorized | `{ "error": "Unauthorized" }`      |
| **403**  | Forbidden    | `{ "error": "Forbidden" }`         |
| **404**  | Not Found    | `{ "error": "Not found" }`         |
| **500**  | Server Error | `{ "error": "Internal error" }`    |

---

## 5. Endpoints

### 5.1 AI Chatbot
**POST** `/chat`  
Ask academic questions; receive reference-backed answers.

- **Auth:** Required  
- **Body**
  ```json
  {
    "message": "Explain Pythagorean theorem",
    "context": [{ "role": "user", "message": "What is c?" }]
  }
  ```
- **200 OK**
  ```json
  {
    "reply": "a² + b² = c² …",
    "references": [{ "title": "Euclid’s Elements", "url": "…" }]
  }
  ```

### 5.2 Flashcard Generator
**POST** `/flashcards/generate`  
Generate Q&A cards from notes/docs.

- **Auth:** Required  
- **Body**
  ```json
  {
    "notes": "Photosynthesis is …",
    "documentUrl": "https://…/notes.pdf"  // optional
  }
  ```
- **200 OK**
  ```json
  {
    "cards": [
      { "q": "What is photosynthesis?", "a": "…" },
      { "q": "Where does it occur?", "a": "Chloroplasts" }
    ]
  }
  ```

### 5.3 Study Routine Planner
- **GET** `/routine` — List all routines  
- **POST** `/routine` — Create a new routine  
  - **Body**
    ```json
    {
      "title": "Math Revision",
      "startTime": "2025-05-02T18:00:00Z",
      "duration": 60,
      "days": ["Mon","Wed","Fri"]
    }
    ```
- **PATCH** `/routine/{id}` — Update a routine  
- **DELETE** `/routine/{id}` — Delete a routine

### 5.4 Mind Map Generator
**POST** `/mindmaps/generate`  
Generate concept maps from notes.

- **Body**
  ```json
  { "notes": "Newton’s laws…" }
  ```
- **200 OK**
  ```json
  {
    "mindMapUrl": "https://…/map/abc123",
    "nodes": [ … ],
    "edges": [ … ]
  }
  ```

### 5.5 Practice Tests
- **POST** `/practice-tests` — Create mock exams  
  - **Body**
    ```json
    {
      "title": "Bio Midterm",
      "topics": ["Respiration"],
      "numQuestions": 10,
      "timeLimit": 30
    }
    ```
- **GET** `/practice-tests/{id}` — Get test details  
- **POST** `/practice-tests/{id}/submit` — Submit answers

### 5.6 Analytics & Performance
**GET** `/analytics/summary`  
Fetch user metrics.

- **200 OK**
  ```json
  {
    "streak": 5,
    "cardsReviewed": 120,
    "avgQuizScore": 82.4,
    "insights": ["Focus on thermodynamics"]
  }
  ```

### 5.7 Community Module
- **GET** `/threads` — List threads  
- **POST** `/threads` — Create a thread  
- **GET** `/threads/{id}/messages` — List messages  
- **POST** `/threads/{id}/messages` — Post a message  
- **GET/POST** `/users/{id}/messages` — 1:1 messaging

### 5.8 Subscriptions & Billing
- **GET** `/subscriptions/plans` — List available plans  
- **POST** `/subscriptions` — Subscribe to a plan  
- **GET** `/subscriptions/status` — Check subscription status

---

## Conventions & Notes

- **Rate Limit:** 100 req/min  
- **Data Format:** JSON, camelCase fields  
- **Interactive Docs:** OpenAPI 3.0 + Swagger UI  
- **Versioning:**  
  - Non-breaking changes → PATCH  
  - Breaking changes → v2

---
