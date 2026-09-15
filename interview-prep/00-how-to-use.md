# How to use this handbook

This kit is for **Vaibhav Patil**: 12 years Java + React/Angular, banking and finance, currently Intuit QuickBooks (Virtusa). It is **documentation only**. Pair it with your existing HLD/LLD PDF (tax-exemption Cron + orchestration Saga). Do not treat this repo’s blank CDK TypeScript app as interview material.

## Rules while you study

1. **Accuracy first.** If you did not run Kafka on a workflow, do not mention Kafka when explaining that workflow.
2. **Speak HLD, then one feature, then LLD.** Do not start with class names.
3. **Live coding without Cursor.** Interviews will catch “I usually generate this.” Type from memory.
4. **45–90 second answers.** Interviewers in recording 63 asked for brief answers. Stop after the example.

## 7-day drill order

| Day | Focus | Files | Exit criteria |
| --- | --- | --- | --- |
| 1 | Java Streams + HashMap + Records | [10-live-coding-drills.md](10-live-coding-drills.md), [04-backend-java-spring.md](04-backend-java-spring.md) | First non-repeating character and customer-totals in under 10 minutes |
| 2 | React live coding + SQL top-N | [03-frontend.md](03-frontend.md), [06-data-sql-acid-streams.md](06-data-sql-acid-streams.md), drills | Filter/sort/map table + add/remove; second-highest salary; top-3 customers |
| 3 | Intro + request path UI → K8s | [01-intro-and-accuracy.md](01-intro-and-accuracy.md), [02-request-path-ui-to-k8s.md](02-request-path-ui-to-k8s.md) | Recite 90s intro and interactive request path without notes |
| 4 | Cron/Saga + resilience | HLD PDF §§6–16, [05-distributed-microservices.md](05-distributed-microservices.md) | Happy path, failure + compensate, overlap guard, retry vs CB |
| 5 | Docker, K8s, CI/CD, AWS | [07-docker-k8s-cicd-aws.md](07-docker-k8s-cicd-aws.md) | Image vs container, CronJob `Forbid`, Jenkins plugin version, honest AWS |
| 6 | Question bank aloud | [09-question-bank.md](09-question-bank.md), [08-observability-prod.md](08-observability-prod.md) | Every company list answered once, timed |
| 7 | Full mock | All | Intro → HLD → tax-exemption LLD → one coding problem → one K8s/Kafka question |

## Recommended 2-minute architecture (from HLD §21)

Use this as the spine, then zoom into tax exemption. Full script is in [01-intro-and-accuracy.md](01-intro-and-accuracy.md).

## File map

| File | Purpose |
| --- | --- |
| [01-intro-and-accuracy.md](01-intro-and-accuracy.md) | Intro scripts, contradiction sheet, mentoring, AI |
| [02-request-path-ui-to-k8s.md](02-request-path-ui-to-k8s.md) | Interactive path + Cron/Saga zoom-in |
| [03-frontend.md](03-frontend.md) | React MFE, GraphQL, performance, Angular 8 |
| [04-backend-java-spring.md](04-backend-java-spring.md) | Spring, Java 21, DI, Batch, banking patterns |
| [05-distributed-microservices.md](05-distributed-microservices.md) | Gateway, Kafka, retry, Saga, cache events |
| [06-data-sql-acid-streams.md](06-data-sql-acid-streams.md) | SQL, ACID, Streams |
| [07-docker-k8s-cicd-aws.md](07-docker-k8s-cicd-aws.md) | Delivery stack |
| [08-observability-prod.md](08-observability-prod.md) | Splunk, correlation ID, RCA |
| [09-question-bank.md](09-question-bank.md) | Fulcrum, LTI, ASTON, Virtusa, recording 63 |
| [10-live-coding-drills.md](10-live-coding-drills.md) | Timed problems + gold solutions |

## Out of scope

Recordings 65 and 67 were image PDFs and are not transcribed here. If you paste those transcripts later, patch [09-question-bank.md](09-question-bank.md).
