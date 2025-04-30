# man_etho

Below is a template for your **Scope Documentation** in Markdown. You can fill in or adjust any sections to match your team’s needs.

---

## 1. Introduction
**Project Name:** Manetho  
**Version:** 1.0  
**Date:** 2025-04-30  
**Prepared By:** Rifat Hossain, Gourab Biswas, Musa Tur Farazi  

Briefly describe the purpose of this document and the overall goal of the Manetho project.

---

## 2. Project Overview
Manetho is an AI-powered learning management platform designed to unify study tools, improve study routines, and build a strong learning community. It addresses inconsistent study patterns, fragmented resources, tool overload, and low self-study motivation.

---

## 3. Objectives
- **O1:** Provide an AI chatbot for instant academic Q&A.  
- **O2:** Automate flashcard generation from user notes and documents.  
- **O3:** Offer a personalized study routine planner.  
- **O4:** Enable collaborative community features (forums, group chat).  
- **O5:** Track and visualize learning progress with dashboards.

---

## 4. Scope Description

### 4.1 In-Scope
- **AI Chatbot**  
  - Natural-language question parsing  
  - Reference-backed detailed answers  
- **Flashcard Generator**  
  - Text upload & parsing  
  - Concept extraction & Q&A pair creation  
- **Study Routine Planner**  
  - Goal setting & priority rules  
  - Smart scheduling based on user habits  
- **Progress Tracker**  
  - Daily/weekly streaks  
  - Syllabus completion charts  
- **Community Module**  
  - Public posts & threads  
  - Topic-based channels  
  - 1:1 student-teacher messaging  
- **Subscription & Billing**  
  - Three-tier plans (Basic, Standard, Premium)  
  - Integration with bKash and online payment gateways

### 4.2 Out-of-Scope
- Mobile-only native applications (will be web-first)  
- Third-party LMS integrations beyond basic imports (e.g., Moodle)  
- Offline study mode or offline content caching  
- Non-academic content (e.g., entertainment)

---

## 5. Deliverables
1. **Functional Prototype:** Core AI chatbot and flashcard features.  
2. **Beta Release:** Routine planner, progress tracker, community module.  
3. **Production Release:** All modules, subscription billing, UI polish.  
4. **Documentation:** User guide, API reference, deployment manual.  
5. **Testing Reports:** Unit, integration, and usability test results.

---

## 6. Constraints
- **Technology:** Must use existing open-source AI APIs (e.g., OpenAI).  
- **Budget:** Limited to student startup funding.  
- **Timeline:** MVP within 4 months; full launch in 8 months.  
- **Regulatory:** Data privacy compliance (GDPR-like standards).

---

## 7. Assumptions
- Users have reliable internet access.  
- AI API quotas will be sufficient for beta usage.  
- Payment gateways (bKash) will provide test environment.  
- Initial user base will be university students in Bangladesh.

---

## 8. Acceptance Criteria
- AI chatbot returns accurate, reference-backed answers ≥ 85% of queries.  
- Flashcards cover ≥ 75% of key concepts in uploaded documents.  
- Routine planner achieves ≥ 70% adherence rate in user testing.  
- Community features support ≥ 100 concurrent users without lag.  
- Billing workflow processes test transactions end-to-end.

---

## 9. Stakeholders
- **Product Owners:** Rifat Hossain, Gourab Biswas, Musa Tur Farazi  
- **Developers:** Frontend, backend, AI integration teams  
- **Users:** Students, tutors, academic institutions  
- **Sponsors:** University innovation fund, angel investors  
- **External Vendors:** AI API providers, payment gateway partners

---

## 10. Timeline & Milestones
| Milestone                | Target Date  | Owner         |
|--------------------------|--------------|---------------|
| Requirements Sign-Off    | 2025-05-15   | All           |
| Prototype Release        | 2025-08-01   | Dev Team      |
| Beta Launch              | 2025-10-15   | Dev & QA      |
| User Testing & Feedback  | 2025-11-30   | QA & UX       |
| Production Launch        | 2026-01-10   | All           |

---

## 11. Dependencies
- **AI Service Availability:** OpenAI (or equivalent) uptime  
- **Payment Gateway Testing:** bKash sandbox readiness  
- **Hosting & Infrastructure:** Cloud provider provisioning  
- **Third-Party Libraries:** Licence compliance

---

## 12. Risks & Mitigations
| Risk                                | Impact   | Mitigation                                         |
|-------------------------------------|----------|----------------------------------------------------|
| AI API rate limits                  | High     | Implement caching; negotiate higher quotas         |
| Payment integration delays          | Medium   | Parallel integration of alternate gateway          |
| Security/data privacy vulnerabilities | High   | Conduct regular security audits; encryption at rest |
| Low initial user adoption           | Medium   | University partnerships; pilot programs            |

---

*Feel free to adjust any section or add more details as needed!*
