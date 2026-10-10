# What Engineers Are Expected to Do After Generative AI

*The job is no longer to write the code. It is to define the work, give agents the right context, verify the result, and leave the system easier to change than you found it.*

A few years ago, if an engineer could take a ticket, write solid code, get it through review, and support it in production, most teams would say they were doing the job well.

That is still useful. It is just no longer enough.

Generative AI can now handle a large part of the mechanical work: exploring a repository, scaffolding an endpoint, writing tests, changing configuration, and drafting documentation. Tasks that once took days can sometimes take hours.

But the faster we can produce code, the easier it becomes to produce the wrong code at impressive speed.

This changes what teams should expect from engineers. The old expectation was mainly about implementation. The new expectation is about shaping work so that humans and agents can complete it safely.

The change is simple to state:

**Before GenAI, engineers were expected to turn requirements into code. After GenAI, engineers are expected to turn unclear needs into reliable systems of work.**

That includes code but it also includes context, contracts, validation, sequencing, and feedback.

<p style="font-size: small; color: #6b7280; margin-top: 0.5em;">By Rodrigo A. Diaz Leven — <a href="https://www.linkedin.com/in/rodrigoleven/">LinkedIn</a> · <a href="https://github.com/bruj0">GitHub</a> · <a href="https://resume.bruj0.net/bruj0/rodrigo-leven">Resume</a></p>


## Table of contents

