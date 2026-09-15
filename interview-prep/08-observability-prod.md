# Observability and production support

You already sound credible here (24/7, Splunk, Wavefront, RCA tickets). Add **correlation IDs** so Virtusa L2 “tracing” is not a shrug.

## What to emit

- **Logs:** JSON, `correlationId`, `userId` (if allowed), `exemptionRequestId`, `sagaStage`, `retryCount`, `httpStatus`, latency.
- **Metrics:** Wavefront/Prometheus — error rate, p99, TES client failures, Cron duration, Kafka lag if applicable.
- **Traces:** if a tracer exists, same id; if not, **log correlation is the honest answer**.

## Interactive failure (HLD §19)

1. User reports or alert fires.
2. Take **time, user/firm, correlation id** from the UI (expose it in a support footer if you have it).
3. Splunk: `correlationId=abc` or `exemptionRequestId=`.
4. See which service and stage failed (gateway 401 vs TES 500 vs DB timeout).
5. Check Wavefront: was it a spike or one user.
6. Fix forward (retry job, data repair) + **RCA ticket** with root cause and test gap.

## Failed Saga in Splunk

Search request id → events in stage order: `LOCKED`, `ACCOUNT_FETCHED`, `CUSTOMER_CREATED`, `COMPENSATING`, `FAILED`. If `CUSTOMER_CREATED` exists but DB update missing, that is the classic “compensate or retry DB” question.

## 24/7 rotation story (CSS/Bills/PRIME)

Owned features through rollout and support. Multiple Intuit platforms. Coordinated partner teams. Do not dump every acronym; pick one incident pattern: “latency on bill metadata, Splunk showed downstream timeout, circuit breaker opened, fallback message, then TES recovered.”

## Dashboards and alerts

You mentioned Splunk dashboards and alerts for feature-flag rollouts. Say: error budget on the new plugin version, rollback by pinning the previous plugin if IXP or version pin allows.

## What not to do in the interview

Do not invent OpenTelemetry if you never used it. “MDC correlation id + Splunk + Wavefront” is senior enough when it is true.
