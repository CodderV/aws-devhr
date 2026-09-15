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

**What you should not claim:** you wrote every Helm chart. You **consume** images and CronJob manifests and can read `kubectl` logs.

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

**IKS** on your resume = IBM Kubernetes Service / Intuit’s K8s — say “managed Kubernetes.”

## CI/CD as you described it (keep this story)

**Frontend plugin:** Git push → **Jenkins** pipeline → artifact **plugin version** → pin version in **dev portal / app fabric** on E2E and pre-prod → prod after sanity. Rollback = pin previous version. Feature flags (IXP) decouple deploy from release.

**Backend:** Docker image tagged with git SHA → registry → **Argo CD** (GitOps) syncs the cluster to the desired manifest. You listed Argo CD on Intuit CSS/Bills era — use it there; if a newer stack differs, say Jenkins + K8s deploy without inventing tools.

**PCF / OpenShift / WebSphere** = earlier employers (Discover, Amex). One sentence if they ask history.

## AWS (ASTON Q2, Q13) — honest senior answer

“I have shipped on AWS-hosted environments and used CloudWatch when that was the log sink. Day to day at Intuit I operate **Kubernetes, Docker, Jenkins, Argo CD, Splunk, Wavefront**. If I design a typical three-tier on AWS:”

- **Reliability:** multi-AZ; ALB health checks; RDS Multi-AZ; S3 durable static assets (you mentioned CDN for images).
- **Scalability:** ASG or EKS HPA; cache (ElastiCache/Redis); do not put sessions on the instance.
- **Security:** SG least privilege, IAM roles for pods/instances, secrets not in images.
- **Deploy:** immutable AMIs or EKS images; blue/green or rolling.

If they drill VPC/CIDR and you are weak: “I partner with platform teams on account-level networking; I own the app Deployment, probes, and autoscaling policies.”

## Connecting the delivery path to Path A

Developer → PR → Jenkins tests (Jest/JUnit, Sonar) → image or plugin version → pre-prod → Argo/K8s → prod → Splunk dashboards/alerts. Same artifact through the pipeline is the Docker benefit they want.
