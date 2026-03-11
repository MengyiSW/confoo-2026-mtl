# ConFoo Conference — Day 1 Notes
*Frontend, Security, AI, I/O & DevOps*

---

## Session 1 — Boosting Frontend Speed: Quick Wins for Backend Developers
**Speaker:** Paul Conroy

Paul's talk zeroed in on a common blind spot for backend-focused developers: the browser's rendering pipeline and how small, measurable changes to assets like images, fonts, and videos can dramatically improve user experience. We don't need deep frontend expertise — just the right metrics and the right tools.

### Core Web Vitals & Measurement

| Metric | What It Measures | Target |
|--------|-----------------|--------|
| **CLS** (Cumulative Layout Shift) | Visual stability — how much elements jump around unexpectedly | < 0.1 |
| **LCP** (Largest Contentful Paint) | When the largest visible element renders (hero image, heading) | < 2.5s |
| **INP** (Interaction to Next Paint) | Responsiveness to all user interactions throughout the page lifecycle (replaced FID in March 2024) | < 200ms |
| **Lighthouse / PageSpeed Insights** | Lighthouse runs locally in Chrome DevTools; PageSpeed Insights uses real-world CrUX data | — |

### Images: Format Modernization

Switching from JPEG to modern formats delivers dramatic size reductions with no perceptible quality loss:

- **WebP** — ~25–35% smaller than JPEG at equivalent quality. Supported across all major browsers since 2020.
- **AVIF** — ~50% smaller than JPEG. Based on the AV1 codec. Excellent for photographic content. Broad support in Chrome, Firefox, and Safari 16+.
- Always set explicit `width` and `height` attributes on `<img>` tags to prevent CLS — the browser pre-allocates space before the image loads.
- Use `loading="lazy"` for below-the-fold images so critical images load first, improving LCP.

### Ads & Layout Stability

Ads are a notorious source of CLS because their sizes are often unknown until they render:

- Reserve fixed space for ad slots using CSS `min-height` before the ad loads — this prevents content below from jumping.
- **The 2-click penalty:** a layout shift that causes a user to accidentally click the wrong element leads to frustration and potential abandonment. Google's ranking algorithms penalise high-CLS sites.

### Videos

- Use a `poster` image (thumbnail) so the page renders visually complete before the video player initialises.
- Lazy-load video iframes with the `loading` attribute or an Intersection Observer.
- Search engines can't play videos — supplement with `VideoObject` structured data and a text summary to protect SEO.

### Fonts

- **Font subsetting:** only load the characters our language actually uses. A full Latin font can exceed 200KB; a subset may be under 30KB.
- Use `font-display: swap` to prevent invisible text (FOIT) while the custom font loads.
- The `size-adjust` CSS descriptor matches a fallback font's metrics to our custom font, virtually eliminating layout shift during font swap.
- Preload critical fonts with `<link rel="preload">` to fetch them earlier in the loading waterfall.

### Key Takeaways

- ✅ Measure first with Lighthouse and PageSpeed Insights — data drives prioritization.
- ✅ Switching images from JPEG to WebP/AVIF is a one-time change with lasting 25–50% size savings.
- ✅ Always set explicit image dimensions to prevent CLS and protect our Core Web Vitals score.
- ✅ Font subsetting and `font-display: swap` are quick wins that improve both perceived and measured performance.
- ✅ Reserve ad slot space in CSS upfront to prevent the 2-click penalty and layout instability.

---

## Session 2 — From Vulnerability to Victory: Java Security Essentials
**Speaker:** Anthony Dahanne

Anthony demystified the Java security ecosystem from the ground up: how vulnerabilities are discovered, scored, and disclosed; which databases track them; and which tools we can plug into our build pipeline to detect and remediate issues. He also addressed the underrated risk of framework End-of-Life (EOL).

### The Vulnerability Lifecycle

- **Discovery:** Researchers or automated scanners find a flaw in a library or runtime.
- **CVSS Scoring:** The Common Vulnerability Scoring System rates severity 0–10. Scores above 9.0 are Critical and require immediate action.
- **Disclosure:** Coordinated disclosure is the norm — vendors are notified privately before public announcement to allow patching.
- **Databases:** NVD (National Vulnerability Database), GitHub Advisory Database, and OSS Index are the primary sources. Tools query these to check our dependencies.

### Key Tools

| Tool | Purpose |
|------|---------|
| **SBOM** (Software Bill of Materials) | Machine-readable inventory of all dependencies and versions. Formats: SPDX and CycloneDX. Instantly tells we if we ship a vulnerable library. |
| **Qodana** | JetBrains' static analysis platform. Runs security and code quality inspections in CI. Native IntelliJ integration. |
| **FindSecurityBugs** | SpotBugs plugin for security patterns in Java bytecode. Detects SQL injection, XSS, insecure random, hardcoded credentials, and more. |
| **SonarQube** | SAST for continuous inspection. Tracks issues over time and enforces quality gates in CI. Community Edition is free. |
| **GitLab SAST** | Built-in scanning that auto-detects language and runs analyzers on merge requests. Results surface in the Security Dashboard. |
| **Veracode** | Commercial SAST/DAST/SCA platform for enterprise compliance (SOC 2, PCI DSS). Integrates with CI/CD and IDEs. |
| **VEX** (Vulnerability Exploitability eXchange) | Companion to SBOMs. Declare that a known CVE is not exploitable in our context — reduces alert fatigue. |

### Handling Framework End-of-Life

When a framework like Spring Boot 2.x or Java 8 reaches EOL, it no longer receives security patches:

