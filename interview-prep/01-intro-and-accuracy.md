# Intro scripts and accuracy sheet

## 90-second introduction (memorize this)

I am Vaibhav Patil, a senior full-stack engineer with 12 years in Java and web platforms, mostly banking and finance. I own features end to end: analysis, UI, Spring Boot services, deployment, and production support.

On the backend I work with Java 21 and Spring Boot microservices. On the frontend I have been on React for about four years at Intuit; before that I led Angular 8 work at Discover for merchant boarding, and earlier I built Amex middleware around XML and SOAP.

My current client is Intuit QuickBooks: subscription and product-table management for **direct** customers and **accountants** who manage multiple firms. Eligibility drives upgrade and downgrade. A feature I owned end to end is **sales tax exemption**: previously a care agent processed requests manually; we let the customer raise and track the request. The UI is a React plugin. Backend processing is a Kubernetes CronJob that runs an in-process **orchestration Saga**. We deploy Docker images on Kubernetes and troubleshoot with Splunk.

My designation is Senior Lead; on the Intuit engagement I work as an **individual contributor**. Full-time Intuit staff own squad leadership and agile ceremonies. I still own analysis, implementation, deployment, and production support for my features.

I also mentor juniors, do code reviews, and use Cursor and Claude at Intuit for tech-refresh work, not as a substitute for design or production judgment.

**Stop here.** Wait for the next question.

## What not to say in the intro

- Do not say you migrated “Java 16 to Java 18.” That was React 16 to 18.
- Do not list Kafka, Redux Toolkit, and AWS as if every Intuit workflow uses all of them.
- Do not call the personal banking capstone a production system.
- Do not say **Cloud Board** or **Cursory** — the tools are **Claude** and **Cursor**.
- Do not claim **GraalVM native**, **virtual threads in prod**, **Kinesis**, or **Lambda** unless you shipped them.
- Rec 65: do not say backend experience is **two years**. Do not say **SADA**, **RBCD**, or “I don’t remember Kubernetes.”
- Rec 67: **ngModel is not state management.** Do not claim Angular **19** as a production version you shipped.

## Clean migration stories (pick one when asked)

| Story | Honest version |
| --- | --- |
| React | Led React 16 → 18 on SUBS-UI: new root API, automatic batching, more functional components. TypeScript was a tech mandate. |
| Java | Platform tech refresh Java 8 → 21 on services I work with. Backward compatible for most APIs. We adopted **records** and **sealed classes** where DTOs and closed type hierarchies made sense. Claude/Cursor helped mechanical refactors; we still reviewed behavior and tests. |
| Frontend architecture | Older QuickBooks UI was more monolithic. Teams blocked each other. We moved to **plugins/widgets** (micro-frontends) published as versions and composed in an app-fabric / portal. |

## Contradiction sheet (resume vs interviews)

Interviewers compare your resume to what you say. Use **one sentence per project**.

