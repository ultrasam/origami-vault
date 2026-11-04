# 03_ATTRIBUTION_DOMAIN.md

## Origami Vault Attribution Domain

**Version:** 1.1

**Status:** Frozen

- Conceptual Entity Pass: Completed
- Final Consistency Check (Draft 1.1): Completed; see [Review Corrections](#review-corrections)

---

# Purpose

The Attribution Domain records who deserves recognition for origami knowledge and in what capacity.

It allows designers, diagrammers, translators, researchers, publishers, and other contributors to be credited, including people who never use Origami Vault, while keeping uncertainty and conflicting attributions visible.

---

# Scope

The Attribution Domain answers:

- Which person or organization is credited?
- For what work, edition, or artifact?
- In which contribution role?
- Is the creator identified, unknown, anonymous, or part of a tradition?
- Which attribution claims exist, and what is their review status?
- Which user account, if any, represents a party?

The Attribution Domain does not own:

- Works, editions, or titles.
- Sources, evidence, or curation decisions.
- Licenses or permissions.
- Digital files.
- User accounts or authentication.

Those concepts are owned outside Attribution and may be referenced by it.

---

# Domain Ownership

The Attribution Domain owns:

- `Party`
- `PartyName`
- `Credit`
- `PartyUserLink`

The Attribution Domain may also own this future entity:

- `PartyRelationship`

The `Person`, `Organization`, and `CreditRole` concepts from the FOLD research are represented by `party_type` and `contribution_role` rather than separate entities.

---

# Core Conceptual Decisions

## Credit Is a Claim of Recognition

`Credit` connects a party to a work, edition, or artifact through a contribution role. It is a domain-owned claim, as described in the Provenance Domain.

Direct entry by an authorized contributor creates a credit with the status Claimed. Acceptance does not mean verification.

## Party and User Are Independent

Following Principle 9:

```text
User ≠ Party
```

A historical designer may exist only as a `Party`. A community participant may exist only as a user. A participating designer may be both, connected by a `PartyUserLink`.

## Uploading Does Not Earn Credit

The person who uploads a file is a provenance actor. They receive credit only when a credit says so.

```text
Uploader ≠ Designer
```

## Unknown and Traditional Are Valid Attributions

Absence of a named creator is itself knowledge. A credit may state that the creator is unknown, anonymous, or part of a folding tradition, without inventing a party.

## Conflicting Attributions Coexist

Following Principle 17, several credits may make competing statements about the same work. A curated conclusion changes their statuses and does not delete them.

## Evidence Belongs to Provenance

An attribution source is recorded through the `Source` and `EvidenceLink` entities of the Provenance Domain. Attribution does not keep a second evidence model.

## Contributors Are Not Asked to Understand the Model

Following Principle 18, each field is automatic, optional, or clearly worth asking a contributor for. See [Contributor Experience](#contributor-experience).

---

# Implementation Support Fields

The following fields are implementation support fields rather than purely conceptual attributes. They are listed separately for each entity that uses them.

- `created_at`: time the record was created.
- `updated_at`: time the record was last updated.

---

# Entity: Party

## Definition

A person or organization that can receive credit.

## Identity Boundary

`Party` answers:

```text
Who can be recognized?
```

It does not answer:

```text
Who has an account?
Who performed this upload?
```

## Conceptual Attributes

- `id`
- `party_type`
- `description`
- `status`
- `merged_into_party_id`

Names are resolved through `PartyName`.

## Implementation Support Fields

- `created_at`
- `updated_at`

## Controlled Party Types

- Person
- Organization
- Unknown

An Unknown party type records a named party whose nature is not known.

## Lifecycle Status

- **Active:** available for credit.
- **Deprecated:** preserved, but no longer recommended because it is erroneous or superseded.
- **Merged:** preserved, with its identity resolving to the surviving party.

## Relationships

```text
Party
  1 ─── 1..* PartyName
  1 ─── 0..* Credit
  1 ─── 0..* PartyUserLink (at most one current)
  * ─── 0..1 Party (merged into)
```

## Integrity Rules

- A party must have at least one `PartyName`.
- A party does not require a user account.
- Matching names must not automatically merge two parties.
- `merged_into_party_id` is required when status is Merged and must otherwise be absent.
- Merge references must not form a cycle.
- Personal details are limited to what recognition requires. Date of birth, contact details, and similar personal data are not part of `Party`.

---

# Entity: PartyName

## Definition

A name by which one party is known.

## Purpose

`PartyName` allows a designer's native-script name, romanized names, and alternate names to refer to one party.

## Conceptual Attributes

- `id`
- `party_id`
- `name`
- `language`
- `name_type`
- `preferred`

`language` is a single BCP 47 tag. An unknown language is recorded with `und`.

## Implementation Support Fields

- `created_at`
- `updated_at`

## Controlled Name Types

- Primary
- Alternative
- Historical
- Romanized
- Native Script
- Pseudonym

The vocabulary may be extended through controlled records rather than arbitrary free text.

## Relationship

```text
Party
  1 ─── 1..* PartyName
```

## Integrity Rules

- Every name belongs to exactly one party.
- At most one `PartyName` may be preferred for the same party and language.
- At most one `PartyName` of a party may have the `Primary` name type.
- Names should be preserved rather than overwritten.
- Matching name text does not prove that two records represent the same party.

---

# Entity: Credit

## Definition

A claim that a party, or a stated absence of a named party, holds a contribution role for one work, edition, or artifact.

## Identity Boundary

`Credit` answers:

```text
Who is recognized for this, and in what capacity?
```

## Conceptual Attributes

- `id`
- `target_reference`
- `contribution_role`
- `attribution_state`
- `party_id`
- `credited_as`
- `credit_status`
- `confidence`
- `notes`

`target_reference` identifies an `OrigamiWork`, an `OrigamiEdition`, or a `DigitalArtifact`.

`credited_as` optionally preserves the name as printed in a source, when it differs from the party's preferred name.

## Implementation Support Fields

- `created_at`
- `updated_at`

## Controlled Contribution Roles

- Original Designer
- Collaborator
- Diagram Creator
- Translator
- Editor
- Publisher
- Photographer
- Researcher
- Contributor

The vocabulary may be extended through controlled records rather than arbitrary free text.

## Controlled Attribution States

- **Identified:** a named party holds the role.
- **Unknown:** the holder of the role is not known.
- **Anonymous:** the holder is known or knowable, but their identity is withheld.
- **Traditional:** the design belongs to a folding tradition with no individual creator on record.

## Controlled Credit Status

- **Claimed:** recorded from a source or contributor and not yet reviewed.
- **Unverified:** reviewed, but the available evidence is insufficient to confirm or reject the credit.
- **Verified:** reviewed and confirmed by the available evidence.
- **Disputed:** reviewed and subject to credible conflicting evidence.
- **Deprecated:** retired from current use and preserved as a historical record.

These values match `WorkRelationship.relationship_status`.

## Controlled Confidence

- High
- Medium
- Low

`confidence` is optional. It is a person's assessment of how likely the attribution is, such as for a historical attribution. A tool's score must not set it.

## Relationships

```text
Party
  1 ─── 0..* Credit

Credit
  * ─── 0..1 Party
```

## Integrity Rules

- `party_id` is required when `attribution_state` is Identified, and must be absent when it is Unknown or Traditional.
- When `attribution_state` is Anonymous, `party_id` may be retained under restricted access. It must not be displayed.
- The Traditional state applies only to the Original Designer role on a work.
- Original Designer applies to a work. Diagram Creator, Translator, Editor, and Publisher apply to an edition or artifact. Photographer applies to an artifact.
- Several parties may hold the same role on the same target, such as two original designers.
- Contribution role must be controlled.
- Credit status must be controlled.
- Several credits may make conflicting statements about the same target.
- The same party, role, and target should not be recorded twice. Further support is added as evidence.
- Status changes follow a `CurationDecision`, as defined in the Provenance Domain.
- Accepting an imported author claim creates a Claimed credit, unless the curator also records a review decision.
- A person must not verify a credit that they asserted about themselves.
- Confidence does not set or change credit status. A Verified credit must not have Low confidence.
- Missing credit must not invalidate a work. A missing credit means that nothing has been recorded, which is different from an Unknown credit.

## Display

All credits that are not Deprecated remain available. Their status is shown, so a disputed or unverified attribution is not presented as settled fact.

---

# Entity: PartyUserLink

## Definition

A statement that a user account represents a party.

## Purpose

`PartyUserLink` connects a participating creator's account to their recognized identity, while protecting real people from impersonation.

## Conceptual Attributes

- `id`
- `party_id`
- `user_id`
- `link_status`

## Implementation Support Fields

- `created_at`
- `updated_at`

## Controlled Link Status

- **Claimed:** asserted by the user and not yet reviewed.
- **Verified:** reviewed and confirmed.
- **Disputed:** subject to credible conflicting evidence.
- **Ended:** no longer current, and preserved as a historical record.

## Relationship

```text
Party
  1 ─── 0..* PartyUserLink (at most one current)
```

## Integrity Rules

- A party has at most one current link, and a user has at most one current link.
- A Claimed link must not be presented as a confirmed identity.
- A link does not by itself let a user change existing credits for that party.
- A user cannot verify their own link.
- Link status changes follow a `CurationDecision`.
- When a user account is deleted, its link ends and its user reference is anonymized. The party and its credits remain.

Organizations that several accounts must manage are left to a later release.

## Confirming Identity

A curator confirms a link by reviewing evidence, such as a link from the designer's own website, or confirmation from a publisher or known community member.

Government identification is not collected. Until a link is confirmed, it remains Claimed.

---

# Merges and Privacy

## Work Merges and Splits

A credit keeps its original `target_reference` and reaches the surviving work through `merged_into_work_id`. Duplicates are reconciled during curation.

When a work is split, curation decides which credits apply to each resulting work.

## Party Merges

A party merge is a curated decision. Its credits move to the surviving party, and Provenance records every change.

Its names are copied to the surviving party, except where the same name and language already exist. Copied names keep their type, except that `Primary` becomes `Alternative`, and they are not marked as preferred.

References held in other domains are not rewritten. They resolve through `merged_into_party_id`.

## Privacy Requests

A living person may ask not to be named. Curation may then change the relevant credits to Anonymous and apply the restriction or redaction process defined in the Provenance Domain. Restriction is used first, so the party is retained privately unless removal is legally required.

Credit is not removed merely because a user account is deleted.

---

# Contributor Experience

Attribution is entered through a short, guided question rather than a form that mirrors this model. This follows Principle 18, which applies to data entry in every domain.

## The Guided Question

```text
Who designed this?

  ○ I did
  ○ Someone else → type a name, with suggested matches
  ○ I don't know
  ○ It's a traditional design
```

| Answer | What is recorded automatically |
|---|---|
| I did | The contributor's party, if needed, a Claimed `PartyUserLink`, and a Claimed Original Designer credit |
| Someone else | A matched or new `Party`, and a Claimed Original Designer credit |
| I don't know | A Claimed credit with the Unknown state |
| It's a traditional design | A Claimed credit with the Traditional state |

## Field Classification

| Field | Classification | Notes |
|---|---|---|
| Designer answer | Worth asking | The one attribution question in a first submission |
| Other credits, such as a diagrammer | Optional | Offered after submission |
| `credited_as` | Optional | Normally for curators |
| `confidence` | Optional | Normally for curators |
| `notes` | Optional | Offered only when useful |
| Party `description` | Optional | Added later |
| Additional names | Optional | Added later, normally by curators |
| `contribution_role` | Automatic | Follows the question being answered |
| `attribution_state` | Automatic | Follows the answer |
| `party_type` | Automatic | Defaults to Person, and curators may correct it |
| `credit_status` | Automatic | Starts as Claimed |
| `preferred` and `name_type` | Automatic | The first name becomes Primary and preferred |
| `target_reference` | Automatic | Follows the work being entered |

Questions must use everyday language and must not use terms such as party, credit, or attribution state.

---

# Consolidated Relationships

```text
Party
  1 ─── 1..* PartyName
  1 ─── 0..* Credit
  1 ─── 0..* PartyUserLink (at most one current)
  * ─── 0..1 Party (merged into)

PartyName
  * ─── 1 Party

Credit
  * ─── 0..1 Party

PartyUserLink
  * ─── 1 Party
```

Cross-domain references:

```text
Credit
  → OrigamiWork
  → OrigamiEdition
  → DigitalArtifact

Provenance
  → Credit, through EvidenceLink, CurationDecision, and ValueProvenance

PartyUserLink
  → User
```

Following the Catalog Domain's rule, each cross-domain link is owned by the referencing domain.

---

# Domain Boundaries

| Concern | Owner | Attribution role |
|---|---|---|
| Who uploaded or changed something | Provenance | None; an actor receives no credit |
| Evidence for an attribution | Provenance | References it |
| Imported author text | Provenance | Receives a credit once a claim is accepted |
| Decisions about a credit | Provenance | Holds the resulting status |
| Name of a work | Catalog | None |
| Publisher named in `publication_statement` | Catalog | May add a separate Publisher credit |
| License holder and permissions | Rights | None; credit does not grant rights |
| Account sign-in | Authentication | Links an account to a party |

---

# Conceptual Integrity Summary

```text
User ≠ Party

Actor ≠ Credited Party;
uploading does not earn credit.

Unknown ≠ Missing;
an unknown creator is recorded, and a missing credit is not.

Traditional ≠ Unknown;
a tradition is not an unidentified individual.

Credit ≠ Rights;
recognition does not grant permission.

Conflicting credits coexist;
curated conclusions do not delete them.
```

---

# Validation Scenarios

| Scenario | How it is recorded |
|---|---|
| A contributor enters their own design | Claimed credit and Claimed link. A different curator would need to verify them. |
| A historical designer never uses the site | `Party` only, with credits and no link. |
| A traditional crane is entered | Claimed credit with the Traditional state and no party. |
| The designer of a design is unknown | Claimed credit with the Unknown state. |
| Nothing has been entered about the designer | No credit exists, and the work remains valid. |
| A user uploads someone else's diagram | Upload activity, no credit for the user, and a credit for the diagram's creator when known. |
| A FOLD file has `file_author` text | Pending `Claim`. A curator chooses the party and role when accepting it, because the text does not state a role. |
| A FOLD file has `file_creator` text | This names software, so it creates no credit. |
| Two sources name different designers | Two credits, each with its own evidence, and a curator may mark them Disputed. |
| A curator concludes one attribution is correct | One credit becomes Verified, and the other becomes Disputed or Deprecated with its evidence preserved. |
| A book prints a designer's name differently | The credit points to one party, and `credited_as` preserves the printed form. |
| A designer is known by Japanese and romanized names | One party with several `PartyName` records. |
| Two parties may be the same person | They remain separate until a curator approves a merge. |
| Someone falsely claims to be a famous designer | The Claimed link is not shown as confirmed and gives no power over existing credits. |
| A designer deletes their account | The link ends. The party and credits remain. |
| A living designer asks not to be named | Curation may change credits to Anonymous, keeping the party restricted unless removal is legally required. |
| Two people designed a work together | Two Original Designer credits on the same work. |
| A diagram exists with no edition recorded | A Diagram Creator credit on the artifact. |

---

# MVP Scope

MVP Attribution entities:

- `Party`
- `PartyName`
- `Credit`
- `PartyUserLink`

A first submission needs one attribution answer, and that answer may be "I don't know".

---

# Future Expansion

## PartyRelationship

Relationships between parties, such as membership of an organization or a teacher and student.

## Other Potential Concepts

- External identifiers for parties.
- Biographies and other detailed profile information.
- Organizations managed by several user accounts.
- Credit ordering for works with many contributors.

---

# Key Conceptual Answers

## Does Every Credit Need a Named Person?

No. A credit may record an Unknown, Anonymous, or Traditional state without a party.

## Must a Designer Have an Account?

No. Historical and non-participating designers exist as parties only.

## Does Claiming a Party Prove Identity?

No. A Claimed link remains unconfirmed until a curator reviews it.

## What Is the Difference Between Confidence and Status?

Status records what review has concluded. Confidence is an optional human assessment of likelihood.

---

# Review Corrections

Applied after the Draft 1.0 final consistency check:

- **Link history:** A party may have several links over time, with at most one current.
- **Anonymous credits:** A party may be retained under restricted access, consistent with restricting content before destroying it.
- **Role targets:** Contribution roles state which targets they apply to, and Traditional applies only to an original designer of a work.
- **Shared roles:** Several parties may hold the same role on one target.
- **Party merges:** Name copying and reference resolution follow the same approach as work merges.
- **Deleted accounts:** An ended link's user reference is anonymized.
- **Link and import decisions:** Link status changes follow a `CurationDecision`, and an accepted import creates a Claimed credit.
- **Identity confirmation:** A curator reviews evidence, and government identification is not collected.

---

# Remaining Open Questions

- Which legal requirements apply when a living person asks not to be named? This needs legal review before launch.

This question does not block the conceptual definitions in this document.

---

# Deferred to Other Domain Specifications

- **Rights Domain:** Define how a rights holder relates to a credited party.
- **Community Domain:** Define profiles and how they present a linked party.

---

Omnia Vincit Amor
