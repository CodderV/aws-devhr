# Docker, Kubernetes, CI/CD, AWS

ASTON asked Docker benefits and AWS scalability. Virtusa asked docker image. Recording 63: you deploy images and use a Kubernetes CronJob — go one level deeper without pretending you are a platform engineer.

## Docker (ASTON Q11–Q12)

**Image** — immutable filesystem + metadata + start command. Built from a `Dockerfile`.  
**Container** — running (or stopped) instance of an image.

**Why it helped the lifecycle (say this):**

1. **Dev/prod parity** — same OpenJDK, same OS libs, no “works on my Tomcat.”
2. **CI** — Jenkins builds the image once, scans it, pushes to a registry; E2E/pre-prod/prod pull **the same digest**.
3. **K8s** — scheduler runs that image as pods; scale by replica count, not by installing WARs on VMs.
4. **Isolation** — plugin/backend dependencies do not collide on a shared VM.

**Multi-stage build (interview-level):** compile in a Maven image, copy the JAR into a slim JRE image — smaller attack surface.

**Dockerfile vs “Docker YAML” (recording 65):** the **image** is built from a **Dockerfile**. `docker-compose.yml` is optional for **local** multi-container runs. Do not say the pipeline “uses a Docker YAML to configure the container” unless you mean Compose for laptop only.

**What you should not claim:** you wrote every Helm chart. You **consume** images and CronJob manifests and can read `kubectl` logs.

**Validate the image in CI:** Dockerfile builds; unit/integration tests; (if you have it) Trivy/scan; smoke `docker run` or a compose stack; only then push the **digest** to the registry. Argo CD is **not** the image validator — it syncs a **already built** image into the cluster.

**Why Docker (rec 65, keep this):** same artifact from laptop → CI → pre-prod → prod, so JDK/OS drift is not a production bug.

## Kubernetes objects (minimum senior set)

| Object | Role |
| --- | --- |
| Pod | One or more containers; ephemeral |
| Deployment | Desired replica count, rolling update |
| Service | Stable DNS/IP to pods |
| Ingress / Gateway | HTTP entry from outside |
| ConfigMap / Secret | Config vs credentials |
| CronJob | Time-based Job → Pod |
| HPA | CPU/custom metric → more replicas |

**Tax exemption:** `CronJob` → `Job` → `Pod` running the Spring scheduler/pipeline. `concurrencyPolicy: Forbid` (HLD §7).

**Scalability:** stateless APIs: increase Deployment replicas; HPA. Sessions in Redis, not in the pod. Jobs: scale workers carefully with locks so two pods do not process one exemption.

**Reliability:** liveness (restart deadlocks) vs readiness (stop traffic until up). Pod disruption budgets if you know them; otherwise rolling updates + multiple replicas.

**Ingress (rec 65):** HTTP router at the cluster edge — host/path → Service → pods. It does not “check the request path” instead of the app; it **selects which Service** gets the request (and TLS). AuthZ still belongs in the service.

**Manifests you should name:** `Deployment` (replicas, image, probes), `Service` (stable DNS), `Ingress` (or gateway), `CronJob` for exemption, `ConfigMap`/`Secret`. “I did not author the platform chart; I can read and change image tag, replicas, and Cron schedule.”

**Argo CD:** **GitOps** — cluster state matches Git. The UI shows sync/health and you can rollback a sync. Monitoring pods is a **side effect of the dashboard**, not the reason you use Argo. Day-to-day you may only watch the UI; still say GitOps first.

**IKS** on your resume = IBM Kubernetes Service / Intuit’s K8s — say “managed Kubernetes.”

**Local without Argo (rec 65):** `mvn spring-boot:run` or IDE; `docker compose up` for Redis/DB; Swagger/OpenAPI for the API. That is correct — keep it.

**S3 (rec 65):** tax-exemption **certificate files**. Say durability + private bucket + pre-signed upload if that is true; metadata stays in the DB.

## CI/CD as you described it (keep this story)

**Frontend plugin:** Git push → **Jenkins** pipeline → artifact **plugin version** → pin version in **dev portal / app fabric** on E2E and pre-prod → prod after sanity. Rollback = pin previous version. Feature flags (IXP) decouple deploy from release.

**Backend:** Docker image tagged with git SHA → registry → **Argo CD** (GitOps) syncs the cluster to the desired manifest. You listed Argo CD on Intuit CSS/Bills era — use it there; if a newer stack differs, say Jenkins + K8s deploy without inventing tools.

**PCF / OpenShift / WebSphere** = earlier employers (Discover, Amex). One sentence if they ask history.

## AWS (ASTON Q2, Q13) — honest senior answer

“I have shipped on AWS-hosted environments and used CloudWatch when that was the log sink. Day to day at Intuit I operate **Kubernetes, Docker, Jenkins, Argo CD, Splunk, Wavefront**. If I design a typical three-tier on AWS:”

- **Reliability:** multi-AZ; ALB health checks; RDS Multi-AZ; S3 durable static assets (you mentioned CDN for images).
- **Scalability:** ASG or EKS HPA; cache (ElastiCache/Redis); do not put sessions on the instance.
- **Security:** SG least privilege, IAM roles for pods/instances, secrets not in images. **HashiCorp Vault** (or K8s Secret synced from Vault) for DB passwords and API keys. The pod authenticates to Vault with **Kubernetes/AppRole**, not with the user’s JWT. Spring reads `VAULT_` env or a mounted file. If you did not write the Vault policy, say so: “I consume secrets; platform owns the cluster auth.”
- **Deploy:** immutable AMIs or EKS images; blue/green or rolling.

If they drill VPC/CIDR and you are weak: “I partner with platform teams on account-level networking; I own the app Deployment, probes, and autoscaling policies.”

## Connecting the delivery path to Path A

Developer → PR → Jenkins tests (Jest/JUnit, Sonar) → image or plugin version → pre-prod → Argo/K8s → prod → Splunk dashboards/alerts. Same artifact through the pipeline is the Docker benefit they want.
