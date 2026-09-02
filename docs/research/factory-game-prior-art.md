# Factory / puzzle / dataflow prior art — how games teach code, and what a "factory of integrations" borrows

**Question:** For a factory-themed web game that teaches automation, integration and backend development to an
absolute beginner: (1) how do teaching games ladder from *no code* to *typed code*, (2) what vocabulary do real
visual dataflow tools use, (3) does any of this actually transfer to real programming, and (4) what makes
factory-game progression compelling — and has anyone already built "factory game that teaches API integration"?

**Researched:** 2026-09-02
**Method:** primary sources — first-party game/store/developer pages, official product documentation for the
dataflow tools, and peer-reviewed / published CS-education papers (ACM, arXiv, journal PDFs) read directly
rather than through summaries. Where only a secondary source exists for a claim, it is marked.

**Design context taken as given** (not re-litigated here): the player is an absolute beginner who cannot write
JavaScript unaided; Level 1 has no text editor; machines are real HTTP endpoints, belts are real HTTP calls,
buffers are real queues, a stall is a real timeout; integrated services are fictional in-universe vendors
served by our own backend. The curriculum spine is settled in
[`backend-curriculum-spine.md`](./backend-curriculum-spine.md).

---

## 1. The fidelity ladder — from no code to typed code

### 1.1 What the CS-education literature says the ladder is actually made of

