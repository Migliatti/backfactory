# Backend / Integration / Automation — the dependency-ordered curriculum spine

**Question:** For a learner who knows basic JavaScript/frontend but has never built a backend, what is the
established, ordered curriculum for learning backend development, API integration, and automation?

**Researched:** 2026-09-02
**Method:** primary sources only — official curriculum data (freeCodeCamp curriculum JSON in the
`freeCodeCamp/freeCodeCamp` repo, roadmap.sh's own roadmap API), official docs (MDN, Node.js, Express,
Twelve-Factor), and first-party vendor docs (Stripe, GitHub, Zapier, Make, n8n, Node-RED).
No blog posts, no aggregator articles.

**Confidence note:** roadmap.sh and freeCodeCamp render their curricula client-side, so the ordering below was
taken from the machine-readable sources they publish — `https://roadmap.sh/api/v1-official-roadmap/<slug>` and
`curriculum/structure/**.json` in the freeCodeCamp repo — not from the rendered pages. n8n's and Make's
concept pages were partly unreachable; those claims are cited to the specific pages that did resolve and are
marked where thinner.

---

## Part 1 — The dependency-ordered spine

Each tier lists **what must already be true** before the tier makes sense. "Requires" is a hard prerequisite:
the concept is *not definable* without it, not merely easier.

### Tier 0 — Assumed entry state (not taught here)

