# Swiss AI Weeks Hackathon Challenge: AI-Powered Ticket Triage

Welcome! This challenge asks you to build an AI system that can triage Jira
service desk tickets for a pan-European asset management company the way an
experienced L2 service agent would — figuring out what a ticket actually is,
who should own it, how urgent it really is, and (where possible) how it was
probably resolved.

## What you get

### 1. Training data — `jira_first_20000_requested_fields_synthetic.json`
20,000 synthesized tickets modeled on real Jira Service Management data from
an asset manager operating across Switzerland, France, Luxembourg, Germany,
and the Nordics. Use this to learn patterns in how tickets map to services,
teams, assignees, priorities, and resolutions.

Important realities baked into this data — don't assume it's clean:
- **Not every ticket is properly triaged.** Some tickets show the *wrong*
  "Affected Business or IT Service" or a generic bucket, on purpose — just
  like in real life, when the first person or system that raises a ticket
  guesses wrong.
- **Not every ticket is properly resolved.** Some are fully documented, some
  just say "fixed" with no useful detail, and some have no resolution
  recorded at all — even though a similar past ticket elsewhere in the
  dataset might reveal how that class of problem is normally solved.
- **Comments are inconsistent.** Some tickets have a rich investigation
  trail, some have one or two lines, some have none.
- **Priority/Urgency/Impact are random here.** In this training set these
  three fields are drawn independently of each other and of the ticket
  content. Do not expect them to be internally consistent or to reflect
  real severity in the training data — that matters for the challenge set
  below.

### 2. The challenge file — `jira_hackathon_20_new_tickets_challenge.json`
20 brand-new tickets you need to triage. Some fields are deliberately
missing, blank, or wrong on purpose — that's the point of the exercise.

### 3. Two tools to help you get Priority right

**a) The Urgency / Impact → Priority matrix**

Unlike the training data, the challenge set's `Priority` is **not random** —
it is fully determined by `Urgency` and `Impact` according to the table
below. If you can correctly assess Urgency and Impact from the ticket
content, Priority follows deterministically:

### Incident Priority Calculation Matrix

|➡️ Impact \ <br>⬇️ Urgency | **Major / Widespread**<br><sub>Full unavailability to critical IT services supporting key operations (> 2 hrs downtime)</sub> | **Significant / Large**<br><sub>Partial unavailability of critical IT services, 1+ business entities affected, or financial counterparts affected</sub> | **Moderate / Limited**<br><sub>Full unavailability of non-critical IT services, or up to 1 business entity affected</sub> | **Minor / Localized**<br><sub>Partial unavailability of non-critical IT services, or individuals affected</sub> | **No direct impact / Information**<br><sub>No direct operational impact; informational/maintenance without service degradation</sub> |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Critical**<br><sub>Immediate action required (prevent/fix regulatory breach, security compromise, or major outage). No workaround available.</sub> | **Highest** | **Highest** | **High** | **Medium** | **Medium** |
| **High**<br><sub>Rapid resolution needed within hours to avoid escalation. Workaround available but difficult/time-consuming.</sub> | **Highest** | **High** | **High** | **Medium** | **Low** |
| **Medium**<br><sub>Important to fix soon; no immediate operational/regulatory threat. Easy workaround available.</sub> | **High** | **High** | **Medium** | **Low** | **Low** |
| **Low**<br><sub>Handled in normal workflow without urgent escalation.</sub> | **Medium** | **Medium** | **Low** | **Low** | **Lowest** |
| **Lowest**<br><sub>Routine/informational with no effect on operations or compliance.</sub> | **Medium** | **Low** | **Low** | **Lowest** | **Lowest** |

**b) The critical service list**

Not all services carry the same business weight. Use this list — rated for
a pan-European asset manager — to help judge Urgency and Impact realistically
(a "Critical" service failing tends to justify higher Urgency/Impact than the
same failure on a "Non-Critical" service):

| Service | Rating |
| :--- | :--- |
| Trading Platform | Critical |
| Order Management | Critical |
| Trade Matching | Critical |
| Securities Settlement | Critical |
| Corporate Actions | Critical |
| Fund Pricing | Critical |
| NAV Calculation | Critical |
| Portfolio Accounting | Critical |
| Cash Management | Critical |
| Risk & Compliance Monitoring | Critical |
| Regulatory Reporting | Critical |
| SimCorp Dimension | Critical |
| Rimes Data Feed | Critical |
| Client Reporting | Critical |
| Tax Reporting | Non-Critical |
| CRM & Client Portal | Non-Critical |
| Identity & Access Management | Non-Critical |
| SharePoint & File Storage | Non-Critical |
| Outlook & Email | Non-Critical |
| Emailed Support Tickets | Non-Critical |

## Your task

For each of the 20 tickets in the challenge file, determine the correct
value for:

1. **Work type** — is this really an `Incident` or a `Service Request`?
   Titles can be misleading on purpose.
2. **Affected Business or IT Services** — which service is actually
   impacted? Don't trust the value shown in the challenge file at face
   value.
3. **Service Team(s)** — which team owns that service?
4. **Assignee** — who should this be routed to?
5. **Priority** — derive it correctly from Urgency and Impact using the
   matrix above (Urgency and Impact themselves are optional to correct —
   partial credit for improving them, full credit requires Priority to be
   internally consistent with whatever Urgency/Impact you land on).
6. **Resolution** — what should the resolution status be? Use the same
   vocabulary as the training data: `done`, `cancelled`, `clarification`, or
   `cannot reproduce`.
7. **Resolution text**, written as a comment — a realistic, concrete
   resolution note, in the voice of the assigned agent, similar in style to
   the comments already present in the training data. Where the challenge
   ticket itself doesn't contain enough information to resolve it, look for
   a similar historical ticket in the 20k training set and use its
   resolution pattern as the basis for yours.

## How you'll be judged

Your output will be compared against a hidden reference answer set. Scoring
rewards:
- Correct final Service / Team / Assignee routing (not just "a" plausible
  answer — the *actual* one implied by the ticket content).
- Priority values that are mathematically consistent with the matrix above.
- Resolution comments that are specific and plausible, not generic
  filler like "issue fixed."

## Rules

- Do not hardcode answers to the 20 specific challenge tickets by any means
  (manual lookup, another team's output, previous runs of the same fixed
  file, etc.) — the challenge tickets are freshly generated and not known in
  advance to anyone, including the organizers, before the event starts.
- You may use any model, framework, or retrieval approach you like against
  the training data.
- Good luck, and have fun digging through Rimes feed delays, NAV tolerance
  breaches, and mysteriously vague "problem fixed" comments!
