# Assignment 3

## 1. AI Augmented Concept

### Original Concept

#### 1.1 EventDirectory [User, Event]
**Purpose.** Define events (or recurring chore periods) and maintain membership and roles such as Organizer or DutyMember.

**State.**
- `Events`: { id: Event, title: String, startsAt: Time, endsAt: Time, active: Flag }
- `Memberships`: { event: Event, user: User, role: Enum{Organizer, DutyMember} }

**Actions.**
- `createEvent(creator: User, title: String, startsAt: Time, endsAt: Time)`
- `invite(event: Event, inviter: User, invitee: User, role: Role)`
- `setActive(event: Event, setter: User, flag: Flag)`

#### 1.2 DutyRoster [User, Event]
**Purpose.** Represent all duties and assignments. Duties move through Open → Assigned → Done.

**State.**
- `Duties`: { id: Duty, event: Event, title: String, dueAt: Time, status: Enum{Open, Assigned, Done}, assignee: User? }

**Actions.**
- `addDuty(event: Event, actor: User, title: String, dueAt: Time)`
- `assignDuty(duty: Duty, actor: User, assignee: User)`
- `markDone(duty: Duty, actor: User)`
- `reOpen(duty: Duty, actor: User)`

#### 1.3 RotationGroups [User, Event]
**Purpose.** Save reusable groups of members and standard duties for easy reapplication.

**State.**
- `Templates`: { id: Template, title: String, members: Set<User>, standardDuties: Set<String> }

**Actions.**
- `createTemplate(owner: User, title: String, members: Set<User>, standardDuties: Set<String>)`
- `applyTemplate(template: Template, event: Event, actor: User)`

#### 1.4 Notify [User]
**Purpose.** Deliver notifications when duties are assigned or due soon.

**State.**
- `Notifications`: { id: Notification, recipient: User, subject: String, body: String, createdAt: Time, unread: Flag }

**Actions.**
- `notify(recipient: User, subject: String, body: String)`
- `markRead(notification: Notification, user: User)`

#### 1.5 Syncs (original)
- **Assignment Notification:** when `Duty d` with `d.status = Assigned`, where `u = d.assignee`, then `notify(u, "New duty assigned", d.title + " due " + d.dueAt)`.
- **Due Soon Reminder:** when `Duty d` with `d.status = Assigned` and `now + 24h ≥ d.dueAt`, where `u = d.assignee`, then `notify(u, "Duty due soon", d.title + " is due by " + d.dueAt)`.
- **Template Application:** when `Template t` is applied to `Event e`, where `(u ∈ t.members)`, then `addDuty(e, actor, dutyName, dueAt)` for each `dutyName ∈ t.standardDuties`.

---

### AI Augmented Concept

*(Adds AI‑powered intake, drafting, explanation, and validation while preserving all original behavior.)*

#### 2.1 Extended/New State
- `AssignmentDraft`: {
  id: Draft,
  event: Event,
  proposedAt: Time,
  proposedBy: User | System("LLM"),
  rationale: String,                  // human‑readable explanation from LLM
  objective: Enum{Fairness, MinConflicts, MinFatigue, Custom},
  constraints: Json,                  // availability, max per person, disallowed pairs, history
  assignments: Set<{ duty: DutyRef | {title, dueAt}, assignee: User }>,
  confidence: Float in [0,1],
  model: String,                      // e.g., gpt‑4o‑mini, o3, etc.
  promptHash: String                  // for audit/repro
}

- `DutyParseLog`: {
  id: ParseLog,
  event: Event,
  inputText: String,                  // user’s natural‑language plan
  parsedDuties: Set<{ title, dueAt?, duration?, location? }>,
  parserNotes: String,
  confidence: Float,
  model: String,
  createdAt: Time
}

#### 2.2 New/Extended Actions (Requires/Effects)
**A) Natural‑Language Duty Intake**
- `parseDutyPlan(event: Event, actor: User, nlPlan: String) -> ParseLog`
  - **Requires:** `actor` is Organizer for `event`.
  - **Effect:** Calls LLM to transform `nlPlan` into structured duties; persists `DutyParseLog`.

- `instantiateParsedDuties(parseLog: ParseLog, actor: User) -> Set<Duty>`
  - **Requires:** Organizer.
  - **Effect:** Creates `Duty` rows for each entry in `parseLog.parsedDuties` via `addDuty`.