- Track EOL dates at [endoflife.date](https://endoflife.date) — a community-maintained resource for every major framework and runtime.
- Build EOL upgrades into our quarterly roadmap, not as emergency work.
- SBOM tooling can flag EOL components alongside CVEs, centralizing risk management.

### Key Takeaways

- ✅ Generate and maintain an SBOM as part of every release — it's our security bill of lading.
- ✅ Integrate FindSecurityBugs or SonarQube into CI so vulnerabilities are caught at the PR stage, not post-deployment.
- ✅ Use VEX files to suppress false positives and keep security noise manageable.
- ✅ Treat framework EOL with the same urgency as a high-severity CVE — no patches means compounding risk.
- ✅ CVSS score alone doesn't determine real-world risk; context (exposure, exploitability) matters.

---

## Session 3 — Beyond the Chatbot, Beyond the IDE: AI in my CI/CD
**Speaker:** Justine Gehring

Justine challenged the narrow framing of AI as just a code autocomplete tool. The talk laid out a taxonomy of five AI capability families and showed how each maps to different stages of the software delivery lifecycle — from requirements gathering through post-deployment monitoring.

### The Five AI Capability Families

| Family | CI/CD Application |
|--------|------------------|
| **Learning & Knowledge** | Ingests documentation, runbooks, and incident history to answer developer questions. Reduces onboarding time and makes institutional knowledge searchable. |
| **Coding Assistants** | GitHub Copilot, Cursor, JetBrains AI — generating, explaining, refactoring, and reviewing code. The differentiator is how well-integrated they are into our workflow. |
| **Automation & Agents** | Autonomously executes multi-step tasks: running test suites, triaging failed pipelines, opening PRs for dependency updates. Reduces toil. |
| **Planning** | AI-assisted sprint forecasting and process assessment. Uses historical velocity and defect data to surface bottlenecks and predict delivery risk. |
| **Data Exploration** | Natural-language querying of logs, metrics, and databases. Developers can investigate production issues without writing complex SQL or PromQL. |

### Practical Applications

- **VM migration planning:** AI analyses infrastructure inventory and recommends migration sequencing and target specs, cutting manual discovery effort significantly.
- **CI pipeline triage:** Agents read failing job logs, identify root cause categories, and suggest or apply fixes autonomously.
- **Post-deployment monitoring:** AI correlates deployment events with metric anomalies, surfacing likely regressions faster than manual review.
- **Process assessment:** AI reviews DORA metrics and identifies the highest-leverage improvement areas.

The session referenced **Grainger's** real-world AI integration as evidence that these patterns deliver measurable ROI at scale. Key insight: AI integration centered around *developers* (rather than tools) yields greater adoption and impact.


---

## Session 4 — A Java Developer's Quest for I/O Performance
**Speaker:** David Vlijmincx

David walked through his personal journey building high-performance Java I/O bindings on top of io_uring — the modern Linux async I/O interface. The talk was as much about *how to measure correctly* as it was about the specific optimizations discovered.

### Key Concepts

| Concept | What It Means |
|---------|--------------|
| **io_uring** | A Linux kernel interface (since 5.1) for async I/O using shared memory ring buffers between user space and the kernel. Dramatically reduces syscall overhead versus traditional read/write. |
| **Proactor Pattern** | Async I/O design pattern where the OS *completes* the I/O and notifies our handler — contrast with Reactor which notifies when I/O is *ready*. io_uring is inherently proactor-style. |
| **Project Panama / Foreign Function & Memory API** | Java's modern mechanism for calling native code and managing off-heap memory without JNI boilerplate. |
| **Arena / Confined Memory** | Panama's `MemorySegment` arenas provide scoped, off-heap memory with deterministic lifetime — critical when passing buffers directly to the kernel to avoid GC interference. |
| **Zero-Copy** | Avoiding unnecessary buffer copies between kernel and user space. io_uring supports registered buffers the kernel writes into directly. |
| **Slicing vs Panama APIs** | A deliberate trade-off: slicing existing segments is faster for repeated operations; Panama's higher-level APIs are safer. |
| **Protocol Records** | Using Java records to model I/O protocol structures that map cleanly to native memory layouts, improving readability and pointer arithmetic correctness. |

### Lessons from Benchmarking

- **Misleading benchmarks:** micro-benchmarks showed promising results that disappeared under realistic workloads. Always test with production-like I/O patterns and concurrency.
- **JMH** (Java Microbenchmark Harness) is mandatory for trustworthy Java benchmarks — naive `System.currentTimeMillis()` measurements are unreliable due to JIT and GC.
- `FileChannel` is convenient but adds significant overhead versus direct io_uring calls, especially at high concurrency.
- Profiler runs revealed unexpected hotspots in buffer management, *not* the I/O path itself.

### Key Takeaways

- ✅ io_uring is a generational leap in Linux I/O — worth understanding even if we don't write low-level bindings.
- ✅ Project Panama / Foreign Function API is the modern, safe path for Java-native interop — prefer it over JNI.
- ✅ Off-heap arenas give we GC-safe buffers for kernel I/O — critical for avoiding stop-the-world pauses in high-throughput systems.
- ✅ Measure correctly: use JMH, test with realistic workloads, and trust the profiler over intuition.
- ✅ Zero-copy techniques can 2–5x throughput in I/O-bound applications.

---

## Session 5 — Context-Aware Copilots for Next.js
**Speaker:** Ron Dagdag

Ron demonstrated how to embed a truly context-aware AI copilot into 3 or 4 Next.js application — one that reads live UI state (form values, route parameters, selected items) rather than operating in isolation. The talk covered the architecture from chat routing through tool calls to safety guardrails.

---

## Session 6 — DevOps for the Age of AI: Introducing MLOps
**Speaker:** Semira Allen

Semira argued that the principles of DevOps — automation, repeatability, fast feedback — are necessary but insufficient for machine learning workloads. ML models introduce new failure modes: data drift, model degradation, non-deterministic behaviour, and long training cycles. MLOps extends DevOps to handle these realities.

### Where DevOps Falls Short for ML

| Concern | The ML Problem |
|---------|---------------|
| **Reproducibility** | Traditional apps are deterministic given the same code. ML models depend on training data, hyperparameters, and framework versions — all of which must be versioned together. |
| **Testing** | Unit and integration tests don't capture model quality. ML requires evaluation against held-out datasets, fairness checks, and performance benchmarks. |
| **Deployment** | Rolling deployments work for services. ML models may need shadow mode, A/B testing, or champion/challenger patterns to validate before full rollout. |
| **Monitoring** | Traditional monitoring tracks error rates and latency. ML monitoring must also track prediction distribution shifts (data drift) and accuracy degradation over time. |
| **Retraining** | Applications are updated via code commits. Models must be periodically retrained on new data via automated pipelines separate from (but integrated with) CI/CD. |

### The MLOps Lifecycle

- **Data versioning:** Tools like DVC (Data Version Control) track dataset versions alongside model versions.
- **Experiment tracking:** MLflow, Weights & Biases, or Neptune log hyperparameters, metrics, and artifacts for every training run.
- **Model registry:** A central store of trained model versions with promotion workflows (staging → production).
- **Automated retraining pipelines:** Triggered by data drift detection, scheduled cadence, or performance threshold breaches.
- **Feature stores:** Centralise feature engineering logic so training and inference use identical transformations — eliminating training/serving skew.

### Key Takeaways

- ✅ Treat model weights, training data, and hyperparameters as first-class versioned artifacts — not just code.
- ✅ Monitor for data drift and model degradation in production; accuracy degrades silently without it.
- ✅ Automate retraining pipelines the same way we automate build pipelines — on triggers, not manual intervention.
- ✅ Training/serving skew is a silent killer; feature stores eliminate it by sharing transformation logic.
- ✅ MLOps is not a separate discipline — it's DevOps extended, and the same principles apply.

# 🤩 Favorite
- Boosting Frontend Speed: Quick Wins for Backend Developers - Now for Compass
- From Vulnerability to Victory: Java Security Essentials - Now for Compass
- Context-Aware Copilots for Next.js - Future what we can do


---
# ConFoo Conference — Day 2 Notes
*Concurrency, GitOps, Database DevOps, Observability & AI Governance*

---

## Session 1 — Java Concurrency Face-Off: Virtual Threads vs Reactive
**Speaker:** A N M Bazlur Rahman

With virtual threads (Project Loom) now stable in Java 21, the question every Java developer is asking is: does reactive programming still have a place? Bazlur answered this by building the same application three ways and comparing the results head-to-head across multiple dimensions.

### The Three Concurrency Models

| Model | How It Works | When It Was the Answer |
|-------|-------------|----------------------|
| **Traditional Thread Pools** | One OS thread per request. Simple mental model but threads are expensive (~1MB stack each). Blocks on I/O. | Low-concurrency apps where simplicity trumps scale. |
| **Reactive Streams** (Project Reactor / RxJava) | Non-blocking, event-driven pipelines. A small thread pool handles thousands of concurrent operations. Operators like `flatMap`, `zip`, and `onErrorResume` compose async flows. | High-concurrency I/O-bound workloads where thread cost was prohibitive. |
| **Virtual Threads** (Java 21+) | JVM-managed lightweight threads (~few KB each). Write blocking-style code; the JVM pins/unpins carrier threads automatically at blocking points. Millions can coexist. | The new default for most I/O-bound Java applications. |

### Head-to-Head Comparison

| Dimension | Thread Pools | Reactive | Virtual Threads |
|-----------|-------------|----------|-----------------|
| **Performance (I/O-bound)** | Limited by thread count | Excellent | Excellent |
| **Performance (CPU-bound)** | Good | No advantage | No advantage |
| **Memory use** | High (~1MB/thread) | Low | Very low (~few KB/thread) |
| **Debugging** | Easy — familiar stack traces | Hard — stack traces are fragmented across operator chains | Easy — stack traces look synchronous |
| **Backpressure** | Manual | Built-in (Reactive Streams spec) | Not built-in — must handle manually |
| **Error handling** | try/catch | Operator-based (`onErrorResume`, `doOnError`) | try/catch |
| **Learning curve** | Low | High | Low |
| **Code readability** | High | Low–Medium | High |

### The Decision Matrix

- **Choose Virtual Threads** if we have an I/O-bound workload, we value code simplicity, and our team doesn't need backpressure built into the model. This is now the right default for most REST APIs, database-heavy services, and microservices.
- **Choose Reactive** if we genuinely need built-in backpressure (e.g., streaming pipelines, event processing where a slow consumer must signal a fast producer), or if we're already invested in a Reactor/RxJava codebase.
- **Avoid Traditional Thread Pools** for new high-concurrency work — virtual threads obsolete the main reason we'd reach for a large thread pool.
- **Watch out for pinning:** Virtual threads can get pinned to their carrier OS thread when inside `synchronized` blocks or certain native calls. Replace `synchronized` with `ReentrantLock` to avoid this.

### Key Takeaways

- ✅ Virtual threads make blocking-style code scale — we get Reactive-level throughput with imperative readability.
- ✅ Reactive is not dead, but its use case has narrowed to scenarios requiring built-in backpressure.
- ✅ Debugging reactive code remains genuinely hard; virtual threads restore meaningful stack traces.
- ✅ Replace `synchronized` with `ReentrantLock` in virtual-thread-heavy code to prevent carrier thread pinning.
- ✅ CPU-bound work doesn't benefit from either Reactive or Virtual Threads — use `ForkJoinPool` or parallel streams instead.

---

## Session 2 — GitOps with FluxCD, Kubernetes and Kubeseal
**Speaker:** Benoît des Ligneris

Benoît presented GitOps as a production-proven discipline — not a theory — drawing on two years of running a real system that manages thousands of deployments and hundreds of pods. The talk covered both the philosophy and the practical implementation using FluxCD and Kubeseal.

### What is GitOps?

GitOps is an operational model where **Git is the single source of truth for cluster state**. Instead of running `kubectl apply` manually or triggering deployments from CI pipelines, a GitOps operator (FluxCD) continuously watches a Git repository and reconciles the cluster to match whatever is declared there.

Core principles:
- **Declarative:** The desired state is described in files (YAML manifests, Helm charts, Kustomize overlays), not imperative scripts.
- **Versioned:** Every change to cluster state goes through a Git commit — giving we full history, rollback, and auditability for free.
- **Automated reconciliation:** The operator detects drift (cluster state ≠ Git state) and self-heals automatically.
- **Pull-based:** The cluster pulls its configuration from Git, rather than CI pushing to the cluster. CI never needs cluster credentials.

### FluxCD Architecture

| Component | Role |
|-----------|------|
| **Source Controller** | Watches Git repos (and Helm repos, OCI registries) for changes. Fetches and caches manifests. |
| **Kustomize Controller** | Applies Kustomize overlays and plain YAML manifests from Source Controller output. |
| **Helm Controller** | Manages Helm releases declared as `HelmRelease` CRDs. Handles upgrades, rollbacks, and drift detection. |
| **Notification Controller** | Sends alerts (Slack, Teams, webhooks) on reconciliation events. Also receives webhooks to trigger immediate reconciliation. |
| **Image Automation Controller** | Watches container registries for new image tags and opens PRs (or commits) to update image references in Git — fully automating the update loop. |

### Kubeseal — Secrets in Git

The hardest GitOps problem: **we can't store raw Kubernetes secrets in Git** (base64 is not encryption). Kubeseal solves this with asymmetric encryption:

1. A `SealedSecrets` controller runs in the cluster and holds a private key.
2. We encrypt secrets locally with `kubeseal` using the cluster's public key.
3. The resulting `SealedSecret` CRD is safe to commit to Git — only the cluster can decrypt it.
4. On reconciliation, the controller decrypts and creates the real `Secret` in-cluster.

### Key Takeaways

- ✅ GitOps shifts the question from "what did someone deploy?" to "what does Git say should be deployed?" — audit trails become automatic.
- ✅ FluxCD's pull model means CI pipelines never need cluster credentials, reducing our attack surface significantly.
- ✅ Kubeseal is the missing piece that makes GitOps complete — secrets encrypted in Git, decrypted only by the cluster.
- ✅ Reconciliation loops enforce cluster hygiene — manual `kubectl` changes get reverted, preventing config drift.
- ✅ GitOps dramatically simplifies disaster recovery: point a new cluster at the repo and reconciliation does the rest.

---

## Session 3 — Database DevOps with Containers
**Speaker:** Rob Richardson

Rob tackled one of the most overlooked problems in CI/CD: databases. We can spin up and tear down application containers in seconds, but the database contains state that can't just be deleted. The talk showed how to bridge this gap — getting real production-like data to developers in containers, and flowing schema changes back to production safely.

### The Core Problem

Traditional CI/CD assumes ephemeral, stateless workloads. Databases are neither:

- We can't delete the database between deployments — it holds data.
- Developer databases are often stale, inconsistent, or don't reflect production schema.
- Schema migrations must be forward-compatible, tested, and reversible.
- Lookup/seed data must be kept in sync with schema changes.

### The Strategy: Start from Production, Flow Back to Production

TBD
---

## Session 4 — OpenTelemetry and Distributed Tracing
**Speaker:** Marian Marinov

Marian addressed one of the most painful realities of microservices: when a request fails or is slow, which of the 10 services it touched is responsible? OpenTelemetry (OTel) provides the vendor-neutral standard for capturing and correlating that signal across our entire system.

### The Observability Problem in Distributed Systems

A single user request in a microservices architecture may touch an API gateway, an auth service, a product service, a database, a cache, and a message queue — each on a different server, each logging independently. Traditional per-service logging gives we fragments. We need the full picture.

The three pillars of observability:

| Pillar | What It Is | OTel Support |
|--------|-----------|-------------|
| **Traces** | End-to-end record of a request's journey across services, with timing for each hop | First-class — the core of OTel |
| **Metrics** | Numeric measurements over time (latency, error rate, throughput) | Full support via OTel Metrics API |
| **Logs** | Discrete event records | Supported; OTel correlates logs to traces via trace/span IDs |

### OpenTelemetry Core Concepts

- **Trace:** The full journey of one request. Represented as a tree of spans.
- **Span:** A single unit of work within a trace (e.g., "query database", "call auth service"). Has a start time, duration, status, and attributes.
- **Trace Context:** A `traceparent` header propagated across service boundaries (HTTP, gRPC, message queues). This is what links spans from different services into one trace.
- **Collector:** The OTel Collector is a standalone agent/gateway that receives telemetry from our services, processes it (batching, sampling, enrichment), and exports to our backend (Jaeger, Zipkin, Tempo, Datadog, etc.).
- **Auto-instrumentation:** Language-specific agents (Java agent, Node.js SDK) that instrument common frameworks (Spring, Express, JDBC) without code changes.

### Practical Architecture

```
[Service A] --OTLP--> [OTel Collector] ---> [Jaeger / Tempo / Datadog]
[Service B] --OTLP-->        ↑
[Service C] --OTLP-->   (sample, batch,
                          enrich, route)
```

- Services emit telemetry via OTLP (OpenTelemetry Protocol) to the Collector.
- The Collector decouples our services from the backend — swap Jaeger for Tempo without touching application code.
- Head-based sampling (decide at trace start) vs. tail-based sampling (decide after seeing the full trace) — tail-based is more powerful but requires the Collector to buffer.

### Instrumentation in Practice

- **Java:** The OTel Java agent (`-javaagent:opentelemetry-javaagent.jar`) auto-instruments Spring Boot, JDBC, HTTP clients, and more with zero code changes.
- **Context propagation is the hard part:** Ensure `traceparent` headers are forwarded across all async boundaries — message queues and async threads require explicit propagation.
- **Sampling strategy matters at scale:** Tracing 100% of requests in production is expensive. Start with 10% head-based sampling; use tail-based for capturing all error traces.

### Key Takeaways

- ✅ OpenTelemetry is now the industry standard — don't invest in proprietary instrumentation that locks we to one vendor.
- ✅ The OTel Collector is the key architectural component — it decouples our services from whatever observability backend we choose.
- ✅ Auto-instrumentation (Java agent, etc.) gets we 80% of the value with zero code changes — start there.
- ✅ Context propagation across async boundaries (message queues, thread pools) is the most common gap in distributed tracing setups.
- ✅ Tail-based sampling lets we capture all error traces without the cost of tracing every request.

---

## Session 5 — Instrument and Govern AI Agents Before They Go Rogue
**Speaker:** Carl Lapierre

Carl's talk confronted the uncomfortable truth about production AI agents: they are unpredictable, opaque, and fail silently in ways that traditional monitoring doesn't catch. As teams move from experiments to production, observability isn't a nice-to-have — it's what keeps agents aligned with intent and users safe from unexpected behaviour.

### Why AI Agents Are Different

Traditional software fails loudly — exceptions, error codes, timeouts. AI agents fail quietly:

- An agent gives a confident but wrong answer.
- An agent calls a tool with subtly incorrect parameters.
- An agent enters a reasoning loop and produces responses that drift from the original intent.
- Token costs spike because a prompt grew unexpectedly.
- Latency degrades because retrieval quality dropped.

None of these manifest as a 500 error. Without instrumentation, we find out from users — or we don't find out at all.

### What to Track in LLM Observability

| Signal | Why It Matters |
|--------|---------------|
| **Prompt & completion content** | Audit trail for alignment, debugging hallucinations, and compliance. |
| **Token usage** (input/output/total) | Cost control and detecting prompt bloat. |
| **Latency** (TTFT, total) | User experience and SLA monitoring. TTFT = Time To First Token. |
| **Model & version** | Regressions when model versions change silently under we. |
| **Tool calls** (name, input, output) | Did the agent call the right tool? With sensible parameters? |
| **Retrieval quality** (RAG) | Were the retrieved chunks relevant? Low relevance = hallucination risk. |
| **Error rate & type** | Rate limits, context length exceeded, tool failures. |
| **User feedback** (thumbs up/down) | Ground truth signal for fine-tuning and prompt iteration. |

### GenAI Semantic Conventions

OpenTelemetry has published semantic conventions specifically for GenAI workloads — standardized attribute names for LLM spans:

- `gen_ai.system` — the LLM provider (e.g., `openai`, `anthropic`)
- `gen_ai.request.model` — model name
- `gen_ai.usage.input_tokens` / `gen_ai.usage.output_tokens` — token counts
- `gen_ai.operation.name` — `chat`, `embeddings`, etc.

Using these conventions means our traces are structured and queryable in any OTel-compatible backend, and dashboards can be shared across teams and tools.

### Observability Tools Compared

| Tool | Type | Strengths |
|------|------|-----------|
| **LangSmith** (LangChain) | Purpose-built LLM obs | Deep integration with LangChain/LangGraph. Prompt playground, dataset management, evaluations. |
| **Langfuse** | Open-source LLM obs | Self-hostable. Strong tracing, scoring, and dataset features. OTEL-compatible. |
| **Arize Phoenix** | Open-source LLM obs | Strong on RAG evaluation and embedding analysis. Good for retrieval quality debugging. |
| **Datadog LLM Observability** | Commercial APM + LLM | Best if we're already on Datadog — unified view of infra + LLM traces. |
| **OpenTelemetry + Tempo/Jaeger** | Generic distributed tracing | Vendor-neutral. Works with GenAI semantic conventions. Best for teams already invested in OTel. |

### Governance: Supervision Beyond Logging

Observability alone isn't governance. Carl distinguished between *watching* agents and *governing* them:

- **Guardrails:** Input/output validators that block or flag responses outside policy (toxic content, PII leakage, off-topic). Libraries like Guardrails AI or NeMo Guardrails implement these at the application layer.
- **Human-in-the-loop checkpoints:** For high-stakes actions (sending emails, modifying records, making purchases), require explicit human approval before execution — not just logging after the fact.
- **Rate limiting and circuit breakers:** Apply the same resilience patterns to LLM calls as to any external API. Budget limits per user/session prevent runaway cost from prompt injection or adversarial inputs.
- **Evals in CI:** Run automated evaluations against a golden dataset on every prompt change — treat prompt regressions the same way we treat code regressions.

### Key Takeaways

- ✅ AI agents fail silently — standard APM won't catch hallucinations, tool misuse, or reasoning drift. Dedicated LLM observability is required.
- ✅ Adopt OTel GenAI semantic conventions now — structured, standardized traces are queryable and shareable across teams.
- ✅ Track token usage from day one — cost surprises are the most common production shock for teams new to LLM workloads.
- ✅ Governance is not just logging — guardrails, human-in-the-loop checkpoints, and rate limiting are operational requirements for production agents.
- ✅ Run evals in CI against a golden dataset — prompt changes should go through the same regression testing as code changes.


# 🤩 Favorite
- Java Concurrency Face-Off: Virtual Threads vs Reactive - Now for Compass
- GitOps with FluxCD, Kubernetes and Kubeseal - Now for Compass
- Database DevOps with Containers - waiting for slides, will review photos that have been taken

---
# ConFoo Conference — Day 3 Notes
*MCP Automation, Azure Containers, OAuth2, LLM Internals, Context Engineering & Open Source*

---

## Session 1 — How I Automated My Life with MCP Servers
**Speaker:** Cedric Clyburn

Cedric opened with a relatable premise: modern developers aren't just writing code — they're also part-time Linux admins, part-time DevOps engineers, and part-time SREs. MCP (Model Context Protocol) lets we delegate the cognitive overhead of all those roles to an AI that has live access to our actual systems.

### What is MCP?

The Model Context Protocol is an open standard (originally from Anthropic) that defines how AI models connect to external tools and data sources in a structured, composable way. Instead of copy-pasting log output into a chat window, an MCP server gives the AI direct, typed access to the resource — and the AI can query, filter, and reason over it naturally.

The key insight: **MCP turns our infrastructure into a conversational interface.**

```
User: "What's consuming the most memory in our dev cluster right now?"
   ↓
AI + Prometheus MCP Server
   ↓
PromQL query executed against live Prometheus
   ↓
"The highest memory consumer is the api-gateway pod at 3.2 GB"
```

No PromQL knowledge required. No context-switching to Grafana.

### How MCP Works

| Component | Role |
|-----------|------|
| **MCP Server** | A lightweight process that exposes a set of typed tools (functions) and/or resources (data sources) over a standard protocol. One server per integration (Kubernetes, GitHub, Slack, etc.). |
| **MCP Client** | The AI host (Claude Desktop, VS Code Copilot, a custom agent) that discovers available servers and routes tool calls to them. |
| **Tools** | Functions the AI can invoke — `kubectl_get_pods`, `prometheus_query`, `github_create_pr`. Each has a typed schema the model uses to construct valid calls. |
| **Resources** | Read-only data the AI can access contextually — file contents, database rows, documentation pages. |
| **Transport** | stdio (local processes) or HTTP+SSE (remote servers). Local MCP servers are just programs on our machine. |

### Popular MCP Servers

| Server | What It Gives the AI |
|--------|---------------------|
| **Kubernetes / kubectl** | Query pods, deployments, logs, events, resource usage across namespaces. |
| **Prometheus** | Execute PromQL queries, browse metrics, investigate spikes and anomalies. |
| **GitHub** | Search repos, read files, create issues and PRs, review diffs. |
| **Filesystem** | Read and write local files within a sandboxed path. |
| **Postgres / SQLite** | Natural-language queries against our database schema. |
| **Slack** | Read channels, send messages, search history. |
| **Brave Search / Fetch** | Live web search and URL fetching for grounding responses in current info. |
| **Docker** | List containers, inspect logs, manage images. |

### Chaining MCP Servers

The real power emerges when the AI can use multiple servers in one reasoning chain:

> "Find all pods with memory over 1GB in the dev cluster, look up their deployment configs in GitHub, and create a Jira ticket summarising the findings."

This crosses Kubernetes MCP → GitHub MCP → Jira MCP in a single natural-language request. The AI orchestrates the tool calls; we just describe the outcome we want.

### Practical Tips from Cedric

- Start with one MCP server for a tool we use daily (kubectl, GitHub). The habit forms fast.
- Local stdio MCP servers run with our own credentials — no additional auth setup needed.
- Be explicit in prompts: "using only the Kubernetes MCP server, list pods in the staging namespace" helps the AI pick the right tool.
- MCP servers can be scoped — restrict filesystem access to specific directories to avoid accidents.

### Key Takeaways

- ✅ MCP turns our entire toolchain into a natural-language interface — we ask questions, the AI executes the right commands.
- ✅ Chain multiple MCP servers for cross-tool workflows that would otherwise require significant manual context-switching.
- ✅ Local stdio servers run with our credentials and require no infrastructure — just a process on our machine.
- ✅ The compounding effect: each MCP server we add multiplies the AI's ability to reason across our stack.
- ✅ MCP is vendor-neutral and growing fast — investing in it now means our automation stack is portable across AI tools.

---

## Session 2 — Effortless & Sustainable Apps with Azure Container Apps
**Speaker:** Chad Green

Chad made the case for Azure Container Apps (ACA) as the right abstraction for teams that want Kubernetes-grade capabilities without the operational overhead of managing Kubernetes itself. The talk covered the architecture, scaling model, and real-world cost optimisation patterns.

### The Positioning: Where ACA Fits

| Option | Best For | Overhead |
|--------|----------|----------|
| **Azure VMs** | Legacy workloads, full OS control | High |
| **Azure Kubernetes Service (AKS)** | Full Kubernetes control, complex orchestration needs | High |
| **Azure Container Apps** | Microservices, APIs, event-driven workers, most containerized workloads | Low |
| **Azure Container Instances** | One-off jobs, simple single containers | None |
| **Azure App Service** | Traditional web apps, PaaS feel | Low–Medium |


---

## Session 3 — OAuth2: Client and Server Implementation
**Speaker:** Martin Legris

Martin's workshop cut through the confusion around OAuth2 by covering both sides of the protocol — implementing a client that requests tokens and a server that issues and validates them. He also addressed the "almost standards" problem: implementations differ in ways that cause real integration pain.

### OAuth2 Core Mechanics

OAuth2 is an authorization framework, not an authentication protocol. It answers: "Can this application access this resource on behalf of this user?" — not "Who is this user?" (that's OpenID Connect, which layers on top).

**The four roles:**
- **Resource Owner** — the user who owns the data.
- **Client** — the application requesting access.
- **Authorization Server** — issues tokens after verifying consent (e.g., Auth0, Keycloak, Azure AD).
- **Resource Server** — the API that validates tokens and serves protected data.

### Grant Types (Flows)

| Flow | Use Case | How It Works |
|------|----------|-------------|
| **Authorization Code + PKCE** | Web apps, mobile apps (the modern standard) | User is redirected to auth server, approves, gets an auth code, client exchanges code for tokens. PKCE prevents code interception attacks. |
| **Client Credentials** | Machine-to-machine (no user involved) | Client sends its ID + secret directly to auth server, receives an access token. Used for service-to-service calls. |
| **Device Code** | CLIs, smart TVs (no browser) | Device shows a code; user visits a URL on another device to approve. |
| ~~Implicit~~ | *(Deprecated)* | Tokens returned directly in URL fragment. Replaced by Auth Code + PKCE. |
| ~~Resource Owner Password~~ | *(Deprecated)* | Client collects username/password directly. Never use for new implementations. |

### Token Types

| Token | Format | Purpose | Lifetime |
|-------|--------|---------|----------|
| **Access Token** | JWT or opaque | Grants access to a specific resource (scope). | Short — minutes to an hour. |
| **Refresh Token** | Opaque | Used to obtain new access tokens without re-authentication. | Long — hours to days. Store securely. |
| **ID Token** | JWT (OIDC only) | Contains identity claims about the authenticated user. | Short — use once for login. |

### The "Almost Standards" Problem

OAuth2 is a framework, not a single implementation. Real-world pain points Martin highlighted:

- **Token endpoint URLs** vary per provider — no single standard path.
- **Scope formats** differ: some providers use space-separated, others use comma-separated.
- **PKCE support** is now universal for public clients but wasn't always enforced — older servers may not require it.
- **Token introspection and revocation** (RFC 7662, RFC 7009) are optional extensions not all servers implement.
- **Solution:** Use a battle-tested library (Spring Security OAuth2, Passport.js, oidc-client-ts) rather than hand-rolling. Libraries handle the provider-specific edge cases.

### Database-Driven Implementation

A key practical insight from the workshop: hardcoding client IDs, secrets, and redirect URIs in config files is brittle. A database-driven approach stores OAuth2 client registrations as rows — enabling multi-tenant apps to support arbitrary identity providers without redeployment.

Schema sketch:
```
oauth2_clients (client_id, client_secret_hash, redirect_uris[], scopes[], grant_types[], provider_metadata_url)
```

The `provider_metadata_url` points to the provider's `.well-known/openid-configuration` endpoint, from which our server can dynamically discover all endpoint URLs — making onboarding new identity providers a data operation, not a code change.

### Key Takeaways

- ✅ Use Authorization Code + PKCE for all user-facing apps — it's the current secure standard. Implicit and ROPC flows are deprecated.
- ✅ Use Client Credentials for service-to-service calls — clean, no-user-context machine authentication.
- ✅ Never hand-roll OAuth2 — use a library. The "almost standards" variations will break our handcrafted implementation.
- ✅ Database-driven client registration makes our auth system multi-tenant and extensible without code changes.
- ✅ Access tokens are short-lived by design — refresh tokens are the sensitive credential; store them encrypted and never in localStorage.

---

## Session 4 — LLM from Scratch: No Slides, Just Code
**Speaker:** Alexis Fortin-Côté

Alexis did something rare: a live-coding session that built a working LLM from first principles — not a toy n-gram model, but an actual transformer with attention, trained end-to-end. The goal was demystification: if we understand what goes in and what comes out at each step, the "magic" becomes engineering.

### The Pipeline: What Actually Happens

```
Raw Text
   ↓  Tokenization
Token IDs (integers)
   ↓  Embedding Layer
Token Vectors (high-dimensional floats)
   ↓  Transformer Blocks (× N)
   │     ├─ Multi-Head Self-Attention
   │     ├─ Layer Normalization
   │     └─ Feed-Forward Network
   ↓
Contextual Representations
   ↓  Linear + Softmax (the "head")
Probability Distribution over Vocabulary
   ↓  Sampling (temperature, top-k, top-p)
Next Token
```

### Step 1: Tokenization

Text can't go directly into a neural network — it must become numbers first.

- **Character-level:** Each character is a token. Simple but produces very long sequences.
- **Byte Pair Encoding (BPE):** Start with characters; iteratively merge the most frequent adjacent pairs into new tokens. This is what GPT-2/3/4 use (via `tiktoken`). Produces compact sequences while handling novel words gracefully.
- **SentencePiece:** Google's approach used by LLaMA, T5. Language-agnostic, treats the input as a raw byte stream.
- Vocabulary size matters: GPT-4 uses ~100K tokens. Larger vocab = better compression but larger embedding tables.

### Step 2: Embeddings

Each token ID maps to a dense vector (e.g., 512 or 4096 floats). These are learned during training. The embedding layer is just a lookup table.

**Positional encoding** is added on top — since transformers have no inherent sense of order, positional information must be injected. Options: sinusoidal (original "Attention is All We Need"), learned, or RoPE (Rotary Position Embedding, used by LLaMA/modern models).

### Step 3: The Transformer Block

This is the core building block, repeated N times (GPT-3 has 96 layers):

**Multi-Head Self-Attention:**
- Every token looks at every other token in the sequence and computes how much to "attend" to it.
- Produces Query (Q), Key (K), and Value (V) matrices from the input.
- Attention score: `softmax(QKᵀ / √d_k) × V`
- Multiple heads let the model attend to different aspects simultaneously (syntax, semantics, coreference).
- **Causal masking:** For autoregressive (decoder-only) models, future tokens are masked so the model can't "cheat" by seeing ahead.

**Feed-Forward Network:**
- After attention, each token's representation passes through a two-layer MLP independently.
- This is where a lot of "factual knowledge" is believed to be stored.

**Layer Normalization** stabilizes training and is applied before (Pre-LN, modern) or after (Post-LN, original) each sub-layer.

### Step 4: Training

- **Loss function:** Cross-entropy loss. The model predicts the next token; loss measures how wrong it was.
- **Optimizer:** AdamW. Weight decay prevents overfitting.
- **Learning rate schedule:** Warmup then cosine decay. Critical for stability with large models.
- **Gradient clipping:** Prevents exploding gradients, especially early in training.
- At small scale (the demo), training a character-level model on Shakespeare converges in minutes on a laptop GPU.

### Step 5: Inference & Sampling

The model outputs a probability distribution over the full vocabulary. How we sample from it controls the "creativity" vs "accuracy" trade-off:

| Strategy | Effect |
|----------|--------|
| **Greedy (argmax)** | Always picks the highest-probability token. Deterministic but repetitive. |
| **Temperature** | Divides logits before softmax. Low temp (0.1) = conservative; high temp (1.5+) = creative/chaotic. |
| **Top-k sampling** | Sample from only the k highest-probability tokens. Prevents very low-probability tokens. |
| **Top-p (nucleus) sampling** | Sample from the smallest set of tokens whose cumulative probability exceeds p. More adaptive than top-k. |

### Key Takeaways

- ✅ An LLM is a next-token predictor trained at massive scale — the "intelligence" emerges from that simple objective applied to vast data.
- ✅ Transformers work because attention lets every token in a sequence influence every other — it scales where RNNs couldn't.
- ✅ Temperature, top-k, and top-p are the knobs that control generation quality — understanding them helps we tune AI features in our own applications.
- ✅ Tokenization is a first-class design decision: vocabulary size, tokenizer choice, and context window length all have downstream effects on model behaviour.
- ✅ The same architecture underlies GPT-4, LLaMA, Claude, and Gemini — differences are in scale, data, and fine-tuning, not fundamental architecture.

---

## Session 5 — The Art of Context Engineering
**Speaker:** Carl Lapierre

Carl's second talk of the conference (following his AI observability session on Day 2) zoomed in on the single most important variable in LLM application quality: context. His framing was precise — LLMs are pure functions. The same model, given different context, produces radically different outputs. Therefore, engineering context is engineering the application.

### The Core Insight: LLMs are Pure Functions

```
output = LLM(context)
```

There is no hidden state. No memory between calls (unless we add it). No mood. The model is fully deterministic given the same context and temperature. This means **every quality problem is a context problem** — the model is doing exactly what the context tells it to, even when the result is wrong.

### What "Context" Actually Contains

In a production LLM call, context is assembled from multiple sources:

| Source | What It Contributes |
|--------|-------------------|
| **System prompt** | Persona, task instructions, output format, constraints, and guardrails. |
| **Conversation history** | Prior turns — gives the model memory within a session. |
| **Retrieved documents** (RAG) | Relevant knowledge chunks fetched from a vector store or search index. |
| **Tool results** | Output from function/tool calls injected back into context. |
| **User input** | The current message or query. |
| **Few-shot examples** | Demonstrations of correct input/output pairs. |

### Context Rot: The Slow Poison

Context rot happens when accumulated context degrades model performance over a long session:

- Old, irrelevant conversation turns dilute the signal from recent instructions.
- Contradictory information from multiple retrieved chunks confuses the model.
- A growing system prompt with overlapping, inconsistent rules creates instruction conflict.
- Long contexts push important information past the model's effective attention range (even within the stated context window).

**Symptoms:** The model starts ignoring instructions it previously followed, repeats itself, or contradicts earlier responses.

### Context Engineering Techniques

**Writing Context:**
- Be explicit and unambiguous — the model can't infer intent from vague instructions.
- Use structured formats (XML tags, numbered lists, delimiters) to separate sections. Models parse structure reliably.
- State constraints positively ("always respond in English") and negatively ("never include code unless asked").
- Put the most important instructions at the beginning and end of the context — the middle of long contexts receives less attention.

**Selecting Context:**
- Don't retrieve everything — retrieve what's relevant. A RAG pipeline that returns 20 chunks when 3 are relevant is adding noise.
- Use a reranker (e.g., Cohere Rerank, cross-encoder) after initial retrieval to select the highest-quality chunks.
- Prefer recent information over old when both are available and the topic is time-sensitive.

**Compressing Context:**
- Summarise long conversation history rather than appending it indefinitely.
- Extract only the relevant facts from long documents rather than including the full text.
- Use structured extraction: have the model (or a smaller, cheaper model) distill a long document into a compact JSON before including it in the main context.

**Isolating Context (Splitting Problems):**
- Complex tasks with many constraints are better split into focused sub-tasks with their own clean contexts.
- A model asked to "research, plan, and write" simultaneously performs worse than three separate calls, each focused on one step.
- Map-reduce patterns: process chunks in parallel with small contexts, then synthesize results in a final call.

### When to Use Memory

| Type | Implementation | Use When |
|------|---------------|----------|
| **In-context memory** | Include relevant past turns directly | Short sessions; few key facts to recall. |
| **External memory** (vector store) | Retrieve relevant memories by similarity | Long-running agents; large fact bases. |
| **Structured memory** | Key-value store of extracted facts | User preferences, profile data, decisions made. |
| **Episodic memory** | Summaries of past sessions | Continuity across long-running projects. |

The key rule: **don't include memory by default — retrieve it when relevant.** Unconditional memory injection bloats context and introduces noise.

### Key Takeaways

- ✅ Context is the application — quality problems are context problems. Debug the context before blaming the model.
- ✅ Structure our context explicitly with delimiters and format. Models parse structure more reliably than prose instructions.
- ✅ Put critical instructions at the start and end of context — the middle of long contexts receives less model attention.
- ✅ Compress and summarise long history rather than appending indefinitely — context rot is real and degrades performance.
- ✅ Split complex tasks into focused sub-calls with clean, isolated contexts rather than piling everything into one prompt.

---

## Session 6 — My Journey into Open Source and How We Can Start Too
**Speaker:** Maxime David

Maxime closed the conference with a career reflection and a practical guide. He traced his arc from open-source consumer → contributor → author of a popular serverless benchmarking tool → core maintainer at Amazon, leading the AWS Lambda Runtimes team. The talk was both motivational and tactical.

### Key Takeaways

- ✅ Start with documentation or tests — they are always welcome and teach we the codebase without high-stakes code risk.
- ✅ Solve a problem we actually have — the best open source projects start as personal tools.
- ✅ README quality is our project's first impression — invest in it proportionally to the time we invest in the code.
- ✅ Respond to early issues and PRs fast — first contributors set the culture of our project.
- ✅ Open source is a career compounding machine: every contribution builds our network, portfolio, and skills simultaneously.

---
# 🤩 Favorite
- How I Automated My Life with MCP Servers - Future what we can do
- Context Engineering Techniques - Now for development

---


## ConFoo — Three-Day Summary For both BE and FE


**Measure everything.** Core Web Vitals, CVSS scores, JMH benchmarks, LLM token usage, distributed traces — every session that involved performance or reliability came back to instrumentation. If we leave ConFoo with one habit, make it: instrument before we optimize.

**Git is the control plane.** GitOps showed that Git-as-source-of-truth extends naturally from code to infrastructure to configuration. The same pull request discipline we apply to features should apply to Kubernetes manifests and database migrations.

**AI is infrastructure now.** Three days, five AI-related sessions. The message is consistent: AI is not a feature we add — it's a layer of our stack that requires the same engineering rigour as our database or our CI pipeline. Observe it, govern it, engineer its context deliberately.


## Some Ideas

### Context-Engineered System Prompt Template
*From Day 3 Session 5*

A reusable prompt structure using the techniques Carl described:
- XML-delimited sections (system, retrieved docs, history, user input)
- Few-shot examples block
- Compression logic for long conversation history

### DevOps Automation MCP Server Or Even for our Modelers
*From Day 3 Session 1*

Build MCP servers for our specific stack (Kubernetes, GitHub, our database) so an AI can:
- Query pods and correlate with GitHub deployment configs
- Triage failing CI jobs by reading logs
- Open PRs for dependency updates automatically

### AI-Powered CI Pipeline Triage Agent
*Combining Day 1 Session 3 (AI in CI/CD) + Day 2 Session 5 (AI governance) + Day 3 Session 1 (MCP)*

- Agent reads failing job logs
- Uses MCP tools (kubectl, GitHub) to investigate
- Has guardrails + human-in-the-loop for any proposed fixes
- Fully instrumented with LLM observability

### GitOps Bootstrap Template
*From Day 2 Session 2*

A repo template with:
- FluxCD + Kubeseal pre-wired
- Kustomize overlays for staging/production
- Notification Controller alerts to Slack
- Disaster recovery out of the box