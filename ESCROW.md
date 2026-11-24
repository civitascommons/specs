# Escrow — money-phase custody rules (DRAFT)

**Status:** DRAFT v0.2 — published 2026-11-18 (City Hall, founder-approved as
draft). Revised per the Third Chair's blind review (H1–H4, M10).
**Not in effect.** This document takes effect only when the money phase starts,
and only after publication in the specs repo for at least 30 days. No escrow
exists before then. Closes tabletop gap G03 (as a draft, not as an activation).

---

## 0. Why this document exists before it is needed

The constitution says the founder never custodies citizens' money. It does not
say who does. This document answers that question — in draft, now — so that the
money phase never starts with the custody question unanswered. A city that
starts moving money before its custody rules are written is a city that will
write them during its first crisis, which is the worst time to write rules.

## 1. Who holds the money

**Never City Hall alone. Never the founder. Never the counterparty.**

Money in escrow is held by a **custodian** in one of two permitted forms:

- **(a) A licensed third-party escrow provider** meeting the criteria in §2.
- **(b) An on-chain smart contract** with published, independently audited
  code, meeting the criteria in §2.

The custodian is chosen by City Hall from a published shortlist, with the
reasons published. Citizens may propose custodians; City Hall accredits them
against §2 and publishes the accreditation. A custodian that stops meeting
the criteria loses accreditation, and no new escrows use it from that day.

**The arbiter.** Release authorization is 2-of-3: buyer, seller, and
**arbiter**. The arbiter is City Hall, acting as the office — not as a person,
and never as a custodian. City Hall holds one of the three keys. One key is
never a majority and never moves funds alone: City Hall cannot release funds
without the buyer or the seller, and cannot freeze funds by key power alone
(the freeze is a ledger record the custodian enforces, §6). City Hall's
arbiter key will be publicly disclosed at activation: the key's public part
and the office holding it will be listed in the specs repo before the first
escrow is funded. This is a change from the v0.1
wording ("City Hall never touches funds"): City Hall never *holds* funds and
never *moves* funds alone, but it does hold one of three release keys, and
the city says so in the open rather than hiding it.

## 2. Custodian criteria

A custodian must meet **all** of the following:

1. **No single-key control.** Funds move only on multi-party authorization
   (2-of-3: buyer, seller, arbiter — the arbiter is City Hall, §1) or on a
   time-locked contract whose release conditions are public and automatic.
   No one person, key, or server can move funds alone. A time-locked
   contract is permitted **only** if it includes a freezable condition: a
   contract that cannot be frozen cannot be a custodian (§6).
2. **Published security posture.** The custodian publishes how funds are
   stored, how keys are managed, and its incident history. A custodian that
   will not describe its security does not get accredited.
3. **Incident history is public.** Past breaches are disclosed, with what
   was lost, what was learned, and what changed. A custodian that hides a
   breach is disqualified permanently.
4. **Segregation.** Escrowed funds are segregated from the custodian's own
   operating funds at all times. The custodian's creditors have no claim on
   escrowed funds.
5. **Licensed or audited.** Form (a): holds the relevant escrow or
   money-transmitter license in its jurisdiction. Form (b): code audited by
   an independent firm, audit report public, and no unaudited upgrade path
   that could change the release conditions.
6. **Freeze enforcement.** The custodian must be able to execute a freeze
   on City Hall's signal: either a freeze function in the contract, or a
   binding cooperation obligation with a stated response time. The freeze
   signal is a `dispute` record on the ledger (§6); the custodian reads the
   ledger directly and freezes the named escrow within 24 hours of the
   record's appearance. A custodian that cannot freeze cannot be a
   custodian — a freeze that depends on the custodian's goodwill is not a
   freeze.
7. **Ledger-hardening gate.** The money phase does not start until the
   ledger's anchoring or signed events are live (LEDGER.md §5). Release
   authorization rests on the ledger's acceptance record, and the
   founding-era ledger — single writer, rewritable Git history — is not a
   strong enough foundation for money. This gate is part of the custodian
   criteria because the custodian reads the ledger to release (§3.4); a
   custodian reading an unhardened ledger is a custodian trusting a single
   writer's account security.

**If no custodian meets these criteria, the money phase does not start.**
This is a gate, not a preference. The city would rather stay in the
mutual-aid phase than move money through a custodian it cannot vouch for.

## 3. The escrow flow

1. **Fund.** The buyer deposits the task amount with the custodian. City
   Hall verifies the deposit **directly with the custodian** — never on the
   buyer's word. A deposit the custodian cannot confirm is not a deposit,
   and no `escrow` record is written. The confirmed deposit is recorded on
   the ledger as an `escrow` record (amount, parties, custodian, task id).
   The seller sees the funded record before starting work.
2. **Deliver.** The seller delivers against the task card's acceptance
   criteria.
3. **Accept.** The buyer accepts (or the acceptance window in §4 expires
   and deemed acceptance applies). Acceptance is verified against the
   deliverable, per LEDGER.md §4 — the check reads back the real state.
4. **Release.** On acceptance, the custodian releases the funds to the
   seller, minus the escrow fee (§5). The custodian **reads the ledger
   directly and verifies the hash chain** to confirm the acceptance record;
   it does not accept a release message from City Hall, the buyer, or
   anyone else. The release is recorded on the ledger.

At no step does City Hall hold the funds. City Hall's role is the record,
one of three release keys, and, in dispute, the ruling — never the money.

## 4. Release authorization

A release happens only when **both** of the following are true:

- The ledger holds an `exchange` record for the task with
  `acceptance: passed` — written on the buyer's confirmation, or on City
  Hall's deemed acceptance under LEDGER.md §4.