The single most useful primary source for this project is Kölling, Brown & Altadmri, *Frame-Based Editing:
Easing the Transition from Blocks to Text-Based Programming* (WiPSCE '15, ACM).
([PDF](http://twistedsquare.com/Blocks-Text.pdf) ·
[Kent Academic Repository](https://kar.kent.ac.uk/50406/) · DOI 10.1145/2818314.2818331)

Its section 3 enumerates **thirteen distinct things that get harder** when a learner steps from blocks to text.
This is the real ladder: the gap is not one step, it is thirteen independent difficulties that a designer can
choose to release *one at a time*. Verbatim headings, paraphrased content:

| # | Issue | What blocks give you for free |
|---|---|---|
| 1 | **Readability** | keywords not punctuation; near-natural-language ordering; scope shown as a bracket shape |
| 2 | **Memorisation of commands** | "all available commands are visually represented on screen in a block catalogue" — *recognition over recall* |
| 3 | **Memorisation of syntax** | you never place a comma or brace; also removes the English-keyword burden for non-English speakers |
| 4 | **Typing / spelling** | "the pure mechanical act of typing the program text poses an additional hurdle" |
| 5 | **Number of commands** | a palette is finite and browsable; the Java standard library "includes tens of thousands of methods" |
| 6 | **Prototype vs definition** | a dragged block *is a syntactically correct call*; text gives you a signature you must convert into a call yourself |
| 7 | **Matching identifiers** | dropdowns of known identifiers instead of spelling + case sensitivity |
| 8 | **Grouping** | bracket/indentation errors are "among the most common and persistent syntax errors holding up novice programmers" |
| 9 | **Writing expressions** | block shape shows arity, position and often type of every operand |
| 10 | **Understanding types** | block systems often need only booleans-vs-other; text needs real type understanding |
| 11 | **Interpreting error messages** | block errors are localised; text errors are "notoriously vague, misplaced from their causal location and wrong in their wording" |
| 12 | **Managing layout** | indentation is automatic |
| 13 | **Changing programming paradigm** | e.g. Scratch is object-based, target languages are class-based |

The paper's own answer is *frame-based editing* (the Stride language in Greenfoot): not blocks, not text, and
not a two-view toggle, but "a novel editing method that genuinely incorporates elements from both". Frames keep
structural editing (you can never produce a broken brace) while allowing keyboard entry of expressions.

**Design lesson.** These thirteen are separable dials. backfactory does not need "Level 1 blocks → Level 9 text".
It needs a schedule in which each level hands the player exactly one of these thirteen burdens.

### 1.2 The evidence that blocks beat text *for learning*, and the catch

Weintrop & Wilensky, *Comparing Block-Based and Text-Based Programming in High School Computer Science
Classrooms*, ACM TOCE 18(1), Article 3, 2017.
([DOI 10.1145/3089799](https://dl.acm.org/doi/10.1145/3089799) ·
[PDF](https://www.cs.unm.edu/~learningcomputing/readings/17_weintrop_wilensky.pdf))

- Design: 5-week quasi-experimental study, N = 60 high-schoolers, two sections of the same Intro to Programming
  course, **same teacher, back-to-back periods**, isomorphic block and text versions of the *same* environment
  (Pencil.cc / Pencil Code) — so modality is genuinely the only variable.
- Result: both conditions improved; "students in the blocks condition showed greater learning gains and a higher
  level of interest in future computing courses." Post-assessment means: blocks 66.6% (SD 13.4) vs text 58.8%
  (SD 14.6), from statistically indistinguishable pre-test scores.
- The catch: "Students in the text condition viewed their programming experience as more similar to what
  professional programmers do and as more effective at improving their programming ability."
  On *Pencil.cc is similar to what real programmers do*, text scored M = 6.71 vs blocks M = 5.89 — a difference
  the authors are careful to say is **not statistically significant** (Wilcoxon U = 448.5, p = .23).
- The qualitative data is sharper than the statistics. Student quotes: "I feel like Java will be more useful in
  the long run than what (Pencil.cc) could offer me"; "(Pencil.cc) is a bit too limiting for someone who goes
  into this class thinking I'm going to make something that is going to be used in industry."
  The authors conclude that "students with more prior experience or more serious intentions for future computer
  science instruction appear to be more critical of the authenticity and utility of block-based introductory
  tools."

The authenticity problem is independently reported by Kölling et al., citing Powers et al.: block-based systems
"because of their use primarily in education and toy-like appearance, are often viewed by students as not being
'real' programming, reducing motivation."

**Design lesson — and this is the one that most directly serves backfactory's settled design.** The known
weakness of blocks is *perceived inauthenticity*, and backfactory's core premise is a structural cure for it:
the belts really are HTTP calls, the buffers really are queues, the stall really is a timeout. A player who can
open a network log and see their drag-drop factory issuing real requests is not playing with a toy. **Make the
authenticity legible, not just true** — a visible request/response inspector next to the no-code canvas is
worth more than any amount of "this is real, we promise" copy.

Bau, Gray, Kelleher, Sheldon & Turbak, *Learnable Programming: Blocks and Beyond*, CACM 60(6), June 2017
([arXiv:1705.09413](https://arxiv.org/abs/1705.09413)) names the three mechanisms that make blocks work, and they
are worth keeping as design criteria for a no-code level: blocks "improve learnability for novices by
1) favoring recognition over recall, 2) reducing cognitive load, and 3) preventing errors." The same paper flags
the frontier that is exactly backfactory's territory: "New blocks frameworks are making it easier to access a
variety of APIs through blocks environments, opening the doors to a greater diversity of programming domains."

Blockly, the library most likely to be reached for, states its own purpose in the same terms: it "lets users
program without having to worry about syntax or the intimidation of the command line", and developers "define
the string (usually code) that gets generated for each block", with Blockly "concatenating whole strings of
blocks". Note the framing: "Blockly is for developers, Blockly apps are for learners."
([docs.blockly.com](https://docs.blockly.com/guides/get-started/what-is-blockly))

### 1.3 The canonical game ladder, rung by rung

The Human Resource Machine → TIS-100 comparison is real, but it is not two rungs — it is at least five, and the
interesting ones are *below* HRM and *between* HRM and TIS-100.

**Rung 0 — place components, no instruction list at all.**
*Opus Magnum* (Zachtronics): "Design and build machines that carry out alchemical processes using a variety of
components." Nothing is typed. Programming exists, but as a **timeline of icons** per mechanical arm. What it
teaches: parallelism, resource contention, timing, and above all optimisation as a first-class goal — "compete
against your friends and the world to build the **simplest, fastest, and most compact** solutions", plus
"Export animated GIFs to show them off."
([zachtronics.com/opus-magnum](https://www.zachtronics.com/opus-magnum/))

*Design lesson:* three simultaneous, conflicting score axes are the entire replay economy of a no-code level,
and they are exactly backfactory's natural axes — **latency, throughput, cost**. The GIF export is not a gimmick:
a shareable artefact of your own machine running is the cheapest retention mechanic in this genre.

**Rung 1 — a linear instruction list, drag-and-drop, tiny opcode set.**
*Human Resource Machine* (Tomorrow Corporation): "Program little office workers to solve puzzles", using
"simple drag n' drop commands." The critical numbers are in the official copy: "You start the game with just
**2 commands**, and gradually earn more as you're promoted. The entire language contains only **11 total
commands**." The machine model it names openly is Harvard architecture, a single accumulator, inbox/outbox, and
memory slots. Its promise to the beginner is explicit: "Don't worry if you've never programmed before —
programming is just puzzle solving… You'll be taught everything you need to know." Each level carries optional
"Optimization Challenges … that test how well your solution optimizes for **program size and execution speed**."
([tomorrowcorporation.com/humanresourcemachine](https://tomorrowcorporation.com/humanresourcemachine))

*Design lesson:* **11 verbs, released 2 at a time, is the whole curriculum-pacing device.** A drag-drop
vocabulary that grows by one verb per level is how you get sequencing, branching, looping and subroutine-free
control flow taught with zero typing. For backfactory the verbs are the machine catalogue: `fetch`, `send`,
`branch on status`, `retry`, `wait`, `batch`, `map field`, `store`, `emit`.

**Rung 2 — same drag-drop, but concurrency.**
*7 Billion Humans*: "Automate swarms of office workers to solve puzzles inside your very own parallel computer
made of people", with "a whole new programming language" where "lots of workers can all execute at the same
time", where in HRM "puzzles were all executed by a single worker."
([tomorrowcorporation.com/7billionhumans](https://tomorrowcorporation.com/7billionhumans))

*Design lesson:* Tomorrow Corporation escalated **not** by adding typing but by adding *concurrency*. That is
the highest-value observation in this whole section for backfactory. Race conditions, ordering, and "two
workers grabbed the same item" are teachable with zero syntax — and they are precisely the backend concepts
(queues, workers, at-least-once delivery, idempotency) that the curriculum spine puts mid-game. **You can teach
the hardest backend ideas before you introduce a text editor.**

**Rung 3 — typed assembly, but with a hard ceiling on program size and an out-of-game manual.**
*TIS-100*: "rewrite corrupted code segments to repair the TIS-100 and unlock its secrets"; "Print and explore
the TIS-100 manual, which details the inner-workings of the TIS-100"; "Solve more than 20 puzzles, competing
against your friends and the world to minimize your **cycle, instruction, and node counts**."
([zachtronics.com/tis-100](https://www.zachtronics.com/tis-100/))

*Design lesson:* the first typing rung is survivable because each node holds only a handful of instructions.
The unit of code is small enough to hold in your head. backfactory's equivalent: the first text editor should
edit **one machine's transform function**, not a program.

**Rung 4 — typed code plus a real datasheet-reading skill.**
*SHENZHEN I/O*: "Build circuits using a variety of components from different manufacturers, like
microcontrollers, memory, and logic gates"; "Write code in a compact and powerful assembly language where every
instruction can be conditionally executed"; and the mechanic that matters most — "**Read the included manual,
which includes over 30 pages of original datasheets, reference guides, and technical diagrams.**" Zachtronics
sells a printed copy of that manual for $5 plus shipping.
([zachtronics.com/shenzhen-io](https://www.zachtronics.com/shenzhen-io/))

*EXAPUNKS* does the same trick as an in-fiction magazine: "Learn to hack from **TRASH WORLD NEWS**, the
underground computer magazine. Tutorials, hacking tips, secret information, searing commentary", while the
programming model moves to distributed agents — "Program your EXAs (EXecution Agents) to tear through networks,
replicate themselves, trash files, terminate other EXAs".
([zachtronics.com/exapunks](https://www.zachtronics.com/exapunks/))

*Design lesson — the single strongest transferable mechanic in this document.* Zachtronics turned **reading
vendor documentation** into gameplay. backfactory's fictional vendors should each ship an in-universe API
reference: base URL, auth scheme, rate limits, error codes, a "known issues" section. Learning to read an API
doc *is* the integration skill, and this is the proven way to make it a mechanic rather than a chore. It also
resolves Kölling issue #5 (number of commands): the vendor catalogue is deliberately larger than memory, so
the player is forced into "explore and navigate extensive documentation", which is the real-world skill.

**Rung 5 — you just type the real language.**
*CodeCombat / Ozaria* skips the whole ladder: learners "type real code (in Python or JavaScript)" from early
levels, scaffolded by narrative and worked examples rather than by modality
([blog.codecombat.com](https://blog.codecombat.com/codecombat-launches-ozaria/) ·
[codecombat.com](https://codecombat.com/)).

*Design lesson (cautionary):* CodeCombat's bet is that story and scaffolding can substitute for a fidelity
ladder. Given backfactory's stated player — an absolute beginner who cannot write JavaScript unaided — this is
the model **not** to copy at Level 1. It is a reasonable model for Level 20.

### 1.4 The ladder, restated for backfactory

| Rung | Interaction | Kölling burdens released | Backend concepts teachable here |
|---|---|---|---|
| 0 | place machines, connect belts | none | endpoints, request/response, latency, throughput, bottleneck |
| 1 | ordered instruction strip per machine, palette grows 1–2 verbs/level | #2 partially | sequencing, branching on status code, retry, timeout |
| 2 | multiple machines running concurrently | none new | queues, workers, ordering, duplicate delivery, idempotency, backpressure |
| 3 | fill typed *fields* inside blocks (a URL, a JSON path, a header value) | #4, #7 (typing, identifiers) | auth headers, path/query params, JSON shape |
| 4 | read an in-universe vendor datasheet to configure a machine | #5, #6 (catalogue size, signature→call) | API contracts, rate limits, error taxonomies, webhooks |
| 5 | edit one machine's transform body in a real editor | #1, #3, #8–#12 | expressions, types, real error messages |
| 6 | author a whole endpoint | #13 | the actual job |

The rungs 0–2 carry an unreasonable amount of the backend curriculum with **no text editor at all**. That is the
finding.

## 2. Visual dataflow tool vocabulary

Five products, one shape. Every one of them is a canvas of **nodes** joined by **edges**, started by a
**trigger**, authenticated by a stored **credential**, and observable as a **run**. Where they differ is
exactly where backfactory has to make a decision.

### 2.1 Apache NiFi — the one that is already a factory

NiFi is the closest fit and the best source, because it does not merely resemble flow-based programming, it
declares itself as such: "NiFi's fundamental design concepts closely relate to the main ideas of Flow Based
Programming." The Overview ships an explicit FBP→NiFi mapping table
([nifi.apache.org, NiFi Overview](https://nifi.apache.org/docs/nifi-docs/html/overview.html)):

| FBP term | NiFi term | NiFi's definition (verbatim) |
|---|---|---|
| Information Packet | **FlowFile** | "A FlowFile represents each object moving through the system and for each one, NiFi keeps track of a map of key/value pair attribute strings and its associated content of zero or more bytes." |
| Black Box | **FlowFile Processor** | "Processors actually perform the work… doing some combination of data routing, transformation, or mediation between systems." |
| Bounded Buffer | **Connection** | "Connections provide the actual linkage between processors. These act as queues and allow various processes to interact at differing rates." |
| Scheduler | **Flow Controller** | "The Flow Controller maintains the knowledge of how processes connect and manages the threads and allocations thereof which all processes use." |
| subnet | **Process Group** | "A Process Group is a specific set of processes and their connections, which can receive data via input ports and send data out via output ports." |

Three NiFi features are *literally* factory mechanics and should be stolen wholesale:

- **Back pressure.** "NiFi supports buffering of all queued data as well as the ability to provide back pressure
  as those queues reach specified limits or to age off data as it reaches a specified age." A belt that backs up
  and stops the machine upstream is the same object as a queue depth threshold. This is the single best
  no-code teaching device for backpressure in existence.
- **Prioritized queuing.** "NiFi allows the setting of one or more prioritization schemes for how data is
  retrieved from a queue. The default is oldest first, but there are times when data should be pulled newest
  first, largest first, or some other custom scheme."
- **Data provenance.** "NiFi automatically records, indexes, and makes available provenance data as objects
  flow through the system even across fan-in, fan-out, transformations, and more." This is the answer to
  "how does the player debug?" — a per-item trace, not a log file.

**Design lesson.** NiFi proves the factory metaphor is not a metaphor: item → machine → belt-as-bounded-buffer
is the *actual* architecture of production dataflow systems. backfactory should adopt NiFi's semantics
(bounded belts, backpressure, per-item provenance) and NiFi's word for the item — but keep factory nouns in the
UI, because NiFi's own vocabulary ("FlowFile", "Process Group") is where its beginner-friendliness stops.

### 2.2 n8n — the node/credential/expression vocabulary

From n8n's own *Key concept glossary* ([docs.n8n.io](https://docs.n8n.io/key-concept-glossary/)):

- **Canvas** — "Main interface for building workflows" where you add and connect nodes.
- **Node** — "Individual components that you compose to create workflows", defining execution conditions and
  data processing.
- **Trigger node** — "Special node responsible for executing the workflow in response to certain conditions."
- **Workflow** — "Collection of nodes that automate a process", executing sequentially after trigger activation.
- **Credential** — stores authentication information for connecting with apps and services.
- **Expression** — "Allow you to populate node parameters dynamically by executing JavaScript code."
- **Data pinning** — temporarily freezes node output during development, ignored in production.
- **Cluster node / root node / sub-node** — a root node with sub-nodes that extend its functionality.
- **Template** — "Pre-built workflows designed by n8n and community members."

Two of these are unusually important for backfactory:

- **Expression** is n8n's *fidelity ladder rung 3*. The node is drag-drop; one field inside it takes JavaScript.
  This is precisely the "typed field inside a block" rung, shipped in a production tool used by professionals.
  It is strong evidence that rung 3 is a real, sustainable place to stand — not a training-wheels compromise.
- **Data pinning** is a debugging affordance with no game equivalent yet: freeze one machine's output so you can
  iterate downstream without re-running the world. In a factory that is "put a pallet of known-good widgets at
  this station".

Error vocabulary, from *Handle errors gracefully* and the node settings panel
([docs.n8n.io](https://docs.n8n.io/build/flow-logic/handle-errors-gracefully) ·
[Error Trigger node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.errortrigger)):
**Retry On Fail** (with **Max Tries** and **Wait Between Tries (ms)**), **On Error** (options: *Stop Workflow*
— the default, *Continue*, *Continue (using error output)*), a workflow-level **Error workflow** setting, the
**Error Trigger** node that such a workflow must begin with, and a **Stop and Error** node for deliberate
failure. The Error Trigger receives "the error message, stack trace, and which node failed".

**Design lesson.** n8n has already reduced retry semantics to **three UI controls and one dropdown**. Since the
sibling research concluded that failure modes *are* the curriculum, this is the exact UI surface backfactory
should reproduce on a machine: a retry count, a backoff wait, and a choice of what happens to the item when the
retries run out (halt the line / drop it / divert it to a second belt). "Continue (using error output)" is a
**second output port for failures** — a scrap chute. That is a game mechanic ready-made.

### 2.3 Node-RED — the message and the wire

From *Core concepts* ([nodered.org](https://nodered.org/docs/user-guide/concepts)):

- **Node** — "A Node is the basic building block of a flow." Nodes have **at most one input port** and as many
  output ports as needed.
- **Flow** — "A Flow is represented as a tab within the editor workspace and is the main way to organise nodes."
- **Wire** — "Wires connect the nodes and represent how messages pass through the flow."
- **Message** — "Messages are what pass between the nodes in a flow. They are plain JavaScript objects that can
  have any set of properties", conventionally carrying a `payload` property.
- **Subflow** — "a collection of nodes that are collapsed into a single node in the workspace."
- **Context** — "a way to store information that can be shared between nodes without using the messages that
  pass through a flow", at Node, Flow and Global scope.
- **Palette** — "on the left of the editor and lists of the nodes that are available to use in flows."

**Design lesson.** *One input port, many output ports* is a deceptively strong constraint: it makes every flow
readable as a tree of outcomes, and it makes fan-out (routing by status code) visually obvious while making
fan-in (joining) require an explicit node. A factory belt system naturally has the same asymmetry — merging
belts needs a merger machine. Adopt the constraint. **Context** is also worth noting as the escape hatch that
every dataflow tool eventually needs: shared state that does not travel on the wire. Introduce it late; it is
the thing that makes flows hard to reason about.

### 2.4 Zapier — the vocabulary that beginners actually already know

Consumer-facing, and therefore the most ruthlessly simplified. From Zapier's help centre and developer platform
glossary ([What is a Zap?](https://help.zapier.com/hc/en-us/articles/8496309697421-What-is-a-Zap) ·
[Zapier Glossary](https://docs.zapier.com/platform/quickstart/glossary) ·
[Learn key concepts in Zaps](https://help.zapier.com/hc/en-us/articles/8496181725453-Learn-key-concepts-in-Zaps)):

- **Zap** — "A workflow that connects your apps to automate repetitive tasks", consisting of a trigger and one
  or more actions. Developer-platform wording: "Each Zap consists of a trigger and one or more actions."
- **Trigger** — "The app integration event that starts a Zap when new or updated data is added to your app,
  **either by Zapier polling a specific API endpoint to check for new data or via a notification REST webhook.**"
- **Action** — "The app integration step that finds, creates, or updates data in your app **using a GET, PUT or
  POST request to your API**."
- **Task** — "An action that a Zap successfully completes will count towards task usage."
- **Zap run** — each instance of a Zap being triggered and performing its action steps.
- **Path** — branching, "using conditional if/then logic"; **Filter** — restricts the Zap to run only when
  conditions are met ([Paths](https://help.zapier.com/hc/en-us/articles/8496288555917-Add-branching-logic-to-Zaps-with-Paths)).

**Design lesson — the most quotable line in this whole section.** Zapier's own definition of a trigger
*already names the polling-vs-webhook distinction*, and its definition of an action *already names the HTTP
verb*. The industry's most beginner-facing automation product does not hide HTTP; it states it in the glossary
entry. backfactory can therefore say "this belt is an HTTP POST" on day one without breaking the fiction —
Zapier has established that beginners tolerate this. Also note **Task** as a *metered unit of successful work*:
a billing meter is a scoring mechanic the player will already recognise, and it makes "don't retry blindly"
cost something.

### 2.5 Make — bundles, operations, and the cost model

From Make's help centre ([Types of modules](https://help.make.com/types-of-modules) ·
[Operations](https://help.make.com/operations) · [Router](https://help.make.com/router)):

- **Scenario** — Make's word for a workflow; **Module** — its word for a node.
- **Instant trigger** — "With an instant trigger, the service notifies Make when new data arrives, starting the
  scenario immediately." (i.e. a webhook.)
- **Polling trigger** — "A polling trigger checks for new data in a service account since the last scenario run,
  based on the scenario's schedule."
- **Action module** — "processes the data retrieved from a service", with Get / Create / Update / Delete types.
- **Search module** — "helps you to get specific data from a service such as records, profiles, or other
  objects"; a **List** module returns everything unfiltered.
- **Bundle** — the unit of data a module emits; a polling trigger's new data appears "in the module's output as
  bundles."
- **Operation** — the metered unit, like Zapier's task; **Router** — the branching module.

**Design lesson.** Make draws the instant/polling line *inside the trigger vocabulary itself* — two named kinds
of the same object. That is exactly the mid-game pivot the sibling research identified (webhooks are the pivot),
and Make shows how to stage it: the player builds with a polling trigger first, then swaps the same machine for
its instant variant and watches latency and operation-count collapse. **That swap is a whole level.**

### 2.6 The consolidated vocabulary table

| Concept | NiFi | n8n | Node-RED | Zapier | Make | Proposed backfactory noun |
|---|---|---|---|---|---|---|
| the workspace | canvas / Process Group | Canvas | Flow (tab) | — | Scenario | **floor** |
| the whole program | flow | Workflow | Flow | Zap | Scenario | **line** |
| unit of work | Processor | Node | Node | Step (trigger/action) | Module | **machine** |
| the edge | Connection (a queue) | connection | Wire | — | route | **belt** |
| the thing that travels | FlowFile | item | Message (`msg.payload`) | — | Bundle | **crate / part** |
| what starts it | — | Trigger node | inject / input node | Trigger (poll or REST hook) | Instant / polling trigger | **intake** |
| stored auth | Controller Service | Credential | node config | App connection | Connection | **vendor pass** |
| one run | — | Execution | — | Zap run | scenario run | **shift** |
| branching | Relationship | IF / Switch | multiple output ports | Path / Filter | Router / Filter | **sorter** |
| queue limit | back pressure threshold | — | — | — | — | **belt capacity** |
| metered work | — | — | — | Task | Operation | **operating cost** |
| per-item history | Data Provenance | Execution log | debug node | Zap history | History | **traveller / job card** |
| failure route | failure Relationship | "Continue (using error output)" | catch node | — | error handler | **scrap chute** |
| reuse | Process Group | Sub-workflow | Subflow | — | — | **sub-assembly** |

The gaps in that table are informative. **Nobody consumer-facing exposes queue depth or backpressure** — Zapier
and Make hide it entirely; only NiFi surfaces it, and NiFi is an engineer's tool. Backpressure is therefore a
concept with essentially no beginner-facing prior art, and a factory is the one interface where it is
self-evident. That is backfactory's clearest differentiator.

## 3. Transfer of skill — does any of this work?

Reported honestly: **the evidence is real but thinner and more fragile than the genre's marketing implies, and
the strongest results all come from cases where the transfer was deliberately engineered rather than hoped for.**

### 3.1 The best available meta-analysis, and its uncomfortable moderators

Scherer, Siddiq & Sánchez Viveros, *The Cognitive Benefits of Learning Computer Programming: A Meta-Analysis of
Transfer Effects*, Journal of Educational Psychology 111(5), 2019, 764–792
([DOI 10.1037/edu0000314](https://doi.org/10.1037/edu0000314) ·
[ERIC EJ1220317](https://eric.ed.gov/?id=EJ1220317) ·
[PDF](https://gwern.net/doc/psychology/2019-scherer.pdf)). 3-level random-effects meta-analysis,
**105 studies, 539 effect sizes**.

Headline: "We found evidence for a moderate, overall transfer effect (g = 0.49, 95% CI [0.37, 0.61]) and
identified a strong effect for near transfer (g = 0.75, 95% CI [0.39, 1.11]) and a moderate effect for far
transfer (g = 0.47, 95% CI [0.35, 0.59])."

Their definitions matter: "near transfer refers to successful transfer between similar contexts… far transfer
refers to successful transfer between dissimilar contexts" (after Barnett & Ceci 2002; Perkins & Salomon 1992).

Now the moderators, which are where the honesty lives:

- **Active control groups collapse the effect.** "transfer effect sizes were significantly lower for studies
  including treated control groups (g = 0.16) than for studies including untreated control groups (g = 0.65)",
  QM(1) = 40.12, p < .001. For far transfer specifically: g = 0.15 with treated controls vs 0.64 with
  untreated. In plain terms: **against "do nothing", programming looks great; against "do some other
  structured activity", most of the effect disappears.**
- **Randomisation collapses it too.** "Studies with random group assignment (g = 0.29) showed lower near
  transfer effects than for those without (g = 0.95)."
- **Publication bias is present.** "Significantly higher effects occurred for published literature (g = 0.60)
  than for gray literature (g = 0.34)"; the authors report Egger's test and a model adjusted for publication
  bias fitting better than the unadjusted one, concluding "the presence of some publication bias and
  small-study effects."

Note also what this meta-analysis is *not*: it measures transfer **from** programming **to** other cognitive
skills. backfactory's question is the reverse and easier one — transfer from a programming-like game **to**
programming — which is squarely *near* transfer. The near-transfer estimate is the relevant one, and it is the
one with the widest confidence interval (0.39–1.11) and the biggest randomisation penalty.

### 3.2 The field's own methodological critique

Scherer's earlier position paper, *Learning from the Past — The Need for Empirical Evidence on the Transfer
Effects of Computer Programming Skills*, Frontiers in Psychology 2016
([PMC5021694](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5021694/)) is blunt: "there is very little evidence
on the transfer effects of computer programming skills in the context of twenty-first century education", and
"many publications that argued for the transfer effects later on only described programming tools or
feasibility studies thereof on a conceptual level." It sets three bars for any credible claim: outcome measures
"that go beyond students' self-reports, enjoyment, or interest"; pretest–posttest designs with treatment and
control groups; and random assignment.

That standard indicts most of the "programming game" literature. Reviews of serious games for programming
repeatedly report that **most studies measure engagement, not learning**, with heterogeneous designs, absent
control groups and short interventions
([Vahldick, Mendes & Marcelino, *A Review of Games Designed to Improve Introductory Computer Programming
Competencies*, FIE 2014](https://link.springer.com/chapter/10.1007/978-3-030-02762-9_21) ·
[*Game-based learning in computer science education: a scoping literature review*, IJ STEM Ed 2023](https://link.springer.com/article/10.1186/s40594-023-00447-2)).

### 3.3 Where transfer *has* been demonstrated — and the pattern is consistent

Three positive results, and they share a mechanism.

- **Weintrop & Wilensky 2017** (details in §1.2): blocks beat isomorphic text on learning gains at 5 weeks with
  the same teacher and same content. This is the cleanest modality experiment available.
  [DOI 10.1145/3089799](https://dl.acm.org/doi/10.1145/3089799)
- **Armoni, Meerbaum-Salant & Ben-Ari, *From Scratch to "Real" Programming*, ACM TOCE 14(4), 2015**
  ([DOI 10.1145/2677087](https://dl.acm.org/doi/10.1145/2677087)). Longitudinal: middle-schoolers who learned
  CS in Scratch, then studied C#/Java in secondary school. Prior Scratch experience "greatly facilitated"
  later learning — less time to learn new topics, fewer difficulties, higher cognitive levels of understanding
  for most concepts. Kölling et al. summarise the same result as "a slight improvement in performance, but
  also… students with exposure to Scratch were better motivated and learnt more quickly."
- **Dann, Cosgrove, Slater, Culyba & Cooper, *Mediated Transfer: Alice 3 to Java*, SIGCSE 2012**
  ([DOI 10.1145/2157136.2157180](https://dl.acm.org/doi/10.1145/2157136.2157180) ·
  [slides/PDF](https://personal.utdallas.edu/~veerasam/lectures/KG/Alice_Java/Alice_Java_MediatedTransfer.pdf)).
  Two years, treatment vs non-treatment sections, "dramatic improvement" in treatment test scores.

**The mechanism is the same in all three, and it is the finding of this section.** Transfer happened where the
two representations were made *explicitly isomorphic* and the learner was walked across the correspondence:
Weintrop used isomorphic block and text versions of the *same* environment; Dann et al. built Alice 3 to show a
Java preview beside the blocks and used "the exact same example in both Alice and Java"; the Kölling review
notes that the field's standard practice is "reference aids showing fragments of blocks and text programming
side by side with equivalent behaviour."

**Design lesson for backfactory — the most actionable in this document.** Transfer is not a side effect of
playing. It is a *feature you build*. The corresponding feature here is obvious and cheap: **every no-code
machine must be able to show the real HTTP request it makes, and later, the real code it corresponds to.**
A "show me the request" / "show me the code" toggle on every machine is the mediated-transfer scaffold that the
literature says is the difference between a game that teaches and a game that entertains.

### 3.4 The credible critique, from the person best placed to make it

Zach Barth, creative director of Zachtronics — the studio whose games are most often cited as teaching
programming — explicitly rejects the claim. In an interview about SHENZHEN I/O
([Inverse, 2016](https://www.inverse.com/article/23382-shenzhen-io-zachtronics-zach-barth-interview)) he is
reported as "adamant that *Shenzhen I/O* is not an education game", and gives a design reason that every
educational-game designer should sit with:

> "You can't subtract out the stuff that makes it hard [in educational games]."
> "If you really wanted to make a game about electronics, you'd have to take on all those issues."

And, on the framing itself: "escapism isn't evil and education isn't good. A lot of people talk about education
as if it's some kind of monolithic valuable thing… but that's kind of not how it actually works."

This is the strongest available argument against backfactory's premise, and it deserves a straight answer.
Barth's claim is that a game becomes fun *by removing the real difficulty*, and that what remains therefore does
not transfer. **backfactory's settled design is, structurally, the counter-argument:** if the machines are real
HTTP endpoints and the stalls are real timeouts, the difficulty has not been subtracted — it has been
*staged*. The failure modes are authored, but they are not fake. Whether that holds up is an empirical
question the project should be honest about, not a rhetorical one.

### 3.5 Honest summary

| Claim | Verdict |
|---|---|
| Playing a programming puzzle game makes you a programmer | **Not supported.** No credible study demonstrates it; the designer of the canonical examples denies it. |
| Block/visual environments produce better *learning gains* than text for novices | **Supported**, at least at 5 weeks with matched content (Weintrop & Wilensky 2017). |
| Prior visual-programming experience makes later text programming easier | **Supported**, longitudinally (Armoni et al. 2015; Dann et al. 2012), *where transfer was explicitly mediated*. |
| Programming transfers to general cognitive skill | **Weakly supported and shrinking** — g = 0.49 overall, but 0.16 against active controls, 0.29 for near transfer under randomisation (Scherer et al. 2019). |
| The serious-games-for-programming literature measures learning | **Largely not** — it mostly measures engagement (Vahldick et al.; IJ STEM Ed scoping review). |

## 4. Progression loops, and direct precedent

### 4.1 What actually makes Factorio's progression work

**The loop is stated plainly on the store page**: "In the beginning you will find yourself chopping trees,
mining ores and crafting mechanical arms and transport belts by hand, but in short time you can become an
industrial powerhouse", via "mining resources, researching technologies, building infrastructure, automating
production and fighting enemies."
([store.steampowered.com/app/427520](https://store.steampowered.com/app/427520/Factorio/))

Two Wube developer posts explain the machinery underneath.

**Gating on experience, not on currency — *Friday Facts #376: Research and Technology***
([factorio.com/blog/post/fff-376](https://www.factorio.com/blog/post/fff-376)). Wube identified that players
could "research very far ahead from what their factory can currently process", so they would unlock oil
refining "without having seen crude oil yet" and then face "all the piled up recipes to work through". Their
fix was **trigger technologies** — research unlocked by "Mining an entity", "Crafting an item/fluid", or
"Launching a rocket with a certain item", so discoveries feel "quite natural, as everything happens while
you're actually there." Their stated principle:

> "it's always felt much better in games when your progression starts as low as possible and you can earn all
> of the things in the process, which makes it all feel much more deserved in the end."

*Design lesson.* backfactory's unlock condition should be **"you have encountered this problem"**, not "you
have accumulated N points". Unlock the retry machine the first time a vendor 500s on you. Unlock the queue the
first time a belt backs up. Unlock the webhook intake the first time a polling machine wastes a whole shift on
empty responses. This is *identical* to "you must touch crude oil before researching oil processing", and it
is how failure modes become curriculum rather than punishment.

**Throughput ceilings drive progression — *Friday Facts #225: Bots versus belts (part 2)***
([factorio.com/blog/post/fff-225](https://factorio.com/blog/post/fff-225)). Concrete numbers from kovarex:
"bots did 16.4k per minute while belts only 9.6"; with speed research, bots reach "maximum around 19.5k per
minute"; overall "robots are currently around 5+ times stronger compared to belts". The design failure they
name is the one to avoid: "bots are the one solution for everything. With bots, there is no reason to think
about other types of transport."

*Design lesson (a warning).* A dominant strategy kills a factory game's design space. In backfactory the
obvious candidate for a bot-like dominant strategy is **"just add more workers / raise concurrency"**. If
scaling out solves every problem, then rate limits, idempotency, caching, batching and backpressure all become
irrelevant, and the curriculum evaporates. The fictional vendors are the lever: **vendor rate limits and
per-call costs must make brute-force concurrency actively worse**, not merely equal.

### 4.2 shapez 2 — the purity argument

"Automate the extraction, production and delivery of geometric shapes. Each shape is its own set of building
blocks that your factories can take apart, stack, paint and put back together in near-infinite different ways."
Research works by delivery: "Deliver specific shapes to unlock new technologies, expanding your factory's
capabilities and scale."

And the differentiating claim: it is "the purest factory simulation game imaginable: all buildings are free,
resources never run out and there are no enemies or time limits", so players can "effortlessly delete, redesign
and rebuild."
([store.steampowered.com/app/2162800](https://store.steampowered.com/app/2162800/shapez_2/))

*Design lesson.* shapez proves the factory loop survives with **scarcity, combat and time pressure all
removed**, leaving only *complexity* as the source of difficulty. For a teaching game that is close to
mandatory: an absolute beginner who is also being punished by a clock will quit. **Remove every pressure except
the one you are teaching.** In backfactory the single retained pressure should be the *demanded output rate* —
the order the factory must fill — because that is what makes latency, throughput and bottleneck legible. Free
buildings and free rebuilds; no timers.

Also note shapez's research mechanic: **research is unlocked by delivering the thing**. That maps onto
backfactory perfectly — unlock the next vendor by successfully fulfilling the current vendor's contract.

### 4.3 Direct precedent — the honest answer is *no, but four things get close*

I searched for a factory/automation game that teaches backend or API integration and **found none**. The
adjacent space, ordered by how close it gets:

| Title | Mechanic | What it teaches | Distance from backfactory |
|---|---|---|---|
| **while True: learn()** | "Drag objects around your screen with a mouse! Connect them with lines… Try. Fail. Optimize. Try again." Explicitly "No coding experience required!" | Machine-learning pipeline concepts, "loosely based on real-life machine learning technologies: from goofy Expert Systems to mighty Recurrent Neural Networks" | **Closest on interaction.** Node-and-wire, zero code, real technical domain, puzzle-optimise loop. But the pipeline is simulated, not real. ([Steam](https://store.steampowered.com/app/619150/while_True_learn/)) |
| **Screeps: World** | "You control your colony by writing JavaScript code that operates 24/7 in the huge persistent online open world" — code runs on the server while you are offline | Real JavaScript, real long-running distributed process, real resource contention against other players | **Closest on authenticity.** This is the only commercial game where the player's program is genuinely a persistently-running server-side service. Zero on-ramp for a beginner. ([Steam](https://store.steampowered.com/app/464350/Screeps_World/)) |
| **The Farmer Was Replaced** | "Program a drone using a simple python-like language to fully automate various farming tasks"; "Farming earns you resources which can be spent to unlock new technology"; "continuous progression" rather than levels | Real programming concepts in a Python-like language — "The beginning of the game is designed to teach you all the basic programming concepts you will need by **introducing them one at a time**" | **Closest on curriculum pacing.** Proves the automation-loop + one-concept-per-unlock structure works commercially. Text editor from minute one, so no fidelity ladder. ([Steam](https://store.steampowered.com/app/2060160/The_Farmer_Was_Replaced/)) |
| **Autonauts** | "Teach your workerbots requisite steps to complete tasks by asking them to repeat actions utilising a visual programming language" — programming by demonstration, then editing the recorded script | Loops and branches, without ever naming them | **The best rung-0 model.** Programming by demonstration is the lowest-friction "no code at all" entry that still produces an editable program. ([Steam](https://store.steampowered.com/app/979120/Autonauts/)) |

Two more worth naming:

- **API Challenges** (Alan Richardson / eviltester) is the closest *non-game* precedent for the content:
  a hosted set of real APIs where "You learn by sending real HTTP requests, checking responses, and working
  through guided challenges", covering HTTP fundamentals, REST, methods, status codes, auth, content
  negotiation, and OpenAPI, with a tracked challenge list and deliberately *buggy* APIs to test against.
  ([apichallenges.com](https://apichallenges.com/)) It validates the content model — including authored
  failure — but it has no visual layer, no factory, and assumes the learner can already drive a REST client.
- **Zapier / Make / n8n themselves** are the real competition for the beginner's attention. They are already
  free, already visual, and already integrate with real vendors. backfactory's differentiator cannot be
  "visual automation" — that market is saturated. It has to be **the authored failure and the visible
  machinery**: Zapier will never show you the queue depth, never let a vendor rate-limit you on purpose, and
  never turn a 429 into a lesson.

**Conclusion for Q4:** the niche is genuinely empty. Nobody has built a factory game about backend
integration. The reason is probably not that nobody thought of it — it is that the two halves pull in opposite
directions, which is the risk stated at the end of this document.

## Design lessons for backfactory

Ordered by how much they should change the design, not by section.

1. **Build the mediated-transfer scaffold as a core feature, not a nice-to-have.** Every positive transfer
   result in the literature (Weintrop & Wilensky 2017; Armoni et al. 2015; Dann et al. 2012) came from
   deliberately showing the learner the *same thing* in both representations, side by side. Concretely: every
   machine gets a **"show the request"** panel and, from the mid-game, a **"show the code"** panel. This is the
   single highest-leverage feature in the whole design and it is cheap, because backfactory's machines are
   real endpoints — the request already exists.

2. **The fidelity ladder has 13 rungs, not 2.** Use Kölling et al.'s list of transition burdens as the release
   schedule. Ship typing (#4) and identifiers (#7) as "fill this URL field inside a block" long before you
   ship syntax (#3), grouping (#8) or error-message interpretation (#11). n8n's **Expression** field proves
   the "typed field inside a drag-drop node" rung is a place professionals are happy to stand.

3. **Concurrency before syntax.** 7 Billion Humans escalated from Human Resource Machine by adding *parallel
   workers*, not text. Queues, workers, ordering, duplicate delivery, idempotency and backpressure are all
   teachable with zero typing — and they are exactly the mid-game concepts the curriculum spine cares about.
   Level 1 having no text editor is not a limitation on what you can teach; it barely constrains you at all.

4. **Steal NiFi's semantics, keep factory nouns.** NiFi's Connection *is a bounded queue with backpressure*,
   and NiFi is the only tool in the survey that exposes queue depth to a user. A belt with a capacity that
   backs up and stalls the machine upstream is the clearest teaching device for backpressure that exists, and
   **no consumer-facing automation product offers it**. Add prioritised queuing and per-item provenance
   ("this crate's traveller card") and the debugging story writes itself.

5. **Make the retry semantics a four-control panel, copied from n8n.** *Retry On Fail* / *Max Tries* /
   *Wait Between Tries* / *On Error → {Stop, Continue, Continue using error output}*. "Continue using error
   output" is a second output port for failures — a **scrap chute** — and it turns error handling into a
   visible piece of factory layout rather than an invisible setting.

6. **Unlock on encounter, never on currency (Factorio FFF-376).** Trigger technologies: the player unlocks the
   retry machine by *being rate-limited*, the queue by *watching a belt back up*, the webhook intake by
   *wasting a shift polling nothing*. This is the mechanism that makes "failure modes are the curriculum" into
   a progression system instead of a lecture.

7. **Stage the polling → webhook pivot as a machine swap (Make).** Make's own vocabulary names two kinds of
   trigger — instant and polling — for the same job. Give the player the polling intake first, let them feel
   the latency and the wasted operating cost, then hand them the instant variant of the *same* machine. That
   swap, with a before/after on latency and cost, is a complete level.

8. **Three conflicting score axes, and a shareable artefact (Opus Magnum).** Score every solution on
   **latency, throughput and cost** simultaneously, show a histogram against other players, and let them
   export an animation of their line running. Zachtronics' optimisation-challenge economy is what makes people
   replay a solved puzzle, and replaying a solved puzzle is where the learning consolidates.

9. **Turn vendor documentation into gameplay (SHENZHEN I/O, EXAPUNKS).** Each fictional vendor ships an
   in-universe API reference — base URL, auth, rate limits, error codes, a "known issues" page. Make the
   catalogue deliberately larger than memory so the player must learn to navigate documentation, which is
   Kölling burden #5 and also the actual professional skill. This is the mechanic with the highest ratio of
   teaching value to implementation cost in this entire document.

10. **Remove every pressure except the demanded output rate (shapez 2).** Free buildings, free rebuilds,
    infinite resources, no combat, no timers. The only pressure is the order you have to fill. An absolute
    beginner under time pressure quits; an absolute beginner staring at a bottleneck learns.

11. **Design against the dominant strategy now (Factorio FFF-225).** "Bots are the one solution for
    everything" is the failure to avoid, and backfactory's version is "just raise concurrency". Vendor rate
    limits and per-call operating cost must make brute-force scaling *strictly worse*, or rate limiting,
    caching, batching and idempotency become decorative.

12. **Do not hide HTTP.** Zapier's own glossary tells beginners that a trigger works "either by Zapier polling
    a specific API endpoint… or via a notification REST webhook" and that an action uses "a GET, PUT or POST
    request to your API". The most beginner-facing automation product on earth names the verbs in its
    glossary. backfactory can name them on day one.

13. **Meter the work (Zapier's Task, Make's Operation).** A visible count of successful operations, billed,
    is a scoring mechanic beginners already understand from these products — and it is the thing that makes
    a naive retry loop *hurt* in a way the player can read.

14. **Borrow n8n's data pinning as a debug affordance.** Freezing one machine's output so you can iterate
    downstream without re-running the world has no equivalent in any factory game, and in factory language it
    is obvious: park a pallet of known-good crates at this station.

15. **Be honest in the marketing.** The evidence does not support "play this and you'll be a backend
    developer". It supports "visual first produces better learning gains, and prior visual experience makes
    later text programming easier *when the correspondence is made explicit*". Claim that, and build the
    feature (#1) that makes it true.

---

## Biggest risk this prior art reveals

**The authenticity trap: the closer backfactory gets to being a real backend, the more the two halves fight
each other — and the prior art shows both halves losing.**

The evidence converges from three directions:

- Weintrop & Wilensky found the *one* thing blocks lose to text is perceived authenticity, and the students who
  felt it most were the motivated ones — "students with more prior experience or more serious intentions for
  future computer science instruction appear to be more critical of the authenticity and utility of
  block-based introductory tools." Those are backfactory's eventual advocates.
- Zach Barth, from the other side, argues that a *game* is only fun because it subtracted the real difficulty:
  "You can't subtract out the stuff that makes it hard… If you really wanted to make a game about electronics,
  you'd have to take on all those issues. Zachtronics never wanted to." backfactory has explicitly chosen to
  take on those issues — real HTTP, real queues, real timeouts. Barth's position is that this is precisely
  where the fun dies.
- And the empty niche in §4.3 is evidence, not opportunity: **nobody has shipped this because the two halves
  pull apart.** Make the backend real and you inherit real latency, real flakiness and real debugging — which
  is work, not play. Make it a satisfying factory and the "real backend" becomes a skin, at which point the
  transfer story collapses and you have built a slower n8n with sprites.

The design that fails is the one that resolves this by drifting: authored failures get softened because they
frustrate playtesters, timeouts get shortened because waiting is boring, the vendor APIs get forgiving because
reading docs is friction — and six months in the factory is a metaphor again, with all the honesty spent and
none of the fun gained.

**The mitigation the prior art suggests** is that *legibility*, not softening, is what makes real difficulty
playable. Zachtronics' answer to "assembly is hard" was not to make it easier but to make the whole machine
state visible on one screen. NiFi's answer to "distributed dataflow is opaque" was per-item provenance.
Factorio's answer to "this is complicated" was that every bottleneck is physically visible as a gap in a belt.
So: **do not soften the failure; render it.** A timeout the player can *see* as a stalled machine with a
backed-up belt and a traveller card showing where the crate stopped is not frustrating — it is a puzzle. The
same timeout as a red toast notification is a bug report.

The single test to run early, before building anything else: **can a non-programmer, with no text editor,
diagnose an authored vendor failure purely from what the factory floor shows them?** If yes, the two halves
hold together. If they need an explanation, the premise does not survive contact with a real player, and it is
better to learn that in week two than in month six.

## Sources

### CS-education research (peer-reviewed)

- Kölling, M., Brown, N. C. C. & Altadmri, A. (2015). *Frame-Based Editing: Easing the Transition from Blocks
  to Text-Based Programming.* WiPSCE '15, ACM. DOI 10.1145/2818314.2818331 —
  https://kar.kent.ac.uk/50406/ · PDF: http://twistedsquare.com/Blocks-Text.pdf
- Weintrop, D. & Wilensky, U. (2017). *Comparing Block-Based and Text-Based Programming in High School Computer
  Science Classrooms.* ACM TOCE 18(1), Art. 3. DOI 10.1145/3089799 — https://dl.acm.org/doi/10.1145/3089799 ·
  PDF: https://www.cs.unm.edu/~learningcomputing/readings/17_weintrop_wilensky.pdf
- Bau, D., Gray, J., Kelleher, C., Sheldon, J. & Turbak, F. (2017). *Learnable Programming: Blocks and Beyond.*
  CACM 60(6), 72–80 — https://arxiv.org/abs/1705.09413
- Armoni, M., Meerbaum-Salant, O. & Ben-Ari, M. (2015). *From Scratch to "Real" Programming.* ACM TOCE 14(4).
  DOI 10.1145/2677087 — https://dl.acm.org/doi/10.1145/2677087
- Dann, W., Cosgrove, D., Slater, D., Culyba, D. & Cooper, S. (2012). *Mediated Transfer: Alice 3 to Java.*
  SIGCSE '12. DOI 10.1145/2157136.2157180 — https://dl.acm.org/doi/10.1145/2157136.2157180
- Scherer, R., Siddiq, F. & Sánchez Viveros, B. (2019). *The Cognitive Benefits of Learning Computer
  Programming: A Meta-Analysis of Transfer Effects.* Journal of Educational Psychology 111(5), 764–792.
  DOI 10.1037/edu0000314 — https://eric.ed.gov/?id=EJ1220317 · PDF: https://gwern.net/doc/psychology/2019-scherer.pdf
- Scherer, R. (2016). *Learning from the Past — The Need for Empirical Evidence on the Transfer Effects of
  Computer Programming Skills.* Frontiers in Psychology —
  https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5021694/
- Strong, G., Bresnihan, N. & Tangney, B. (2025). *Supporting learners in the transition from block-based to
  text-based programming, a systematic review.* Journal of Computer Languages —
  https://www.sciencedirect.com/science/article/pii/S2590118425000280 *(abstract only; full text 403 at time
  of research — cited for existence and scope, not for specific findings)*
- Vahldick, A., Mendes, A. J. & Marcelino, M. J. *A Review of Games Designed to Improve Introductory Computer
  Programming Competencies* (FIE 2014) / *A Review of Serious Games for Programming* —
  https://link.springer.com/chapter/10.1007/978-3-030-02762-9_21
- *Game-based learning in computer science education: a scoping literature review.* International Journal of
  STEM Education (2023) — https://link.springer.com/article/10.1186/s40594-023-00447-2 *(paywall redirect at
  time of research; cited via its abstract)*

### Product documentation (first-party)

- Apache NiFi, *NiFi Overview* (core concepts and the FBP mapping table) —
  https://nifi.apache.org/docs/nifi-docs/html/overview.html
- n8n, *Key concept glossary* — https://docs.n8n.io/key-concept-glossary/
- n8n, *Handle errors gracefully* — https://docs.n8n.io/build/flow-logic/handle-errors-gracefully
- n8n, *Error Trigger node* — https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.errortrigger
- Node-RED, *Core concepts* — https://nodered.org/docs/user-guide/concepts
- Zapier, *What is a Zap?* — https://help.zapier.com/hc/en-us/articles/8496309697421-What-is-a-Zap
- Zapier, *Learn key concepts in Zaps* — https://help.zapier.com/hc/en-us/articles/8496181725453-Learn-key-concepts-in-Zaps
- Zapier, *Add branching logic to Zaps with Paths* — https://help.zapier.com/hc/en-us/articles/8496288555917-Add-branching-logic-to-Zaps-with-Paths
- Zapier Platform, *Glossary* — https://docs.zapier.com/platform/quickstart/glossary
- Make, *Types of modules* — https://help.make.com/types-of-modules
- Make, *Operations* — https://help.make.com/operations · *Router* — https://help.make.com/router
- Blockly, *What is Blockly?* — https://docs.blockly.com/guides/get-started/what-is-blockly

### Game and developer primary sources

- Tomorrow Corporation, *Human Resource Machine* — https://tomorrowcorporation.com/humanresourcemachine
- Tomorrow Corporation, *7 Billion Humans* — https://tomorrowcorporation.com/7billionhumans
- Zachtronics, *TIS-100* — https://www.zachtronics.com/tis-100/
- Zachtronics, *SHENZHEN I/O* — https://www.zachtronics.com/shenzhen-io/
- Zachtronics, *EXAPUNKS* — https://www.zachtronics.com/exapunks/
- Zachtronics, *Opus Magnum* — https://www.zachtronics.com/opus-magnum/
- Wube Software, *Friday Facts #376 — Research and Technology* — https://www.factorio.com/blog/post/fff-376
- Wube Software, *Friday Facts #225 — Bots versus belts (part 2)* — https://factorio.com/blog/post/fff-225
- *Factorio* on Steam — https://store.steampowered.com/app/427520/Factorio/
- *shapez 2* on Steam — https://store.steampowered.com/app/2162800/shapez_2/
- *while True: learn()* on Steam — https://store.steampowered.com/app/619150/while_True_learn/
- *Screeps: World* on Steam — https://store.steampowered.com/app/464350/Screeps_World/
- *The Farmer Was Replaced* on Steam — https://store.steampowered.com/app/2060160/The_Farmer_Was_Replaced/
- *Autonauts* on Steam — https://store.steampowered.com/app/979120/Autonauts/
- CodeCombat, *Introducing Ozaria* — https://blog.codecombat.com/codecombat-launches-ozaria/ ·
  https://codecombat.com/ · https://www.ozaria.com/
- Alan Richardson, *API Challenges* — https://apichallenges.com/

### Secondary source, used once and marked as such

- Inverse (2016), *'Shenzhen I/O' Is an Abstract Educational Game* — interview quotes from Zach Barth —
  https://www.inverse.com/article/23382-shenzhen-io-zachtronics-zach-barth-interview
  *(This is journalism, not a first-party statement. It is the only accessible record of Barth stating his
  position on educational games; the quotes in §3.4 are the journalist's transcription. Treat as
  attributed-but-unverified.)*