**B) AI Assignment Drafting**
- `proposeAssignments(event: Event, actor: User, objective: Objective, constraints?: Json) -> AssignmentDraft`
  - **Requires:** Organizer; event has duties & members.
  - **Effect:** Calls LLM to produce a proposed mapping duties→assignees with `rationale` and `confidence`; stores `AssignmentDraft`.

- `explainAssignment(draft: Draft, requester: User) -> String`
  - **Effect:** Returns LLM‑generated explanation (fairness math, conflicts avoided, tie‑breakers) for transparency.

- `validateDraft(draft: Draft) -> { ok: Bool, violations: Set<String> }`
  - **Effect:** Deterministic validator (non‑LLM). Checks membership, duplicates, max‑load, time conflicts.

- `applyDraft(draft: Draft, actor: User) -> Set<Duty>`
  - **Requires:** Organizer; `validateDraft(draft).ok` is true.
  - **Effect:** Executes `assignDuty` per draft; triggers existing Notify syncs.

**C) Optional Swap‑Assist**
- `suggestSwap(event: Event, requester: User, duty: Duty, reason: String) -> Set<{counterparty: User, swapWith: Duty, message: String}>`
  - **Effect:** LLM suggests fair swaps (same workload/time window); organizer approves; then `assignDuty` edits.

#### 2.3 Operational Principles
1. **Human‑in‑the‑loop:** AI proposes; organizers confirm (`instantiateParsedDuties`, `applyDraft`).
2. **Deterministic validation before side‑effects:** `validateDraft` must pass before assignments change.
3. **Auditability:** Store `promptHash`, `model`, `confidence`, `rationale` with each draft; enable rollback and review.
4. **Privacy & Minimality:** Avoid unnecessary PII in prompts; prefer stable member IDs.
5. **Graceful Degradation:** Original manual actions remain fully usable without AI.

#### 2.4 Syncs (Extended)
- **On Draft Applied:** For each assigned duty, fire the original **Assignment Notification** sync.
- **On ParseLog Instantiated:** Newly created duties become eligible for the original **Due Soon Reminder**.



## 2. UX Sketches and Journey
![UI Sketch](uxsketchassignment3.png)


## 3. Implemenation in code files

## 4. Test Scenarios & Prompt Iteration

Below are **3 full scenarios**, each with a sequence of actions that culminates in at least one LLM-based step. For each, we show the **prompt variant** and discuss **what worked**, **what went wrong**, and **what remains**.

> The console output in your run (pasted in our conversation) demonstrates the end-to-end behavior for these scenarios.

### Scenario 1 — Parse → Draft → Validate → Apply (Weekly chores)

**Sequence**
1. Organizer adds members: Ana, Ben, Carla, Diego.  
2. Organizer provides a text plan:  
   `Mon: trash after 6pm; Tue: kitchen before 7pm; Wed 8–9pm bathrooms; Sun 5pm common room vacuum.`  
3. `parseDutyPlan` calls LLM → `DutyParseLog`.  
4. `instantiateParsedDuties` creates 4 duties.  
5. `proposeAssignments("Fairness", { maxPerPerson: 2, unavailable: { Ana: ["Wed"] } })` calls LLM.  
6. `validateDraft` checks constraints.  
7. `applyDraft` assigns if validation passes.  
8. `explainAssignment` summarizes fairness.

**Prompt variant v1 (baseline drafting)**  
Hard constraints embedded in SYSTEM prompt:
```
Return ONLY JSON: { "assignments":[{"dutyId":string,"assignee":string}], "rationale":string, "confidence":0..1 }.
Assign only to these members: ["Ana","Ben","Carla","Diego"].
Use only these duty IDs: ["duty_…","duty_…",…].
Do not duplicate the same dutyId.
If constraints cannot be satisfied, return an empty assignments[].
Unavailability (hard): { "Ana": ["Wed"] } — do NOT assign members to dueAt strings containing their unavailable tokens.
```

**What worked:** Even distribution (1 duty each), unavailability respected; validation OK.  
**What went wrong:** Parse sometimes drops the exact time into `parserNotes` instead of `dueAt`.  
**Mitigation:** We deliberately keep parsing conservative and permit missing `dueAt`; organizer can edit after instantiation.  
**Remaining issue:** If organizers need structured times, we’ll add a second pass that parses times into a normalized field.

---

### Scenario 2 — Mixed manual + AI (critical duty pre-assigned)