- The buyer has accepted, or the acceptance window has expired without a
  dispute filing.

**The acceptance window is 3 working days from delivery.** This document
defines the window itself, adopting the tabletop's G05 suggestion
(3 working days, expiry without acceptance → deemed acceptance, per
LEDGER.md §4). The window is defined here because no exchange rule defines
it yet; when the G05 amendment lands, it must match this definition or this
document is amended to match it.

**A release instruction arriving by any other channel is ignored.** An email,
a message, or a task-card comment claiming to be the buyer and asking for
release — or for a change of destination — is not a release authorization,
even if it comes from the buyer's account. Accounts get compromised; the
ledger record plus the acceptance window is the authorization, and nothing
else is. This rule exists because the most common way to steal escrowed funds
is not to break the custodian but to impersonate a party.

## 5. Fees

- The escrow fee is published per custodian and capped by the constitution's
  settlement levy (≤ 2% of the settled amount, all-in). The fee is split
  between the custodian and the city by a published schedule; the city's
  share funds the ledger and dispute operations.
- No fee is charged on a release that never happens (a cancelled or
  refunded escrow returns the full deposit).

## 6. Disputes and freezes

**Freeze scope.** A dispute filing freezes the escrow **only when the
dispute falls within City Hall's adjudication jurisdiction** under
DISPUTES.md: fraud or deception, false reviews, mistaken ledger entries, and
application rejections. A dispute outside that jurisdiction — quality,
price, or any matter DISPUTES.md lists as "not covered at all" — does **not**
freeze the escrow. The filing party is told immediately that the matter is
not covered and that the escrow is unaffected. This is the fix for the
v0.1 trap: a quality complaint must never be able to freeze money City Hall
has no power to rule on.

**Not-covered determination is a release trigger.** If a filing is found
not covered after a freeze was applied in error (or while jurisdiction was
being determined), the not-covered determination itself unfreezes the
escrow, and the escrow then follows the verified delivery state: delivered
and meeting the acceptance criteria → release to the seller; not delivered
→ refund to the buyer. The determination and the resulting release or refund
are both recorded on the ledger.

**On a ruling, for a frozen escrow:**

- On a ruling for the buyer: the deposit returns to the buyer in full.
- On a ruling for the seller: the deposit releases to the seller, minus the
  escrow fee (§5).
- On a split ruling: the ruling states the split, and the custodian
  executes it. The ruling and the executed split are both recorded on the
  ledger.

**Expiry without a ruling.** If the dispute timeline expires without a
ruling, the escrow follows the **verified delivery state**, not an automatic
refund: delivered and meeting the acceptance criteria → release to the
seller; not delivered → refund to the buyer; delivery state unverifiable →
the deposit stays frozen and the failure is recorded on the ledger against
City Hall, until the state is verified. An automatic refund to the buyer
would hand a free deliverable to any buyer who files and waits.

**Seller remedy for buyer non-acceptance.** If the buyer does not accept
within the acceptance window and files no dispute, the seller may ask City
Hall to verify the delivery. City Hall checks the deliverable against the
task card's acceptance criteria: if it meets them, City Hall writes a deemed
acceptance record (LEDGER.md §4) and the escrow releases; if it does not,
City Hall records the deficiency and the escrow refunds to the buyer. A
buyer's silence is not a veto over the seller's payment. (This requires a
DISPUTES.md amendment adding "buyer non-acceptance" to the adjudicable
matters — see §9.)

**Freeze duration.** A freeze lasts at most 30 days from filing, including
any appeal. If the timeline expires without a ruling, §6's expiry rule
above applies. The 30-day cap is stated here because DISPUTES.md's appeal
re-review has no deadline of its own.

## 7. Breach

The custodian is the target. This section plans for its failure, not just
its success.

- **On a suspected breach of a custodian:** all pending escrows with that
  custodian freeze immediately. City Hall publishes the incident (what is
  known, what is not, what is frozen) within one working day. No new escrows
  use that custodian until it is re-accredited under §2.
- **On a confirmed loss:** the custodian's segregation (§2.4) and insurance
  or reserve, where applicable, are the first recourse. The city does not
  guarantee custodians' solvency and does not make affected parties whole
  from city funds — the constitution's risk principle (risk is private)
  applies. What the city guarantees is the record: every frozen escrow, every
  loss, and every recovery is on the ledger, in the open.
- **On a custodian's failure to cooperate** (refusing a freeze, refusing a
  ruling's execution): the custodian is disqualified permanently, and City
  Hall publishes the full correspondence.

## 8. Launch limits

The money phase opens with a **per-transaction cap**, set by City Hall at
activation and published with the activation notice. The cap starts low and
rises only after a published number of clean settlements (funded, delivered,
accepted, released, no dispute). The cap exists because the first real-money
escrows are the ones most likely to be attacked — a new custodian, a new
flow, and an attacker who has read the same public specs.

## 9. What this document does not do

- It does not choose a custodian. That choice is made at activation, from
  the criteria in §2, in the open.
- It does not set the per-transaction cap or the fee split. Those are set
  at activation and published with it.
- It does not promise that escrowed funds cannot be stolen. It promises
  that custody is segregated, multi-party, and on the record — and that a
  theft, if it happens, is visible, frozen, and attributable. The guarantee
  is the record, not the vault.
- **Cross-dependency (satisfied 2026-11-25):** §6's seller remedy requires
  a DISPUTES.md amendment adding "buyer non-acceptance" to the adjudicable
  matters, and a defined appeal deadline. Both are now in place: DISPUTES.md
  v0.4 lists bad-faith refusal to accept (Charter VI), and the appeal
  deadline is seven days from the decision.

— City Hall (draft v0.2)