| Topic | Resume wording | What you said in interviews | Say this |
| --- | --- | --- | --- |
| Redux | Redux Toolkit / RTK Query on Intuit | Current app uses Context + `useReducer`; GraphQL via Apollo; Axios for some REST | “On SUBS-UI I introduced RTK Query on the subscription workstream. On the QuickBooks plugin I work daily, shared state is Context and useReducer, and we load data with Apollo Client. I can discuss both.” |
| Kafka | Listed on Intuit environment | Consumer-side experience; tax exemption is Cron + Saga, not Kafka | “I have consumed Kafka where events drive cache or downstream processing. The tax-exemption workflow is a scheduled Saga, not an event choreography.” |
| Angular vs React | Both on resume | Four years React, Angular before that | Lead with React for Intuit roles; mention Angular 8 for Discover if they ask state management. |
| Cloud | AWS, IKS, PCF, OpenShift | Asked “do you have cloud?” | “Production delivery is Kubernetes (IKS) and Docker, with Argo CD and Jenkins. I have worked in AWS-hosted environments and used CloudWatch in some troubleshooting, but I am stronger on K8s + Splunk than on designing a green-field AWS account from scratch.” |
| Banking capstone | 10-service platform in progress | Easy to overclaim | “Personal design exercise for interviews: gateway, JWT, Eureka, Resilience4j. Not a production bank.” |
| Redis | Cache on resume | Rec 65: “Hibernate second-level cache” + “session LRU” + called `useReducer` a cache | “Redis is a shared store: session or hot reads with **TTL**. Hibernate L2 only if we actually wired it. React `useReducer` is UI state, not a cache.” |
| Vault | Secrets | Rec 65: “OAuth2 link to HashiCorp container” | “Secrets live in **Vault** (or K8s Secret from Vault). The app reads env/file at startup. User login is JWT/OAuth2 — that is not how Vault is configured.” |
| Title | Senior Lead | Rec 65 mixed lead vs IC | “Title is Senior Lead; on Intuit I am an IC delivering features. I mentor and review; I do not run the Intuit sprint process.” |
| AI tool names | Cursor, Claude | Rec 67: cloud port, Cursor, skill + MCP; screen share | “**Claude** and **Cursor**. Skills/MCP if we wired them. Cypress is tests, not GenAI. Close the editor before sharing.” |
| Java 21 extras | Records, sealed | Rec 67: virtual threads + GraalVM made Spring 3 faster | “Records and sealed classes we adopted. Virtual threads and GraalVM native I will mention only if we actually enabled them — otherwise ‘evaluated, not in my services.’” |
| Exemption UX | Plugin upload + Cron Saga (HLD) | Rec 67 TurboScribe: 201 → email → **Avalara**, 30 days, one open request, cron 30 min; **no Kafka because small** | Use Avalara if that is real. Keep “not Kafka.” Do not also describe in-plugin-only upload in the same answer. |
| Angular currency | Angular 8 Discover | Rec 67: ngModel as state; guessed Angular 19; four years off | “Discover merchant boarding, Angular 8. State: services + RxJS. ngModel is binding. I would need a short ramp on current Angular; daily is React.” |
| Kafka ack | Consumer commit / producer acks | Rec 67: TID at gateway; ASR **FedEase**; Mongo for large apps | Tracing id in logs/MDC. Not a NoSQL ack database. |
| Years | 12 Java | Rec 65 TurboScribe: “backend **2** years” after React 4 / Angular 3 | “**Twelve** years backend/Java. **Four** React. **Three** Angular.” Enunciate twelve. |
| Pronunciation | Saga, Argo CD, TTL, LRU | Rec 65: SADA, RBCD, DTL, LUR | Practice aloud once a day. |
| K8s depth | Docker + K8s ~5 years | Rec 65: blanked **Service**, called Ingress an engine, “only Argo dashboard” | Four files: Deployment, Service, Ingress, CronJob. Dashboard ≠ “I don’t use K8s.” |

## 2-minute architecture (HLD §21, your voice)

At a high level the product is a React frontend with **plugins**. The browser talks **GraphQL and REST** to Spring Boot microservices. Services are **Docker images on Kubernetes**. An **API gateway** handles routing and edge security. Users authenticate with **JWT/OAuth**; services enforce **roles and permissions**. We use **Redis** for low-latency shared state or cache, a primary database for persistence, and **circuit breakers/fallbacks** so one downstream outage does not take down the page.

A concrete workflow I owned is **tax exemption**. The customer uploads a certificate in the plugin. A **Kubernetes CronJob** triggers the Spring scheduler, which loads pending requests and runs each record through an **in-process orchestration Saga**: lock request, fetch account, build payload, create customer downstream, update our record, update IFS/TES status. A **processing context** stores the stage. If a step fails we stop, **compensate** earlier steps that need it, update retry/status, and continue the **next record**. That is not one distributed database transaction.

**If they ask Kafka next:** “This workflow is a scheduled Saga. We did not put it on Kafka; the volume did not need a broker. Kafka is for pub/sub and independent consumers.”

## Mentoring (Fulcrum / ASTON)

**Structure:** how you review, how you unblock, how you grow people.

I mentor by pairing on the first ticket in a new area, then reviewing PRs for design and tests rather than style nits. I ask juniors to explain the request path before coding. On production issues I have them write the Splunk query with me so they can own the next RCA. I am comfortable guiding an 8-person Discover team or a 10-person Intuit squad because I already did delivery planning and client status there.

## AI tools (ASTON questions 3 and 22)

I use **Cursor and Claude** at Intuit for Java 21 mechanical migration, boilerplate tests, and navigating large plugin repos. If asked about MCP: “Cursor can talk to MCP servers for repo or browser context; I still review the diff.” I do not treat generated code as correct: I still own contracts, idempotency, and production behavior. I have not trained foundation models and I do not do fraud-detection ML. Cypress-from-AI is **trial**, not how we gate releases. Paid tools only if that is policy — say so. My contribution is **safe adoption**: faster refactors with the same review bar.

## Leadership besides code

- Discover: onsite lead, 8 engineers, client communication, boarding workflows.
- Intuit: 24/7 rotation, RCA tickets, feature flags (IXP) with percentage rollout, pre-prod sanity.
- Code review standards: performance, accessibility, not only happy-path UI.

## Practice checklist

- [ ] 90s intro under 100 seconds
- [ ] Contradiction sheet: Redux, Kafka, cloud, capstone, Redis, Vault, Cursor/Claude names, Avalara vs plugin, ack vs correlation id
- [ ] Mentoring in 45 seconds
- [ ] AI answer without saying “AI built the feature”