**Sequence**
1. Members: Ana, Ben, Carla.  
2. Add duties for an event night.  
3. Manually assign a critical duty `Bar setup → Ben`.  
4. Call `proposeAssignments("MinConflicts", { maxPerPerson: 2 })`.  
5. Validate & apply (should avoid overwriting manual assignment).  

**Prompt variant v2 (overwrite guard + even load)**  
We emphasize: **use only provided duty IDs**, **even load**, and **do not duplicate duty IDs**. Overwrite protection is **enforced in validators**, not prompt, to guarantee it never silently happens.

**What worked:** LLM assigned remaining duties; validator guaranteed no overwrite of the manually assigned task.  
**What went wrong:** LLM can still “recommend” changing the already assigned duty in the JSON.  
**Mitigation:** Validator rejects overwrites unless `allowReassignAssigned: true`.  
**Remaining issue:** If planners *do* want occasional reassign, they can add the flag and rely on the validator to allow it.

---

### Scenario 3 — Stress failures (non-member, duplicates, done & availability)

**Sequence A (Over-assignment + Non-member + Duplicate):**  
- Members: Ana, Ben, Carla; create 3 duties.  
- Inject a *bad* LLM JSON (via FakeLLM) with duplicate duty IDs, a non-member assignee, and exceeding `maxPerPerson=1`.  
- `validateDraft` emits multiple violations and blocks apply.

**Sequence B (Availability + Done reassignment):**  
- Mark one duty `Done`.  
- Inject LLM JSON that tries to reassign the Done duty and assigns Ana on a “Wed” dueAt, while she’s unavailable for `"Wed"`.
- Validator blocks both with explicit, actionable errors.

**Sequence C (Parse hallucination):**  
- Provide a plan that **does not** include “Security Patrol”.  
- Inject LLM parsed list that **hallucinates** “Security Patrol”.  
- `instantiateParsedDuties` uses a soft allowlist to drop unknown categories; only legitimate duties are created.

**Prompt variant v3 (empty-output-on-violation + allowlists)**  
We augmented the SYSTEM prompt with:
```
If you cannot satisfy constraints, DO NOT invent members/duties; return an empty assignments[].
Use only these member names and duty IDs (allowlist).
Do not duplicate the same dutyId.
```

**What worked:** Our validators catch the most realistic failure modes and provide clear messages.  
**What went wrong:** Without the allowlist, some models occasionally propose a new duty or a nickname for a member.  
**Mitigation:** Allowlist + “empty-output-on-violation” instruction in the prompt, and **hard** validator checks.  
**Remaining issue:** Nickname mapping (e.g., “Benji” vs “Ben”) remains an application-level policy choice; we currently require **exact** matches for safety.

---

## 5. Validators & Guardrails

We implemented validators that catch realistic LLM mistakes and fail with **actionable** messages. Key checks (enforced in `validateDraft`):

1. **Membership & Referencing**  
   - **Issue:** Assigning to a non-member; referencing unknown duty IDs.  
   - **Validator:** Confirm `assignee ∈ members`; confirm `dutyId` exists and is not `Done`.  
   - **Message example:** `Unknown member: Evan` / `Unknown duty id: duty_xyz`.

2. **Overwrite & Duplicates**  
   - **Issue:** Overwriting an already `Assigned` duty or including the **same dutyId twice** in a single draft.  
   - **Validator:** Reject overwrites unless `allowReassignAssigned: true`; enforce single dutyId per draft.  
   - **Message example:** `Attempt to overwrite existing assignment for "Kitchen" …` / `Duty duty_abc appears multiple times in the draft.`

3. **Capacity & Availability**  
   - **Issue:** Over-assigning a single person or violating availability.  
   - **Validator:** Enforce `maxPerPerson`; token-match user unavailability against `dueAt` (e.g., `"Wed"` match on `"Wed 8–9pm"`).  
   - **Message example:** `Ana exceeds maxPerPerson (2 > 1).` / `Ana is unavailable for "Bathrooms" (dueAt: Wed 8–9pm, token: "Wed").`

**Additional guardrails**  
- **Idempotent apply:** `applyDraft` refuses to apply the same `promptHash` twice.  
- **Conservative parsing:** Drop empty titles; optional allowlist on `instantiateParsedDuties` to reduce hallucinations.  
- **Timeouts & retries:** All LLM calls run under a timeout with exponential backoff (`withRetryAndTimeout`).  
- **No secrets in repo:** `config.json` is git-ignored.

---