- [What made this change obvious to me](#what-made-this-change-obvious-to-me)
  - [Building a better software-delivery system](#building-a-better-software-delivery-system)
  - [Designing automation from evidence, not assumptions](#designing-automation-from-evidence-not-assumptions)
- [The old job was built around scarce implementation](#the-old-job-was-built-around-scarce-implementation)
- [The new job is built around cheap generation and expensive mistakes](#the-new-job-is-built-around-cheap-generation-and-expensive-mistakes)
- [Prompting is useful, but it is not a delivery process](#prompting-is-useful-but-it-is-not-a-delivery-process)
- [Real-world example one: the feature that was really an idempotency bug](#real-world-example-one-the-feature-that-was-really-an-idempotency-bug)
- [Real-world example two: an endpoint is more than a handler](#real-world-example-two-an-endpoint-is-more-than-a-handler)
- [The review moves upstream, but it does not disappear](#the-review-moves-upstream-but-it-does-not-disappear)
- [Engineers now maintain instructions as well as systems](#engineers-now-maintain-instructions-as-well-as-systems)
- [Working with agents becomes a team skill](#working-with-agents-becomes-a-team-skill)
- [What good looks like now](#what-good-looks-like-now)
- [What should not change](#what-should-not-change)
- [The expectation is higher, not lower](#the-expectation-is-higher-not-lower)
- [About the author](#about-the-author)

## What made this change obvious to me

In one platform engagement, the user-story board and RFCs made the shift visible. The clearest examples fell into two groups.

### Building a better software-delivery system

One project started with a familiar complaint: deploying a platform service required more than 200 lines of configuration, took two or three days, and failed about one time in five. Engineers spent a large part of their week writing configuration, debugging formatting mistakes, and waiting for a delivery controller to reconcile changes.

The tempting GenAI answer was simple: ask an agent to write the same configuration faster. That would have reduced typing while preserving the slow and fragile delivery process around it.

Instead, I treated the problem as a redesign of the delivery system. The RFC described the target architecture, and the user-story board turned it into an ordered backlog:

- **Create the delivery repository first.** Give generated work a clear home for shared environment configuration, application definitions, internal templates, agent instructions, and the deployment pipeline.
- **Define a small intent contract.** Engineers describe the application, target environments, required infrastructure, and network exposure in one application specification instead of editing hundreds of lines across several files.
- **Separate shared facts from generated output.** Account identifiers, network settings, database endpoints, and domain information come from one environment configuration rather than being copied into every service.
- **Encode architectural guardrails.** Generated changes cannot hard-code environment values, bypass validation, use an unsupported network pattern, or create infrastructure outside the approved abstraction.
- **Build validation before broad adoption.** The pipeline checks the application specification, renders the required resources, estimates infrastructure cost, and rejects invalid changes before deployment.
- **Create reusable agent skills.** Separate workflows cover installing an application, modifying one, adding infrastructure, migrating an existing service, synchronizing environment data, and validating the final specification.
- **Test through progressively harder pilots.** Start with a simple service, continue with a service that depends on shared secrets or storage, and finish with a complex service that needs a database, identity permissions, and external access.

That sequence mattered. Repository structure and guardrails came before the agent skills. Validation came before the complex migrations. Each story had prerequisites and a testable result, so “the agent generated some files” could not be mistaken for “the delivery system works.”

The outcome was not merely faster configuration generation. Development deployments fell from days to about half an hour, failures dropped sharply, engineers spent far less time editing configuration, and people outside the platform team could deploy through the same guarded workflow.

**That changed the engineer expectation. The job was not to produce configuration. It was to design the contract, backlog, validation, and guardrails that made generated configuration safe enough for other engineers to use without needing a platform specialist beside them.**

### Designing automation from evidence, not assumptions

The other examples involved work where the first proposed solution was too narrow. In both cases, the RFC process forced the team to investigate the system and sequence the work before automating it.

- **Recurring delivery-pipeline failures:** The first idea was to add more preflight checks. Research showed that many reported failures were transient or had already been fixed, so a large prevention system would have automated assumptions rather than facts. The user story changed direction: start with a tracker that classified failures and created a reliable baseline. Only then did the RFC introduce automated recommendations and remediation, with limited file access, a canary run, a rollback path, and human approval for risky or production-facing changes.
- **A fleet-wide build outage:** The immediate task was to rotate an expired certificate and restore service. The investigation found deeper problems: several replicas were running on one physical node, many repositories shared the same failure point, and the build service required an unnecessarily broad security posture. The resulting RFC defined a phased migration: prove the new build path in one repository, update the shared templates and consumers, improve storage performance, and only then remove the old daemon and dedicated infrastructure.

**The associated stories preserved those decisions after the design meetings ended. A later engineer could see why the tracker came before remediation and why “phase one works” did not mean “the migration is done.”**

Those examples changed how I think about engineering expectations. In each case, generating code or configuration was the easy part. The harder and more valuable work was finding the real problem, turning it into a contract and backlog, sequencing it safely, and leaving enough context for another person or agent to continue.

## The old job was built around scarce implementation

Before GenAI, code was expensive to produce.

An engineer had to find the right files, understand the local conventions, remember the framework details, write the implementation, fix syntax errors, add tests, and explain the change in review. Even when the design was clear, producing the code took time.

That shaped the expectations around the role.

| Before GenAI | What the team expected |
|---|---|
| **Know your part of the stack** | Build deep familiarity with a language, framework, service, or infrastructure layer |
| **Write the implementation** | Translate a ticket or design into working code |
| **Catch problems in code review** | Read the diff line by line and find mistakes before merge |
| **Carry context in your head** | Remember conventions, dependencies, and past decisions |
| **Share knowledge through conversation** | Explain the system in pairing sessions, reviews, and team discussions |
| **Estimate effort** | Predict how long implementation would take based on manual work |
| **Own the change** | Get it deployed and respond when it failed |

This model made sense because implementation was the bottleneck. If a feature required five days of coding, careful manual execution mattered a lot.

It also meant that much of engineering knowledge stayed private. A senior engineer knew which database pattern to use, which service was fragile, and which deployment step everyone forgot. A newer engineer learned those things by asking questions, reading code, and making mistakes.

The system worked, but it depended heavily on memory and access to the right people.

## The new job is built around cheap generation and expensive mistakes

GenAI changes the cost of producing a plausible implementation. It does not remove the cost of a bad decision.

An agent can create an API route in minutes. It can also forget authorization, copy an outdated error pattern, choose the wrong transaction boundary, or add a dependency the team does not want.

An agent can generate deployment configuration quickly. It can also expose a service publicly, choose an expensive default, or apply the right pattern to the wrong environment.

An agent can write fifty tests. It can also write fifty tests that prove its own misunderstanding.

So the main question is no longer, “Can you produce the code?”

It is, “Can you create the conditions in which the right code is likely to be produced, checked, and maintained?”

| After GenAI | What the team should expect |
|---|---|
| **Clarify the outcome** | Turn a vague request into a specific problem with boundaries |
| **Build reliable context** | Give the agent the relevant architecture, conventions, constraints, and current system state |
| **Define a contract** | State inputs, outputs, invariants, acceptance criteria, and what must not change |
| **Sequence the work** | Break large changes into small, testable waves with review gates between them |
| **Move quality checks earlier** | Add tests, linting, safety rules, and deployment checks before implementation spreads |
| **Review reasoning as well as code** | Check the research and plan before inspecting the generated diff |
| **Encode reusable knowledge** | Turn repeated patterns into instructions, templates, and automated checks |
| **Own the result** | Verify the outcome in practice rather than merely confirming that the agent completed its task |

The engineer still writes code when that is the best way to move forward. But typing is no longer the center of the role. Judgment is.

## Prompting is useful, but it is not a delivery process

Most people start using GenAI in the same way: open a chat, describe a task, inspect the answer, correct it, and repeat.

That works well for a small change.

For example, imagine a settings page with a mislabeled button. You can show the agent the component, ask for the wording change, run the tests, and finish. A formal planning process would add more work than value.

Now imagine a request to “add automatic refunds.”

That short sentence hides questions about payment state, partial refunds, duplicate requests, audit records, customer notifications, retries, permissions, and reporting. A more capable model does not remove those questions. It can simply make confident assumptions about them faster.

**This is where teams get into trouble. They use a prompt as if it were a specification, then discover the missing requirements inside a large generated change.**

The problem is not that the prompt was badly worded. The problem is that the work had no contract.

For non-trivial work, engineers should be expected to define at least:

- the outcome the change must produce;
- the parts of the system that are in scope;
- the behavior that must remain unchanged;
- the main interfaces and data flows;
- the acceptance criteria;
- the failure and rollback conditions;
- the work that is explicitly out of scope.

That may fit in an issue. It may need a longer design note. The size of the artifact should match the risk of the change.

**The point is not to write more documents. The point is to remove dangerous ambiguity before an agent turns it into code.**

## Real-world example one: the feature that was really an idempotency bug

Consider a team with a background process that sometimes creates duplicate customer notifications.

The obvious ticket says: “Replace the notification worker.”

Before GenAI, an engineer might spend several days reading the service, designing a replacement, and implementing it. The high cost of that work creates natural friction. People ask whether the rewrite is worth it because the rewrite is expensive.

After GenAI, an agent can scaffold a replacement quickly. That sounds helpful, but it removes some of the friction that used to force the team to think.

**A strong engineer does not begin by generating the new worker. They begin by investigating the failure.**

They find that the queue retries a message after a timeout, while the worker has no idempotency check. The message is processed twice even though the queue behaves as designed.

The engineer rewrites the work item:

- record a stable idempotency key;
- ignore a repeated message after the first successful action;
- test retries before and after the action completes;
- add a metric for suppressed duplicates;
- roll out the change to a small percentage of traffic first;
- do not replace the worker in this change.

The agent can now implement a narrow solution against a clear contract.

This is where the workflow starts to look like **spec-driven development**. In [Spec Bridge](https://bruj0.github.io/spec-bridge-docs/), research becomes a reviewable specification, the specification defines acceptance criteria and negative constraints, and implementation begins only after that contract is clear.

**The valuable skill was not producing the patch. It was refusing to automate the wrong plan.**

## Real-world example two: an endpoint is more than a handler

Suppose the team needs to let users turn email notifications on or off from the account settings page.

In the old model, an experienced engineer might remember to add request validation, permissions, an audit event, tests, API documentation, and monitoring. A newer engineer might learn those requirements during code review.

With GenAI, both engineers can produce a working handler quickly. The danger is that “working” may only mean that the happy path returns a success response.

**The new expectation is that the engineer treats the endpoint as a complete capability, not a code file.**

The work item should say:

- which users may update the preference;
- which values are valid;
- what happens when the record does not exist;
- whether the operation must be idempotent;
- which audit data must be stored;
- which existing behavior must remain unchanged;
- which tests prove the contract;
- which metric or log confirms the feature works after release.

The agent can generate the route, model, tests, and documentation. The engineer checks that the package satisfies the contract.

If this pattern appears often, the engineer should also improve the reusable endpoint instructions so the next implementation includes these elements by default.

That is a new part of the job: fix the output, then improve the system that produced it.

## The review moves upstream, but it does not disappear

One tempting conclusion is that teams should stop reviewing code and review only specifications.

That would be a mistake.

A good specification can still produce a flawed implementation. An agent may misunderstand a library, miss a race condition, or satisfy the written acceptance criteria while violating an unwritten operational constraint.

**The change is about where to spend the most attention.**

Before GenAI, the implementation often revealed the design because the design existed mainly in the engineer's head. Reviewers had to reconstruct the intent from the diff.

After GenAI, the team should review the intent before generation begins.

A practical review flow looks like this:

1. **Review the research.** Does it describe the current system accurately?
2. **Review the contract.** Are the scope, invariants, and acceptance criteria clear?
3. **Review the sequence.** Can each wave be tested before the next one starts?
4. **Review the generated change.** Did the implementation follow the agreed plan?
5. **Review the evidence.** Did the tests and production signals show the expected result?

The depth of review should follow the risk. A copy change does not need the same process as an authorization change or data migration.

**The goal is not ceremony. It is to catch mistakes at the cheapest point.**

## Engineers now maintain instructions as well as systems

Every team has rules that experienced people know but rarely write down.

“Never call that dependency inside a transaction.”

“Every background task needs a retry limit and a dead-letter path.”

“Changes to this table need a compatibility period because two application versions run during deployment.”

Before GenAI, code review often carried these rules. A reviewer saw a violation and left a comment.

That does not scale when agents can generate changes faster than reviewers can inspect them. The rules need to exist before generation.

Teams should keep persistent instructions close to the code. Those instructions should explain:

- the purpose and shape of the system;
- the approved patterns;
- naming and file placement rules;
- test expectations;
- error-handling conventions;
- security and operational constraints;
- which documentation must change with the code.

Engineers should be expected to maintain these instructions when reality changes.

If an agent repeatedly makes the same mistake, correcting each pull request is not enough. The team should update the instruction, template, test, or lint rule that allowed the mistake.

This is similar to fixing a production incident. Restarting the service is not enough. You must remove the condition that caused the failure.

## Working with agents becomes a team skill

When one engineer uses one agent for one task, coordination is simple.

When several people and agents work in parallel, normal delivery problems return in a new form: overlapping files, inconsistent assumptions, hidden dependencies, and unclear ownership.

**The answer is not a cleverer prompt. It is a clear team structure for how to use agentic AI and an equally clear agreement about how not to use it.**

That shared structure reduces friction because it stops every engineer from inventing a private workflow. People know what context an agent must receive, which checks must run, when another person reviews the plan, and where automation must stop.

Without those rules, the time saved during implementation often reappears as review work. An agent can generate a 2,000-line pull request in an afternoon. That does not mean a teammate can read 2,000 unfamiliar lines, reconstruct the design, check every assumption, and understand the operational impact in the same afternoon.

Expecting someone to “just review the diff” transfers the cognitive cost from the author to the reviewer. It also creates a bad choice: block the work because it is too large to understand, or approve it without the level of confidence the team normally expects.

A useful rule is: **if reviewers must reverse-engineer the intent from a large generated diff, the workflow failed before the pull request was opened.**

Teams should make both sides of the agreement explicit:

| Use agentic AI to | Do not use agentic AI to |
|---|---|
| Research a bounded part of the system and produce findings that an engineer validates | Explore the whole codebase, assume the findings are correct, and immediately implement them |
| Implement a reviewed specification in small, testable waves | Generate one 2,000-line pull request that mixes architecture, refactoring, dependencies, and product behavior |
| Apply established patterns through shared instructions and templates | Invent a new pattern silently because the generated code happens to pass tests |
| Run automated checks and prepare evidence for review | Treat green tests as proof that the requirement, design, and operational behavior are correct |
| Draft a plan that exposes assumptions and open questions | Hide uncertainty behind a polished explanation generated by the same agent |
| Automate reversible work inside defined permissions | Give an agent broad production access or allow it to merge high-risk changes without a human gate |

Being honest about the “do not” column matters. Not every task should be delegated. Not every generated change should be kept. Sometimes the right response is to reduce the scope, discard the output, ask a specialist, or write the critical part by hand.

Once those boundaries are shared, each work item can follow the same basic discipline: it needs a clear owner, defined scope, dependencies, and exit criteria. Large work should move in waves. Shared files need explicit ownership. A review gate should separate one wave from the next.

For example, a team might split a new reporting feature into four waves:

| Wave | Deliverable | Exit condition |
|---|---|---|
| **1. Safety** | Test fixtures, access rules, and validation pipeline | Invalid data and unauthorized access fail automatically |
| **2. Data** | Read model and query interface | Contract tests pass against realistic data |
| **3. Product** | User-facing report and export flow | Acceptance scenarios pass end to end |
| **4. Operations** | Monitoring, support notes, and rollout controls | Team can detect, pause, and diagnose failures |

Agents can work within each wave. Humans review the evidence before the next wave starts.

This looks less magical than asking a swarm to “build the feature.” It also works more often.

## What good looks like now

**The strongest engineers after GenAI are not the people who generate the most code. They are the people who make useful work easier to complete and harder to get wrong.**

You can see that in ordinary behavior.

A strong engineer:

- investigates before accepting the proposed solution;
- turns vague requests into clear, testable contracts;
- states what must remain unchanged as clearly as what should be added;
- gives agents focused context instead of dumping an entire repository into a chat;
- breaks large work into small delivery waves;
- puts automated quality gates in place early;
- checks generated output against the system, not against the agent's explanation of the system;
- updates shared instructions when a repeated mistake exposes a missing rule;
- leaves behind artifacts that another person or agent can use;
- follows the work into production and checks whether the intended outcome happened.

This does not require every engineer to become an architect with a grand strategy for every ticket.

It does require every engineer to take responsibility for the shape of the work as well as the code inside it.

## What should not change

Some parts of good engineering remain exactly the same.

Engineers still need curiosity. They still need to understand failure. They still need to care about users, operations, security, and maintainability. They still need to ask for help when the work crosses their expertise.

They also need technical depth. An engineer who cannot reason about code cannot reliably review generated code. GenAI can help someone cross an unfamiliar part of the stack, but it cannot make every decision equally safe.

The standard is not “use AI for everything.”

The standard is “use the best method for the work, and remain accountable for the result.”

Sometimes the fastest path is to ask an agent. Sometimes it is to write the change by hand. Sometimes it is to stop and talk to the person who understands the risk better.

Good judgment includes knowing the difference.

## The expectation is higher, not lower

GenAI does not reduce the need for engineers. It raises the standard for what engineering work should include.

If code is faster to produce, teams can no longer excuse unclear requirements, missing tests, undocumented constraints, or repeated manual work as easily as before. The tools make it cheaper to generate. They also make weak process more visible.

The engineer's value is not that they can type what the machine cannot.

It is that they can understand a messy situation, decide what matters, define a safe path, and check whether the result works in the real world.

Before GenAI, a good engineer could turn a plan into code.

After GenAI, a good engineer helps the team create better plans, execute them with agents, catch mistakes early, and improve the way the next change gets built.

That is a broader job.

It is also a more honest description of what good engineers were doing all along.

---

## About the author

**Rodrigo A. Diaz Leven** is a platform engineering lead focused on AI-assisted engineering, evaluation, and developer tooling.

If this essay sparked a question, disagreement, or an example from your own team, he would like to hear it:

- LinkedIn: <https://www.linkedin.com/in/rodrigoleven/>
- GitHub: <https://github.com/bruj0>
- Resume: <https://resume.bruj0.net/bruj0/rodrigo-leven>