- JavaScript basics, functions, objects, JSON literal syntax.
- Asynchrony: callbacks, promises, `async`/`await`.

  *Node.js docs assume JavaScript and devote a dedicated Getting Started article to "How much JavaScript do you
  need to know to use Node.js?" — it is a prerequisite question, not a lesson.*
  ([nodejs.org/en/learn/getting-started/introduction-to-nodejs](https://nodejs.org/en/learn/getting-started/introduction-to-nodejs))

  Async is a real prerequisite, not a nicety: Node.js is defined by non-blocking I/O — "instead of blocking the
  thread and wasting CPU cycles waiting, Node.js will resume the operations when the response comes back" (ibid.).

### Tier 1 — The client–server model and the HTTP message pair

**Requires:** nothing beyond Tier 0.

1. **Client–server protocol.** "HTTP is a client-server protocol, which means requests are initiated by the
   recipient, usually the Web browser."
   ([MDN, HTTP overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Overview))
2. **Request / response as discrete messages.** "Clients and servers communicate by exchanging individual
   messages (as opposed to a stream of data)." (ibid.)
3. **The four parts of a request:** URL → method → headers → body. MDN's *Client-Server overview* presents them
   in exactly that order, then encoding (URL parameters for GET, body for POST, cookies), then the response
   (status code → headers → body).
   ([MDN, Client-Server overview](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/First_steps/Client-Server_overview))
4. **Status codes** as the machine-readable outcome.
5. **Method semantics — safe and idempotent.** This is the *first* place idempotency legitimately appears:
   "An HTTP method is idempotent if the intended effect on the server of making a single request is the same as
   the effect of making several identical requests." Safe methods (GET, HEAD, OPTIONS, TRACE) plus PUT and
   DELETE are idempotent; POST and PATCH are not.
   ([MDN Glossary: Idempotent](https://developer.mozilla.org/en-US/docs/Glossary/Idempotent))

### Tier 2 — Statelessness, and the machinery that fakes state

**Requires:** Tier 1 (headers).

6. **HTTP is stateless.** "There is no link between two requests being successively carried out on the same
   connection." ([MDN, HTTP overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Overview))
7. **Cookies/sessions as the fix.** "While the core of HTTP itself is stateless, HTTP cookies allow the use of
   stateful sessions… allowing session creation on each HTTP request to share the same context." (ibid.)

   This ordering is load-bearing: sessions and auth (Tier 8) are *unintelligible* until the learner knows the
   protocol has no memory.

### Tier 3 — Calling an API you did not write

**Requires:** Tiers 1–2. Does **not** require owning a server.

8. Making an HTTP request from code; reading API docs; API keys/tokens as headers; parsing JSON; handling
   non-2xx responses; respecting rate limits.

   Placement evidence: freeCodeCamp teaches *consuming* JSON APIs in the `json-apis-and-ajax` block, which lives
   in the **Data Visualization** certification — and `curriculum/structure/curriculum.json` orders
   `data-visualization` **before** `back-end-development-and-apis`. Consuming precedes building, by construction.
   (`curriculum/structure/superblocks/data-visualization.json`, `curriculum/structure/curriculum.json`,
   [freeCodeCamp/freeCodeCamp](https://github.com/freeCodeCamp/freeCodeCamp))

### Tier 4 — Running your code outside the browser

**Requires:** Tier 0.

9. Runtime (Node), modules (CommonJS/ESM), npm and semantic versioning, environment variables, debugging.

   freeCodeCamp's *current* Back End cert opens here, before HTTP: `introduction-to-nodejs` →
   `nodejs-core-modules` → `node-package-manager` → a CLI-ish lab, and only then HTTP.
   (`curriculum/structure/superblocks/back-end-development-and-apis-v9.json`)

   The Odin Project does the same: *Introduction to the Back End* → *What is NodeJS?* → *Getting Started* →
   *Debugging Node* → **Project: Basic Informational Site** → *Environment Variables*.
   ([theodinproject.com — NodeJS course](https://www.theodinproject.com/paths/full-stack-javascript/courses/nodejs))

   roadmap.sh's Node.js roadmap likewise orders: Introduction → Modules → npm → Error Handling → Async
   Programming → Files → CLI apps → Building & Consuming APIs → Databases → Testing.
   ([roadmap.sh Node.js roadmap data](https://roadmap.sh/nodejs))

### Tier 5 — Your own server: listen, route, respond

**Requires:** Tiers 1 + 4.

10. Bind a port; map (method, path) → handler; return status + headers + body.

    freeCodeCamp v9: module `http-and-the-web-standards-model` (lectures on HTTP/DNS/TCP-IP and the
    request–response model, then **workshop: build a web server**) → `introduction-to-express` (routing).
    Classic fCC: `basic-node-and-express` runs *Meet the Node console → Start a Working Express Server → Serve an
    HTML File → Serve Static Assets → Serve JSON on a Specific Route → Use the .env File → …*
    (`curriculum/structure/blocks/basic-node-and-express.json`)

    Odin: *Introduction to Frameworks → Introduction to Express → Routes → Controllers → Views →
    Project: Mini Message Board → Deployment*.

### Tier 6 — The request pipeline (middleware) and input parsing

**Requires:** Tier 5.

11. **Middleware.** "An Express application is essentially a series of middleware function calls executed during
    the request-response cycle." A middleware function receives `req`, `res`, `next`; "if a middleware function
    does not end the request-response cycle, it must call `next()`… Otherwise, the request will be left hanging."
    Express presents the kinds in order: application-level → router-level → error-handling → built-in → third-party.
    ([Express, Using middleware](https://expressjs.com/en/guide/using-middleware.html))
12. **Reading input:** route params → query params → request body.
    fCC classic teaches exactly this order: *Implement a Root-Level Request Logger Middleware → Chain Middleware
    → Get Route Parameter Input → Get Query Parameter Input → Use body-parser to Parse POST Requests → Get Data
    from POST Requests.* (`curriculum/structure/blocks/basic-node-and-express.json`)

### Tier 7 — Persistence

**Requires:** Tier 5 (something to persist *for*).

13. A datastore, a schema/model, CRUD, and queries.

    fCC classic: `mongodb-and-mongoose` — *Install and Set Up Mongoose → Create a Model → Create and Save a
    Record → … → Chain Search Query Helpers.* MDN's Express tutorial introduces the database only at **Part 3**,
    after the skeleton site exists.
    ([MDN Express tutorial series](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/Express_Nodejs))
    Odin instead teaches *Installing PostgreSQL → Using PostgreSQL → Project: Inventory Application*, and later a
    dedicated *ORMs → Prisma ORM* section.

### Tier 8 — Identity: authentication and authorization

**Requires:** Tiers 2 (cookies/sessions) + 7 (a user record) + hashing.

14. Password hashing, sessions or tokens (JWT), OAuth as a later variant.

    roadmap.sh Backend Beginner puts Authentication (JWT) at step 7, right after APIs.
    ([roadmap.sh backend-beginner](https://roadmap.sh/backend-beginner))
    Odin: *Authentication Basics → Project: Members Only*, after Express + PostgreSQL.
    fCC v9 puts `security-and-privacy` then `authentication` near the **end** of the cert.

### Tier 9 — Failure as a first-class subject

**Requires:** Tiers 5–6.

15. Error handling as a pipeline concern (Express error middleware has the four-argument signature
    `(err, req, res, next)` — [Express, Using middleware](https://expressjs.com/en/guide/using-middleware.html));
    status codes as a contract; timeouts; structured error bodies (roadmap.sh API-design pairs *Error Handling*
    with *RFC 7807 – Problem Details*).
    fCC v9 has a dedicated `error-handling-in-express` module immediately after `rest-api-and-web-services`.

### Tier 10 — Retries, then key-based idempotency

**Requires:** Tier 9. **This is the ordering the ticket asked about, and the answer is: yes, retry comes first.**

16. **Retry** — the client repeats a request whose outcome it does not know.
17. **Idempotency keys** — only meaningful once retries exist. Stripe frames it purely as retry-safety: the API
    "supports idempotency for safely retrying requests without accidentally performing the same operation twice…
    if a connection error occurs, you can safely repeat the request without risk of creating a second object."
    Mechanism: the server saves the status code and body of the first request for a given key and returns the
    same result for later requests with that key; keys are client-generated (v4 UUIDs suggested), pruned after
    ~24h; **"All POST requests accept idempotency keys. Don't send idempotency keys in GET and DELETE requests
    because it has no effect. These requests are idempotent by definition."**
    ([Stripe, Idempotent requests](https://docs.stripe.com/api/idempotent_requests))

    **Nuance worth teaching explicitly:** there are *two* idempotencies. Method-level idempotency (Tier 1) is a
    definitional property of GET/PUT/DELETE and needs no retry machinery. Key-level idempotency (Tier 10) is a
    server-side dedup mechanism that exists *because* clients retry POSTs. Curricula that teach only one of the
    two leave a hole. MDN teaches only the first; Stripe only the second; roadmap.sh's API-design roadmap lists
    "Idempotency" once, inside *Building JSON / RESTful APIs*, after Versioning, CRUD, Pagination and Rate Limiting.
    ([roadmap.sh API design roadmap](https://roadmap.sh/api-design))

### Tier 11 — Webhooks (inbound events)

**Requires:** Tier 5 (a routable POST endpoint), Tier 6 (raw/JSON body parsing), Tier 9 (return fast, handle
failure), Tier 10 (duplicates), plus HMAC/hashing for verification.

18. **Definition.** GitHub: "Webhooks let you subscribe to events happening in a software system and
    automatically receive a delivery of data to your server whenever those events occur" — explicitly contrasted
    with "polling an API (calling an API intermittently) to see if data is available"; webhooks "require less
    effort and less resources than polling an API" and "allow near real-time updates."
    ([GitHub, About webhooks](https://docs.github.com/en/webhooks/about-webhooks))
19. **What a handler must do**, per Stripe: accept **POST** with a JSON payload; verify the request came from
    the sender using the raw body, the `Stripe-Signature` header and the endpoint's signing secret (HMAC-SHA256);
    and "return a successful status code (2xx) quickly, before any complex logic that could cause a timeout."
    Public endpoints must be HTTPS.
    ([Stripe, webhooks](https://docs.stripe.com/webhooks))
20. **Three delivery realities that must be taught with webhooks, not after:**
    - **Retries.** Stripe retries delivery "for up to three days with exponential backoff" in live mode.
    - **Duplicates.** "Webhook endpoints might occasionally receive the same event more than once… log the event
      IDs you have processed, and don't process already-logged events."
    - **No ordering guarantee.** "Stripe doesn't guarantee delivery of events in the order in which they're
      generated… Don't use `created` to determine event order or whether you've already processed an event."
    - **Process asynchronously.** "Configure your handler to process incoming events with an asynchronous queue…
      You might encounter scalability issues if you choose to process events synchronously."
    (all: [Stripe, webhooks](https://docs.stripe.com/webhooks))
    - CSRF exemption for webhook routes is called out as a framework gotcha (ibid.).

### Tier 12 — Polling vs. webhooks as a design choice

**Requires:** Tier 3 (polling a remote API) + Tier 11 (receiving pushes).

21. The trade-off is the single clearest shared concept between backend and automation curricula.
    Zapier: "With polling triggers, Zapier periodically asks your app for data"; Zapier deduplicates by
    "comparing each piece of data's unique ID to IDs the Zap has received before"; polling interval is plan-
    dependent (15 min free → 1–2 min on paid). "With instant triggers, apps automatically send new data as each
    trigger event occurs, using webhooks."
    ([Zapier, How Zap triggers work](https://help.zapier.com/hc/en-us/articles/8496244568589-How-Zap-triggers-work))
    Make says the same in one sentence: a polling trigger "checks for new data in a service account since the
    last run, based on the scenario's schedule," whereas with an instant trigger "the service notifies when new
    data arrives, starting the scenario immediately — in essence, an instant trigger is a webhook."
    ([Make, Types of modules](https://help.make.com/types-of-modules))
    roadmap.sh places "Webhooks vs Polling" inside *API Integration Patterns*, near the end of the API-design
    roadmap. ([roadmap.sh API design roadmap](https://roadmap.sh/api-design))

    Note the hidden prerequisite: **polling requires a cursor/watermark and deduplication by ID.** Zapier's and
    Make's definitions both encode it ("since the last run", "unique ID"). This is the automation-side twin of
    Tier 10 idempotency.

### Tier 13 — Automation composition

**Requires:** Tiers 3, 11, 12.

22. **Trigger → action.** Zapier: a trigger is "the event that starts your Zap"; an action is "what the Zap does
    after it triggers."
    ([Zapier, Getting started](https://help.zapier.com/hc/en-us/articles/8496309697421-Getting-started-with-Zapier))
    n8n: "A trigger node is a special node responsible for executing the workflow in response to certain
    conditions. All production workflows need at least one trigger."
    ([n8n key concepts](https://docs.n8n.io/key-concept-glossary/))
23. **Workflow / flow as the unit.** n8n: "An n8n workflow is a collection of nodes that automate a process…
    Workflows begin execution when a trigger condition occurs and execute sequentially." Nodes "define when the
    workflow should run, allow you to fetch, send, and process data, can define flow control logic, and connect
    with external services." (ibid.)
    Node-RED's concept order is: **Node → Configuration node → Flow → Context → Message → Subflow → Wire →
    Palette → Workspace → Sidebar**, where a message is a JS object with a `payload` and wires determine how
    messages traverse the flow.
    ([Node-RED, Concepts](https://nodered.org/docs/user-guide/concepts))
24. **Data shape between steps.** Make: a **bundle** is "a unit of data, like an email, contact, or file"; an
    **operation** is "a single module run to process data or check for new data"; a **router** splits a scenario
    into multiple routes.
    ([Make, Operations](https://help.make.com/operations), [Make, Router](https://help.make.com/router),
    [Make, Types of modules](https://help.make.com/types-of-modules))
25. **Credentials as a separate object from logic** (n8n credentials; Make/Zapier connections). This is the
    concept that makes "wiring services" different from "writing code."
26. **Branching, filtering, iterating, aggregating** — Make's router/flow-control; Node-RED's wires and subflows.
27. **Error workflows / failure routes** — Make surfaces "Error handling" as one of four top-level help sections.
    ([help.make.com](https://help.make.com/))

### Tier 14 — Operating the thing

**Requires:** Tier 5 at minimum; genuinely lands after Tier 11.

28. Config in the environment, deployment, logs. The Twelve-Factor App, in its own order: **I Codebase,
    II Dependencies, III Config ("store config in the environment"), IV Backing services ("treat backing
    services as attached resources"), V Build/release/run, VI Processes ("execute the app as one or more
    stateless processes"), VII Port binding, VIII Concurrency, IX Disposability, X Dev/prod parity,
    XI Logs ("treat logs as event streams"), XII Admin processes.**
    ([12factor.net](https://12factor.net/))
    Factors III, IV, VI and XI are the four that a beginner backend/automation learner actually uses; the rest
    are deploy-platform concerns.
    roadmap.sh files *Twelve Factor Apps* under **Architectural Patterns**, i.e. step 17 of 20.
    ([roadmap.sh backend](https://roadmap.sh/backend))

### Tier 15 — Scale and API-surface design

**Requires:** everything above; every source puts these last.

29. Caching (roadmap.sh backend #8), pagination, rate limiting/throttling, versioning, observability/monitoring
    (roadmap.sh backend #20), message brokers (#13), search engines (#14), real-time data (#18), CI/CD (#16).
    ([roadmap.sh backend](https://roadmap.sh/backend))

---

## Part 2 — Where each source actually places things

### roadmap.sh — Backend Developer (full), top-level, in order

Introduction (How does the internet work? → What is HTTP? → What is a Domain Name? → What is hosting? → DNS →
Browsers → Frontend Basics: HTML/CSS/JS) → Pick a Backend Language → Version Control Systems → Repo Hosting
Services → **Relational Databases** → Learn about APIs → Authentication → Caching → Learn about Web Servers →
More about Databases → NoSQL Databases → Scaling Databases → Message Brokers → Search Engines → Testing →
CI/CD → Architectural Patterns → Real-Time Data → Building For Scale → Observability.
([roadmap.sh/backend](https://roadmap.sh/backend), via its official roadmap data)

### roadmap.sh — Backend Beginner (the same author's "what's actually core")

Pick a Language (JS/Go/Python) → Version Control (Git, GitHub) → Repo hosting → Relational Databases
(PostgreSQL) → Learn about APIs (REST, JSON APIs) → Authentication (JWT) → Caching (Redis) → More about
Databases (ORMs, ACID, Transactions, N+1) → Testing (unit, integration, functional).
([roadmap.sh/backend-beginner](https://roadmap.sh/backend-beginner))

Ten steps. Everything else on the full roadmap — web servers, NoSQL, sharding, message brokers, search engines,
CI/CD, architectural patterns, real-time, scale, observability — is dropped. That deletion **is** the core/
optional boundary, stated by the same publisher.

### roadmap.sh — API Design, in order

Learn the Basics (What are APIs → HTTP → HTTP versions → methods → status codes → headers → URL/query/path
params → cookies → content negotiation → CORS → TCP/IP → DNS → HTTP caching) → Different API Styles (REST, JSON,
SOAP, GraphQL, gRPC) → Building JSON/RESTful APIs (REST principles → URI design → **versioning** → CRUD →
**pagination** → **rate limiting** → **idempotency** → HATEOAS → resource modeling → naming → filtering/sorting/
search → **error handling** → RFC 7807) → Real-time APIs (WebSockets, SSE, streaming) → Authentication →
Authorization → API keys → Documentation → Security → Performance (incl. **error handling/retries**) →
**API Integration Patterns (sync vs async, event-driven, gateways, microservices, webhooks vs polling, batch,
queues, RabbitMQ, Kafka, BFF)** → Testing → Standards/compliance → Lifecycle → Observability.
([roadmap.sh/api-design](https://roadmap.sh/api-design))

### freeCodeCamp — Back End Development and APIs

*Classic cert*, block order from `curriculum/structure/superblocks/back-end-development-and-apis.json`:
`managing-packages-with-npm` → `basic-node-and-express` → `mongodb-and-mongoose` → projects
(Timestamp Microservice → Request Header Parser → URL Shortener → Exercise Tracker → File Metadata).

*Current (v9) cert*, module order from `back-end-development-and-apis-v9.json`:
introduction-to-nodejs → nodejs-core-modules → node-package-manager → *lab: prime number checker module* →
**http-and-the-web-standards-model** (HTTP/DNS/TCP-IP; request–response model; web standards model; workshop:
build a web server) → introduction-to-express → *lab: personal profile app* → express-middleware →
rest-api-and-web-services → *lab: timestamp microservice* → error-handling-in-express → websockets →
*lab: chat app* → security-and-privacy → authentication → *lab: family movie watchlist API* → review → exam.
([freeCodeCamp/freeCodeCamp](https://github.com/freeCodeCamp/freeCodeCamp))

Notable: **v9 contains no database module at all** — databases moved to a separate Relational Databases
certification. The modern fCC backend spine is Node → HTTP → Express → middleware → REST → errors → realtime →
security → auth.

### The Odin Project — Full Stack JavaScript path

Path order: Intermediate HTML and CSS → JavaScript → Advanced HTML and CSS → React → **Databases** → **NodeJS** →
Getting Hired.

NodeJS course: *Introduction to the Back End → What is NodeJS? → Getting Started → Debugging Node →
Project: Basic Informational Site → Environment Variables* | *Introduction to Frameworks → Introduction to
Express → Routes → Controllers → Views → Project: Mini Message Board → Deployment → Forms and Data Handling →
Installing PostgreSQL → Using PostgreSQL → Project: Inventory Application* | *Authentication Basics →
Project: Members Only* | *Prisma ORM → Project: File Uploader* | *API Basics → API Security →
Project: Blog API* | *Testing Routes and Controllers → Testing Database Operations* | full-stack projects →
Odin-Book.
([theodinproject.com](https://www.theodinproject.com/paths/full-stack-javascript/courses/nodejs))

### MDN

*Server-side first steps*: Introduction to the server-side → Client-Server overview → Server-side web frameworks
→ Website security. Prerequisites are stated as "what is a web server / what software do I need / how do you
upload files" — **no prior programming knowledge required**.
([MDN, Server-side first steps](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/First_steps))

*Express tutorial series*: Express/Node introduction → Setting up a Node (Express) development environment →
Local Library overview → Part 2 skeleton website → **Part 3 Using a Database (Mongoose)** → Part 4 Routes and
controllers → Part 5 Displaying library data → Part 6 Working with forms → Part 7 Deploying to production.
([MDN, Express/Node](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/Express_Nodejs))

### Node.js official Getting Started, in order

Introduction to Node.js → How much JavaScript do you need → Differences between Node.js and the Browser →
The V8 engine → npm → ES2015+ → Debugging → Fetching data with Node.js → WebSocket client → dev vs production →
Profiling → WebAssembly → Security best practices → Userland migrations.
([nodejs.org](https://nodejs.org/en/learn/getting-started/introduction-to-nodejs))

### Automation platforms (no server required at any point)

- **Zapier**: Zap → trigger → action; then polling vs instant triggers and deduplication.
- **Make**: scenario → module (trigger / action / search / universal) → bundle → operation → router; triggers
  split into polling vs instant.
- **n8n**: workflow → node → trigger node → credentials → expressions → data pinning.
- **Node-RED**: node → config node → flow → context → message → subflow → wire. The *first flow* tutorial is
  Inject node → Debug node → wire them → Deploy → observe → Function node. **No HTTP appears in the first
  tutorial at all.**
  ([Node-RED first flow](https://nodered.org/docs/tutorials/first-flow))

---

## Part 3 — Core vs. optional/advanced, as the sources themselves mark it

**Core in every source consulted:**
HTTP request/response, methods, status codes, headers, body · statelessness and cookies/sessions · a runtime +
package manager · routing · middleware/request pipeline · reading params/query/body · JSON responses ·
persistence + CRUD · authentication · error handling · environment variables/config.

**Explicitly optional or deferred by at least one authoritative source:**

| Concept | Marked how |
|---|---|
| NoSQL databases | roadmap.sh full backend #11, under "More about Databases"; **absent** from Backend Beginner |
| Web servers (nginx/Apache/Caddy) | roadmap.sh full backend #9; absent from Backend Beginner |
| Message brokers (Kafka, RabbitMQ) | roadmap.sh #13; absent from Backend Beginner; roadmap.sh API-design puts queues in the late *Integration Patterns* section |
| Search engines (Elasticsearch, Solr) | roadmap.sh #14; absent from Backend Beginner |
| CI/CD, containers, Kubernetes | roadmap.sh #16 and later; absent from Backend Beginner |
| Architectural patterns / microservices / Twelve-Factor | roadmap.sh #17; absent from Backend Beginner |
| Real-time data (WebSockets, SSE, polling) | roadmap.sh #18 — but fCC v9 promotes WebSockets into the core cert |
| Observability / monitoring / telemetry | roadmap.sh #20, last |
| GraphQL, gRPC, SOAP | roadmap.sh API-design lists them as *alternative styles* after REST; Backend Beginner keeps only REST + JSON APIs |
| HATEOAS | roadmap.sh API-design lists it inside REST but after CRUD/pagination/rate limiting/idempotency |
| Authorization models beyond RBAC (ABAC, PBAC, MAC, ReBAC) | roadmap.sh API-design, grouped after authentication |
| Testing | roadmap.sh Backend Beginner's **last** step; Odin's second-to-last; **absent from the fCC backend cert entirely** (separate QA cert) |
| Databases | **absent from freeCodeCamp's current v9 backend cert** — moved to its own certification |
| Language choice beyond one | roadmap.sh marks languages "Personal Recommendation" / "Alternative Option"; explicitly a preference, not a sequence |

---

## Part 4 — Commonly taught early, but low-value or better deferred

These are inferences *from* the sources' own placements, not claims any source makes as advice. Flagged as such.

1. **Choosing between many languages/frameworks/databases.** roadmap.sh labels every language node "Personal
   Recommendation" or "Alternative Option" — the graph is deliberately a menu, not a sequence. Time spent
   comparing is not time spent on the spine.
2. **Template engines and server-rendered views.** Odin teaches *Views* and MDN spends Parts 5–6 on displaying
   data and Pug forms. For a learner whose goal is APIs/integration/automation, HTML rendering is a parallel
   branch, not a prerequisite for anything downstream in this spine. roadmap.sh's Node roadmap files template
   engines under *Development Tools*, not under APIs.
3. **NoSQL/Mongoose as the first database.** Classic fCC and MDN both teach Mongoose first; roadmap.sh (both
   versions) and Odin teach relational first, and roadmap.sh places NoSQL nine steps later. The newer curricula
   moved away from NoSQL-first. Treat "Mongoose first" as a legacy artifact.
4. **Auth as a from-scratch build, early.** fCC v9 moved authentication to the **end** of the cert, after error
   handling, WebSockets and a security module. Hashing, sessions, tokens and OAuth each pull in prerequisites
   (Tier 2 + Tier 7 + crypto) that a beginner does not have at Tier 5.
5. **Deployment/hosting mechanics before there is anything worth deploying.** roadmap.sh keeps "What is hosting"
   in the Introduction as *awareness*, but the actual deploy work sits at MDN Express Part 7 and Odin's
   post-Express *Deployment* lesson — after a working app exists.
6. **Twelve-Factor as a checklist.** roadmap.sh files it under Architectural Patterns (#17 of 20). Only factors
   III (config in environment), IV (backing services as attached resources), VI (stateless processes) and
   XI (logs as event streams) have any purchase on a beginner's actual work.
7. **HATEOAS, content negotiation, RFC 7807, API versioning strategies.** All present in roadmap.sh API-design,
   all placed after the parts a learner will actually exercise. Versioning in particular presupposes having
   shipped a v1 to somebody.
8. **Message brokers and microservices.** Deferred by every source; in an integration context the *concept* that
   matters early is "process the event asynchronously so you can return 2xx fast" (Stripe), not the broker
   product.

---

## Part 5 — Is integration taught before or after "build your own server"?

**Short answer: the formal backend curricula teach it strictly after; the industry's own integration tooling
teaches it entirely without. Both positions are held by high-trust sources, and they are answering different
questions.**

### Evidence for AFTER (build the server first)

- Odin puts *API Basics* and *API Security* **after** Express, PostgreSQL, authentication and ORMs — the sixth
  section of the NodeJS course.
- roadmap.sh's API-design roadmap places *API Integration Patterns* (webhooks vs polling, gateways, queues,
  event-driven) near the very end, after building REST APIs, auth, security and performance.
- roadmap.sh's Backend Beginner roadmap contains **no integration concept at all** — no webhooks, no schedules,
  no third-party services.
- MDN's server-side path has no integration module.
- fCC's backend cert (both classic and v9) builds servers; it never consumes a third-party API.

**The argument:** a webhook is an inbound POST to *your* endpoint that you must parse, verify, answer with 2xx
and deduplicate (Stripe). If the learner cannot route a POST, read a raw body, or return a status code, the
webhook lesson is a black box. Integration is *downstream* of the server, not parallel to it.

### Evidence for BEFORE (integrate first)

- freeCodeCamp's own global ordering does exactly this: `json-apis-and-ajax` sits in the Data Visualization
  cert, and `curriculum.json` lists `data-visualization` **before** `back-end-development-and-apis`. Learners
  consume APIs long before they build one.
- Four vendor platforms — Zapier, Make, n8n, Node-RED — are entire products whose beginner concept sets contain
  **no server concepts whatsoever**. Zapier's getting-started reduces to Zap/trigger/action. Node-RED's first
  flow is inject → debug → wire → deploy, with no HTTP at all. n8n's key concepts are workflow/node/trigger
  node/credential/expression.
- Those platforms nevertheless teach, on day one, several concepts the backend curricula defer for months:
  polling vs. instant triggers, deduplication by unique ID, "since the last run" watermarks, credentials as
  first-class objects, and error routes.

**The argument:** integration-first gives a working, useful artifact before any server exists, and it front-loads
the concepts (event → handler, at-least-once delivery, dedup, scheduling) that make the later backend material
*motivated* rather than arbitrary. It also matches how most working developers first meet these ideas.

### Reconciliation

The two camps split on which meaning of "integration" they mean:

- **Outbound integration** (calling someone else's API) genuinely requires only Tier 1–3. It can and arguably
  should come first. fCC does this.
- **Inbound integration** (receiving webhooks) genuinely requires Tiers 5, 6, 9, 10 plus HMAC. It cannot
  honestly come first *unless* a platform hosts the endpoint for you — which is exactly what Zapier, Make, n8n
  and Node-RED do. They are not skipping the prerequisite; they are supplying it.

---

## Part 6 — Where the sources disagree

1. **Does HTTP come first, or does "run code on a server" come first?**
   roadmap.sh puts *What is HTTP?* in the opening Introduction block, before choosing a language.
   freeCodeCamp v9 puts the entire `http-and-the-web-standards-model` module **fifth**, after Node, core
   modules, npm and a lab. MDN splits the difference (Client-Server overview is step 2 of four conceptual
   articles, but the Express tutorial's practical HTTP work comes after environment setup).

2. **Relational or document database first — or none at all?**
   roadmap.sh: relational at #5, NoSQL at #11. Odin: PostgreSQL, with Prisma later. Classic fCC and MDN: MongoDB/
   Mongoose only. fCC v9: **no database in the backend cert at all.** Four different answers.

3. **Database before or after the server?**
   The Odin Project's *path* puts the Databases course **before** NodeJS. roadmap.sh puts Relational Databases
   before "Learn about APIs". MDN and fCC classic both build the server first and add the database afterwards
   (MDN Part 3; fCC's `mongodb-and-mongoose` after `basic-node-and-express`).

4. **Where does authentication go?**
   roadmap.sh Backend Beginner: step 7 of 10, before caching and testing. Odin: after Express + PostgreSQL, before
   APIs. fCC v9: near the end, after error handling, WebSockets and security. fCC classic: **not in the cert at
   all** (it lives in the non-certification `advanced-node-and-express` block).

5. **Is testing core?**
   roadmap.sh Backend Beginner ends on Testing (so: core, last). Odin has *Testing Express* after APIs. fCC
   excludes testing from the backend cert entirely — it is a separate Quality Assurance certification.

6. **Is idempotency an HTTP property or a retry mechanism?**
   MDN defines it as a property of methods, with no retry machinery required
   ([Glossary: Idempotent](https://developer.mozilla.org/en-US/docs/Glossary/Idempotent)). Stripe defines it as
   a client-supplied key that makes retried POSTs safe
   ([Idempotent requests](https://docs.stripe.com/api/idempotent_requests)). roadmap.sh lists it once, mid-REST.
   These are not the same concept and the sources never reconcile them.

7. **Are webhooks core, advanced, or day-one?**
   Absent from fCC (all versions), Odin, MDN and roadmap.sh's Backend Beginner. Late-stage on roadmap.sh's
   API-design roadmap. **Day-one, foundational vocabulary** for Zapier ("instant triggers… using webhooks"),
   Make ("an instant trigger is a webhook"), GitHub and Stripe. This is the sharpest disagreement found, and it
   is precisely the gap a game about automation + integration sits in.

8. **Is frontend a prerequisite for backend?**
   roadmap.sh's backend roadmap embeds *Frontend Basics* (HTML/CSS/JS) inside its Introduction. MDN's server-side
   first steps explicitly state **no prior programming knowledge is required**, while positioning the module as
   an extension after client-side learning. Mild but real.

9. **Framework-first or protocol-first?**
   MDN dedicates an entire first-steps article to *Server-side web frameworks* before any code, and Odin has
   *Introduction to Frameworks* before *Introduction to Express*. fCC v9 has learners **build a raw web server**
   in the HTTP module before introducing Express. roadmap.sh's Node roadmap lists the `http` module alongside
   axios/fetch under "Making API Calls", not as a server-building step.

---

## Part 7 — What this means for a game curriculum

`backfactory` teaches automation, integration **and** backend. The research says those three are not three
parallel tracks; they are one spine viewed from three distances, and the game can exploit the disagreement in
Part 6 §7 as its actual design opportunity.

**1. The spine gives a natural level progression.** Tiers 1 → 15 above are already a difficulty curve with
enforced prerequisites. A tech-tree/factory metaphor maps onto it directly: each tier unlocks the mechanics the
next tier needs, and a player literally cannot build a webhook receiver before they have a routable POST
endpoint and a status-code response — the same constraint the real curriculum has.

**2. Start on the integration side of the split, not the server side.** freeCodeCamp's own global ordering
(consume APIs before building them) plus four vendor platforms that teach automation with zero server concepts
give solid cover for opening the game at Tier 3 / Tier 13: call a service, get JSON, feed it to another service.
That produces a working artifact in minutes and motivates every later tier. Reserve "build your own server"
(Tier 5) for the moment the player needs an endpoint *someone else* can call — which is exactly when webhooks
enter.

**3. Make the webhook the mid-game pivot.** It is the single concept that requires the most of the spine
(routing + POST + body parsing + status codes + signature verification + retries + duplicates + no ordering) and
the one every formal curriculum omits. Introducing it as "the other machine can now push to *you*" converts a
list of prerequisites into a felt need.

**4. Teach the two idempotencies separately and in order.** Method-level (GET/PUT/DELETE are idempotent by
definition — MDN) belongs in the HTTP tier. Key-level (a client-generated key that dedupes retried POSTs —
Stripe) belongs only after retries and duplicate deliveries have visibly hurt the player. A duplicate order,
a double charge, a doubled counter: the mechanic teaches itself.

**5. Failure is the curriculum, not a difficulty setting.** Stripe's webhook page is, read as a syllabus, a list
of failure modes: retries with exponential backoff for three days, duplicate deliveries, unordered events,
timeouts from slow handlers, signature mismatches, TLS errors, 3xx treated as failure. Zapier's and Make's
polling docs add the automation twin: watermarks and dedup by unique ID. A factory game where pipes occasionally
deliver twice, out of order, or not at all is *more* faithful to the domain than one where they do not.

**6. Where to be deliberately unfaithful.** The deferred list in Part 4 is a list of things to keep out of the
early game: language/framework choice, template engines, message-broker products, microservices, Kubernetes,
HATEOAS, versioning strategies, Twelve-Factor as a checklist. Keep four factors only — config in the environment,
backing services as attached resources, stateless processes, logs as event streams — because those four are
directly representable as game objects (a credentials store, a pluggable service port, a machine with no memory,
an output belt of events).

**7. Two concepts the formal curricula under-teach that the game should treat as first-class**, because the
vendor docs make them day-one: **credentials/connections as objects distinct from logic** (n8n, Make, Zapier all
model this explicitly), and **the polling-vs-push trade-off with its cost** (Zapier's interval-by-plan table is
a resource-cost mechanic sitting there fully formed).

---

## Sources

- MDN, *An overview of HTTP* — https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Overview
- MDN, *Glossary: Idempotent* — https://developer.mozilla.org/en-US/docs/Glossary/Idempotent
- MDN, *Server-side website programming first steps* — https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/First_steps
- MDN, *Introduction to the server side* — https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/First_steps/Introduction
- MDN, *Client-Server overview* — https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/First_steps/Client-Server_overview
- MDN, *Express web framework (Node.js/JavaScript)* — https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/Express_Nodejs
- Node.js, *Introduction to Node.js* / Getting Started — https://nodejs.org/en/learn/getting-started/introduction-to-nodejs
- Express, *Using middleware* — https://expressjs.com/en/guide/using-middleware.html
- roadmap.sh, *Backend Developer Roadmap* — https://roadmap.sh/backend (ordering read from https://roadmap.sh/api/v1-official-roadmap/backend)
- roadmap.sh, *Backend Beginner Roadmap* — https://roadmap.sh/backend-beginner
- roadmap.sh, *API Design Roadmap* — https://roadmap.sh/api-design
- roadmap.sh, *Node.js Roadmap* — https://roadmap.sh/nodejs
- freeCodeCamp curriculum data, `curriculum/structure/curriculum.json`, `curriculum/structure/superblocks/{back-end-development-and-apis,back-end-development-and-apis-v9,data-visualization,front-end-development-libraries}.json`, `curriculum/structure/blocks/{managing-packages-with-npm,basic-node-and-express,mongodb-and-mongoose,back-end-development-and-apis-projects}.json`, and `curriculum/challenges/english/certifications/back-end-development-and-apis.yml` — https://github.com/freeCodeCamp/freeCodeCamp
- freeCodeCamp, *Back End Development and APIs* certification — https://www.freecodecamp.org/learn/back-end-development-and-apis/
- The Odin Project, *NodeJS* course (Full Stack JavaScript path) — https://www.theodinproject.com/paths/full-stack-javascript/courses/nodejs
- The Twelve-Factor App — https://12factor.net/
- Stripe, *Idempotent requests* — https://docs.stripe.com/api/idempotent_requests
- Stripe, *Receive Stripe events in your webhook endpoint* — https://docs.stripe.com/webhooks
- GitHub, *About webhooks* — https://docs.github.com/en/webhooks/about-webhooks
- Zapier, *Getting started with Zapier* — https://help.zapier.com/hc/en-us/articles/8496309697421-Getting-started-with-Zapier
- Zapier, *How Zap triggers work* — https://help.zapier.com/hc/en-us/articles/8496244568589-How-Zap-triggers-work
- Make, *Types of modules* — https://help.make.com/types-of-modules
- Make, *Operations* — https://help.make.com/operations
- Make, *Router* — https://help.make.com/router
- Make Help Center — https://help.make.com/
- n8n, *Key concept glossary* — https://docs.n8n.io/key-concept-glossary/
- Node-RED, *Core concepts* — https://nodered.org/docs/user-guide/concepts
- Node-RED, *Creating your first flow* — https://nodered.org/docs/tutorials/first-flow
