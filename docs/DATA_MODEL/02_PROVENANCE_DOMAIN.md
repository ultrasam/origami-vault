# 02_PROVENANCE_DOMAIN.md

## Origami Vault Provenance Domain

**Version:** 1.2

**Status:** Frozen

- Conceptual Entity Pass: Completed
- Architecture Review (Draft 1.0): Passed with conditions
- Review Conditions (Draft 1.1): Resolved; see [Review Corrections](#review-corrections)
- Final Consistency Check (Draft 1.2): Completed

---

# Purpose

The Provenance Domain records where Origami Vault knowledge came from, how it was produced, how it changed, and how it was reviewed.

It allows users and maintainers to trace a value to its source, distinguish original data from derived data, and preserve competing claims and their evidence.

The Provenance Domain supports every other domain, but it does not own their knowledge. It explains that knowledge.

---

# Scope

The Provenance Domain answers:

- Where did this information come from?
- What activity produced or changed it?
- Which tool, process, or account performed that activity?
- Which claims exist about this value, and which evidence supports or contradicts them?
- Was this value uploaded, imported, contributed, computed, converted, inferred, or curated?
- Has this value been accepted or verified, by what decision, and why?
- What did a validation report conclude about an artifact?
- Was any content restricted or redacted, and on what basis?

The Provenance Domain does not own:

- Works, editions, titles, relationships, or revisions.
- Parties, credits, contribution roles, or attribution status.
- Licenses or rights statements.
- Digital files, artifact versions, checksums, or derivation links.
- FOLD documents, frames, or FOLD-specific validation rules.
- Citations or bibliographic records.
- User accounts, roles, or permissions.
- Security logs or general application activity.

Those concepts are owned outside Provenance. Provenance may reference them.

---

# Domain Ownership

The Provenance Domain owns:

- `ProvenanceActivity`
- `ActivityLink`
- `Source`
- `Claim`
- `EvidenceLink`
- `ValueProvenance`
- `CurationDecision`
- `ValidationReport`

The Provenance Domain may also own this future entity:

- `VerificationReview`

These are conceptual entities. They do not each need a separate database table.

---

# Core Conceptual Decisions

## Provenance Explains Knowledge but Does Not Replace It

An accepted value lives in its owning domain. Provenance records how that value got there.

```text
Owning domain
    = what Origami Vault currently holds

Provenance
    = where it came from, how it changed, and how it was reviewed
```

## Origin and Verification Are Independent

Origin describes how a value was produced. Verification describes how it was reviewed.

```text
origin_type
    = how the value was produced

verification_status
    = what review has concluded
```

An imported value may be verified. A curated value may remain unverified.

## Claim, Acceptance, and Verification Are Different States

```text
Claim
    = information asserted by a source

Acceptance
    = the decision that a value will be held as current knowledge

Verification
    = a review confirming that value against evidence
```

Following Principle 5:

- Imported information remains a `Claim` until it is explicitly accepted.
- Direct entry by an authorized contributor counts as acceptance, with its provenance recorded.
- Acceptance does not mean verification.

```text
Imported information
        ↓
Claim (Pending)
        ↓
CurationDecision
        ↓
Accepted value in its owning domain
        ↓
ValueProvenance
```

## Domain-Owned Claims Stay in Their Domains

A domain may keep explicit claims as its own records when those claims are its subject matter. `WorkRelationship` is one example, and the Attribution Domain may treat `Credit` the same way.

Provenance does not duplicate those records or their status values. It provides their evidence and decision history through `EvidenceLink` and `CurationDecision`. A decision on such a record updates its status in the owning domain.

## Actors Are Not Credited Parties

An actor is the account, process, or system that performed an activity. Recording an actor does not give that actor credit.

```text
Uploader ≠ Designer
```

Credit is owned by the Attribution Domain.

## Provenance Is Append-Only

Provenance history is corrected by adding records, not by rewriting or deleting them.

Current-state fields, such as `Claim.acceptance_status` and `ValueProvenance.verification_status`, may change only through a `CurationDecision`, so that each change remains preserved.

There are two exceptions:

- When a user account is deleted, the actor's identity is replaced with an opaque label. See [Actor Reference](#actor-reference).
- When removal is legally required, the affected content is redacted. See [Access Restriction and Redaction](#access-restriction-and-redaction).

Principle 17 applies: curated conclusions must not destroy the claims and evidence they were based on.

## Access Restriction and Redaction

Preservation is favored, so content is restricted before it is destroyed.

```text
Restrict Access
    = the content is hidden but kept

Redact
    = the content is permanently removed
```

Redaction is used only when removal is legally required, such as under a court order or a valid personal data removal request.

Each action follows this process:

1. Record the request.
2. A designated human maintainer decides and records the basis.
3. Apply the action, and notify the affected contributor when appropriate.
4. Restore access where the action is reversible.

A redaction also reaches copies of the content held in Provenance, such as `claimed_value`, `recorded_value`, and `details`. The record of the action keeps what was affected, the basis, and the date, but not the removed content.

## No General Confidence Value

Provenance has no general confidence value. Uncertainty about a value is expressed through `verification_status`, linked evidence, and the rationale of a `CurationDecision`.

The Attribution Domain owns credit confidence.

A tool's own score may be recorded in `ProvenanceActivity.details` as tool output. It must never set or change an acceptance or verification status.

## Verification Follows Evidence, Not Popularity

Verification is based on evidence and a defined process. Votes, popularity, or reputation alone must not verify a value.

The criteria for a Verify decision are defined in a versioned policy, `docs/policies/VERIFICATION_POLICY.md`, rather than in this model. Facts that do not meet the criteria remain Unverified, which is a valid state.

---

# Shared Concepts

## Record Reference

Provenance entities refer to records in other domains through a record reference.

A record reference identifies:

- The owning domain.
- The record type.
- The record identifier.
- An optional field, when the statement concerns one value rather than the whole record.

A record reference preserves the identity recorded at the time. It is not rewritten when a referenced work is merged; the current identity is resolved through `merged_into_work_id`.

A reference to a work that was split continues to identify the original work. Curation decides which resulting work each pending claim applies to.

## Actor Reference

An actor reference identifies who or what performed an activity:

- **User:** an authenticated account.
- **Process:** an automated process or tool, including an AI model.
- **Migration:** a bulk data migration.
- **Anonymized User:** a deleted account, shown through an opaque label.
- **Unknown:** the actor is unknown.

When a user account is deleted, personal details are removed from its actor references, and its activities remain under an opaque label, such as `Deleted contributor 8F3A`. The same label is used for all of that account's activities, so traceability and the self-verification rule still work.

Any mapping between the label and the deleted account is removed, unless a documented reason requires keeping it.

Anonymizing an actor does not change credit. A `Party` and its credits belong to the Attribution Domain.

---

# Implementation Support Fields

The following fields are implementation support fields rather than purely conceptual attributes. They are listed separately for each entity that uses them.

- `created_at`: time the record was created.
- `updated_at`: time the record was last updated.

---

# Entity: ProvenanceActivity

## Definition

A preserved record of one action that produced, changed, interpreted, or reviewed Origami Vault knowledge.

## Identity Boundary

`ProvenanceActivity` answers:

```text
What happened, who or what did it, and with what result?
```

It does not record page views, sign-ins, permission checks, or other application activity that does not affect knowledge.

## Conceptual Attributes

- `id`
- `activity_type`
- `actor`
- `tool_name`
- `tool_version`
- `started_at`
- `ended_at`
- `outcome`
- `warnings`
- `errors`
- `details`

`details` holds type-specific information and enough change information to reconstruct the prior state when the activity changes knowledge.

The `ImportRecord`, `ParseRecord`, and `TransformationRecord` concepts from the FOLD research are represented as activity types rather than separate entities.

## Implementation Support Fields

- `created_at`

## Controlled Activity Types

- Upload
- Import
- Parse
- Extraction
- Contribution
- Transformation
- Conversion
- Validation
- Curation
- Verification
- Merge
- Split
- Migration
- Access Restriction
- Access Restoration
- Redaction

The vocabulary may be extended through controlled records rather than arbitrary free text.

## Controlled Outcomes

- Succeeded
- Succeeded with Warnings
- Partially Succeeded
- Failed
- Unknown

## Transformation and Conversion Details

A Transformation or Conversion activity records:

- Operation name.
- Parameters and tolerance values.
- Target format or specification version.
- Whether the operation is Lossless, Believed Lossless, Lossy, or Unknown.

Input and output artifact versions are recorded through `ActivityLink`.

## Integrity Rules

- Failed activities remain preserved.
- A tool-based activity records the tool name and version when they are known.
- A transformation must never run against a preservation copy. It operates on a working copy and produces a separate output.
- An AI-generated result is recorded with its model name and version in `tool_name` and `tool_version`, and it does not become authoritative without human review.

## Relationships

```text
ProvenanceActivity
  1 ─── 0..* ActivityLink
  1 ─── 0..* Claim
  1 ─── 0..* ValueProvenance
  1 ─── 0..* CurationDecision
  1 ─── 0..* ValidationReport
```

---

# Entity: ActivityLink

## Definition

A link identifying a record that took part in a `ProvenanceActivity`.

## Conceptual Attributes

- `id`
- `activity_id`
- `target_reference`
- `role`

## Implementation Support Fields

- `created_at`

## Controlled Roles

- **Input:** consumed by the activity.
- **Output:** produced by the activity.
- **Subject:** reviewed or changed by the activity.

## Relationship

```text
ProvenanceActivity
  1 ─── 0..* ActivityLink
```

## Integrity Rules

- Every link belongs to exactly one activity.
- Role must be controlled.
- A link does not replace `ArtifactDerivation`, which the Artifact Domain owns. A derivation may refer to the activity that produced it.

---

# Entity: Source

## Definition

An identifiable origin of information.

## Conceptual Attributes

- `id`
- `source_type`
- `source_reference`
- `description`

## Implementation Support Fields

- `created_at`

## Controlled Source Types

- **Artifact Version:** a preserved version of a digital artifact.
- **External Reference:** a citation or external resource owned by the External Reference Domain.
- **Contributor Statement:** a statement made by a contributor.
- **External System:** another system or dataset.
- **Unknown:** the source is unknown.

## Relationships

```text
Source
  1 ─── 0..* Claim
  1 ─── 0..* EvidenceLink
  1 ─── 0..* ValueProvenance
```

## Integrity Rules

- `Source` refers to artifacts and citations; it does not copy them.
- `source_reference` is required unless the source type is Contributor Statement or Unknown.
- An Unknown source is valid and must not be replaced with an assumption.
- For a Contributor Statement, the person asserting it is the activity's actor, unless the source identifies someone else.

---

# Entity: Claim

## Definition

Information asserted by a source and preserved as stated, whether or not it has been accepted.

## Identity Boundary

`Claim` answers:

```text
What did this source say?
```

It does not answer:

```text
What does Origami Vault currently hold?
Is this statement true?
```

## Conceptual Attributes

- `id`
- `target_reference`
- `claimed_property`
- `claimed_value`
- `language`
- `source_id`
- `activity_id`
- `acceptance_status`

`target_reference` may be absent when the claim was imported before a target record exists. A target may later be assigned or changed only through a `CurationDecision`.

`activity_id` is required, because every claim enters through an activity. When the circumstances are unknown, the activity records a Migration type or an Unknown actor.

`claimed_value` preserves the text or value exactly as stated.

`language` is a BCP 47 tag. An unknown language is recorded with `und`.

## Implementation Support Fields

- `created_at`
- `updated_at`

## Controlled Claimed Properties

- Title
- Author
- Description
- Publication Date
- Publication Statement
- Language
- License
- Classification

The vocabulary may be extended through controlled records rather than arbitrary free text.

## Controlled Acceptance Status

- **Pending:** not yet decided.
- **Accepted:** adopted as the current value.
- **Not Accepted:** reviewed and not adopted. This does not mean the claim is false.
- **Superseded:** previously accepted and later replaced as the current value.

## Relationships

```text
Claim
  * ─── 1 Source
  * ─── 1 ProvenanceActivity
  1 ─── 0..* EvidenceLink
  1 ─── 0..* CurationDecision
```

## Integrity Rules

- A claim must not overwrite a value in any domain automatically.
- A claim's stated value must not be edited. A correction is a new claim or a curated value.
- Claimed property must be controlled.
- Acceptance status may change only through a `CurationDecision`.
- When a later value replaces an accepted claim's value, that claim becomes Superseded.
- Claims must not be deleted because they were not accepted.
- Several claims about the same property may coexist.
- Matching claim text does not establish that two records describe the same subject.

---

# Entity: EvidenceLink

## Definition

A link stating how a source bears on a claim, a domain-owned claim record, or an accepted value.

## Conceptual Attributes

- `id`
- `target_reference`
- `source_id`
- `stance`
- `locator`
- `notes`

`target_reference` may identify a `Claim`, a domain-owned claim such as `WorkRelationship` or `Credit`, or an accepted value through its `ValueProvenance`.

`locator` identifies the relevant part of the source, such as a page range, frame range, or timecode.

## Implementation Support Fields

- `created_at`

## Controlled Stances

- **Supports:** the source supports the target.
- **Contradicts:** the source contradicts the target.
- **Context:** the source is relevant but does not decide the question.

## Relationships

```text
Source
  1 ─── 0..* EvidenceLink

Claim
  1 ─── 0..* EvidenceLink
```

## Integrity Rules

- Every link identifies exactly one source and one target.
- Stance must be controlled.
- Evidence that contradicts an accepted value remains preserved.
- Adding evidence does not change a status automatically. A curator decides whether a value or domain-owned claim is disputed.

---

# Entity: ValueProvenance

## Definition

A record of how an accepted value, or a whole record, was produced.

## Identity Boundary

`ValueProvenance` answers:

```text
How did this value come to be held?
```

## Conceptual Attributes

- `id`
- `target_reference`
- `origin_type`
- `activity_id`
- `source_id`
- `claim_id`
- `recorded_value`
- `verification_status`
- `supersedes_id`

`recorded_value` optionally preserves the value as it stood when this record was created.

## Implementation Support Fields

- `created_at`
- `updated_at`

## Controlled Origin Types

- **Uploaded:** present in a source artifact and preserved as supplied.
- **Imported:** brought from an artifact, file metadata, or external system as a claim, and later accepted without change.
- **Contributed:** entered directly by an authorized contributor.
- **Inherited:** obtained through a structural rule, such as FOLD frame inheritance.
- **Computed:** derived algorithmically.
- **Converted:** produced by format conversion.
- **Inferred:** suggested from incomplete information, including by an AI model.
- **Curated:** edited by a human curator, including a claim whose text was changed when it was accepted. `claim_id` still links such a value to the claim.
- **Unknown:** the origin is unknown.

## Controlled Verification Status

- **Unreviewed:** not yet reviewed.
- **Unverified:** reviewed, but the available evidence is insufficient to confirm or reject the value.
- **Verified:** reviewed and confirmed by the available evidence under a defined process.
- **Disputed:** reviewed and subject to credible conflicting evidence.

These values align with `WorkRelationship.relationship_status`, where Claimed corresponds to Unreviewed.

## Granularity

Provenance may be recorded for a whole record or for one field. A field-level record is used when a value's origin differs from the rest of its record.

Bulk derived data may use process-level provenance through `ProvenanceActivity`. A provenance record is not required for each vertex, edge, or face.

A record that has not been superseded is current. When both exist, a field-level record takes precedence over a record-level record for that field.

## Relationships

```text
ValueProvenance
  * ─── 1 ProvenanceActivity
  * ─── 0..1 Source
  * ─── 0..1 Claim
  * ─── 0..1 ValueProvenance (supersedes)
```

## Integrity Rules

- Origin type must be controlled.
- At most one `ValueProvenance` record may be current for each target.
- Verification status must be controlled.
- Each change to `verification_status` must be recorded by a `CurationDecision`.
- Apart from `verification_status`, a record is not edited. A later value adds a new record that supersedes the earlier one.
- Derived data must never be presented as though it was supplied in the original artifact.
- Inferred values require human review before they become authoritative.
- `verification_status` does not replace `relationship_status` or the status of any other domain-owned claim.
- Origin and verification status must be available wherever a value is presented, including through APIs.
- Missing provenance must not invalidate a value. It is recorded as Unknown when the origin is unknown.

---

# Entity: CurationDecision

## Definition

A preserved decision about a claim, a value, or a domain-owned claim record, together with its reason.

## Conceptual Attributes

- `id`
- `activity_id`
- `decision_type`
- `target_reference`
- `rationale`
- `policy_reference`

The actor and time come from the related `ProvenanceActivity`.

`policy_reference` identifies the curation or verification policy applied, when one exists.

## Implementation Support Fields

- `created_at`

## Controlled Decision Types

- Accept
- Decline
- Supersede
- Assign Target
- Verify
- Mark Unverified
- Mark Disputed
- Approve Merge
- Approve Split
- Deprecate
- Restrict Access
- Restore Access
- Redact

## Relationships

```text
ProvenanceActivity
  1 ─── 0..* CurationDecision

Claim
  1 ─── 0..* CurationDecision
```

## Integrity Rules

- Decision type must be controlled.
- A Verify decision must record a rationale, refer to evidence, and identify the version of the verification policy applied.
- In the MVP, only an authorized curator may make a Verify decision.
- A person must not verify a claim that they asserted, or a value that they contributed. A designer's statement about their own work is evidence, not verification.
- Uploading or importing another source's statement does not count as asserting it.
- An automated process, including an AI model, must not make a `CurationDecision`. A person may approve a batch of decisions.
- A decision on a domain-owned claim updates the status in the owning domain. Provenance holds the decision, and the owning domain holds the status.
- A later decision may reverse an earlier one, but both remain preserved.
- Authorization roles are defined outside this domain.

---

# Entity: ValidationReport

## Definition

A preserved result of checking an artifact version or derived representation against a defined specification or rule set.

## Conceptual Attributes

- `id`
- `activity_id`
- `target_reference`
- `specification_declared`
- `specification_interpreted`
- `result`
- `findings`

The validator name, validator version, and time come from the related `ProvenanceActivity`.

`findings` holds structured warnings and errors. In the MVP, they may be embedded in the report.

## Implementation Support Fields

- `created_at`

## Controlled Results

- Passed
- Passed with Warnings
- Failed
- Could Not Complete

## Relationship

```text
ProvenanceActivity
  1 ─── 0..* ValidationReport
```

## Integrity Rules

- A failed validation must not prevent preservation.
- A report must not be overwritten by a later validation. Each run adds a report.
- Warnings and errors are stored outside the preserved artifact.
- Format conformance must not be presented as proof of physical foldability.
- The FOLD Domain owns FOLD-specific rules and interpretations. `ValidationReport` records the result of applying them.

---

# Consolidated Relationships

```text
ProvenanceActivity
  1 ─── 0..* ActivityLink
  1 ─── 0..* Claim
  1 ─── 0..* ValueProvenance
  1 ─── 0..* CurationDecision
  1 ─── 0..* ValidationReport

Source
  1 ─── 0..* Claim
  1 ─── 0..* EvidenceLink
  1 ─── 0..* ValueProvenance

Claim
  1 ─── 0..* EvidenceLink
  1 ─── 0..* CurationDecision
  1 ─── 0..* ValueProvenance

ValueProvenance
  * ─── 0..1 ValueProvenance (supersedes)
```

Cross-domain references:

```text
Provenance
  ├── Any domain record, through record references
  ├── ArtifactVersion, as a source, input, or output
  ├── ExternalReference, as a source
  └── User, as an actor

Other domains
  ├── ArtifactDerivation → ProvenanceActivity
  └── FOLD validation interpretations → ValidationReport
```

Following the Catalog Domain's rule, each cross-domain link is owned by the referencing domain.

---

# Domain Boundaries

| Concern                                     | Owner                       | Provenance role                          |
|---------------------------------------------|-----------------------------|------------------------------------------|
| Current title of a work                     | Catalog                     | Explains its origin and history          |
| Named revision of an edition                | Catalog                     | Holds evidence and process history       |
| Merge or split of works                     | Catalog defines the effects | Records every move, copy, and assignment |
| Credit, confidence, and attribution status  | Attribution                 | Holds evidence and decision history      |
| Immutable file versions and checksums       | Artifact                    | References them                          |
| Link from a derived file to its source file | Artifact                    | Explains the process that produced it    |
| FOLD-specific rules and parsed data         | FOLD                        | Records the validation result            |
| Citation details                            | External Reference          | Uses the citation as a source            |
| Copyright complaint procedure               | Rights                      | Records the restriction or redaction     |
| Sign-in, permissions, and accounts          | Authentication              | References the acting user               |

---

# Conceptual Integrity Summary

```text
Claim ≠ Accepted Value ≠ Verified Value

Origin describes production;
verification describes review.

Actor ≠ Party;
performing an activity does not earn credit.

Revision belongs to Catalog;
ArtifactVersion belongs to Artifact;
the explanation of both belongs to Provenance.

Not Accepted does not mean false;
curated conclusions do not destroy evidence.

Verification follows evidence;
popularity does not verify knowledge.
```

---

# Validation Scenarios

These scenarios test the model. Attribution-specific details remain for the Attribution Domain to define.

| Scenario                                                | How it is recorded                                                                                                                  |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| A FOLD file has `file_author` text                      | Pending `Claim` from an Artifact Version `Source`. No credit is created until a decision accepts it.                                |
| A FOLD file has `file_title` text                       | Pending `Claim`, possibly with no target yet. If accepted, a `WorkTitle` is created with `Imported` provenance.                     |
| Two sources name different designers                    | Both remain preserved with their own evidence. A curator may record a conclusion without removing the other claim.                  |
| The designer is unknown                                 | The Attribution Domain records the unknown state, and provenance records how that state was reached.                                |
| A user uploads a historical diagram                     | The user is the actor of an Upload activity and receives no credit.                                                                 |
| A creator enters their own title                        | `Contributed` provenance, accepted and `Unreviewed`. No `Claim` is required.                                                        |
| A creator claims a design as their own                  | Their statement is evidence. A different authorized curator must make any Verify decision.                                          |
| An import conflicts with an accepted title              | A Pending `Claim` is added, and the accepted title does not change.                                                                 |
| Sources disagree about a publication date               | Catalog holds the accepted date, and the competing claims remain in Provenance.                                                     |
| A FOLD file is converted to another format              | A Conversion activity records the tool, parameters, and losslessness. Artifact owns the derivation link.                            |
| A FOLD file fails validation                            | The file remains preserved, and a Failed `ValidationReport` is added.                                                               |
| An AI model extracts a title from a PDF                 | Pending `Claim` from an Extraction activity that names the model. It needs human acceptance.                                        |
| Two works are merged                                    | Merge activity and Approve Merge decision. Older references keep their original identity and resolve through `merged_into_work_id`. |
| Many community members like a claim                     | No verification results from that alone.                                                                                            |
| The only curator imports a historical file              | The curator did not assert its contents, so they may verify its claims against evidence.                                            |
| A curator corrects a claim's spelling when accepting it | `Curated` provenance linked to the claim, whose original text stays unchanged.                                                      |
| A work with a pending claim is split                    | The claim keeps its original reference until curation decides which resulting work it applies to.                                   |
| A court orders content removed                          | A Redaction activity and Redact decision remove the content, and the record of the removal remains.                                 |
| A copyright complaint is received                       | A Restrict Access decision hides the content under the Rights Domain's procedure, and the content is kept.                          |
| A user deletes their account                            | The actor reference becomes an opaque label, and the activities remain. Any credit stays in the Attribution Domain.                 |
| A creator wants to verify a title they entered          | Not allowed. A different authorized curator must make the Verify decision, using linked evidence.                                   |

---

# MVP Scope

MVP Provenance entities:

- `ProvenanceActivity`
- `ActivityLink`
- `Source`
- `Claim`
- `EvidenceLink`
- `ValueProvenance`
- `CurationDecision`
- `ValidationReport`

The MVP may record provenance progressively, but imported claims, artifact uploads, transformations, validation results, and curation decisions must be traceable from the start.

In the MVP, only authorized curators verify values.

---

# Future Expansion

## VerificationReview

A review submitted by a community member, including their finding, evidence, and notes.

Reviews would allow verification to become collaborative. A review is input to a curation or verification policy; it does not verify a value by itself.

Status:

```text
Conceptually proposed
Planned no earlier than Version 0.2
```

## Other Potential Concepts

- Structured `ValidationFinding` records.
- Interoperability with external provenance standards, such as W3C PROV.

---

# Key Conceptual Answers

## What Is the Difference Between Provenance and an Audit Log?

Provenance explains knowledge. An audit log records system activity. A sign-in belongs in a security log; accepting a title belongs in Provenance.

## When Does Imported Information Become Knowledge?

When a `CurationDecision` accepts the claim. The resulting value then lives in its owning domain, with `ValueProvenance` tracing it back to the claim.

## Does Accepted Mean True?

No. Accepted means Origami Vault holds the value as current. Verified means a review confirmed it against evidence. Neither erases competing claims.

## Does Uploading a File Earn Credit?

No. The uploader is an actor. Credit is recorded in the Attribution Domain.

## Can the Community Verify Knowledge?

Yes, through a defined process based on evidence. In the MVP, authorized curators make Verify decisions. Community reviews are a planned expansion, and popularity never verifies a value by itself.

---

# Review Corrections

Applied after the Draft 1.0 architecture review:

- **Self-verification:** The restriction applies to claims a person asserted themselves, not to statements they uploaded or imported.
- **Append-only history:** Current-state fields change only through a `CurationDecision`, and legally required removal is recorded as a redaction.
- **Current provenance:** Each target has at most one current record, and field-level records take precedence.
- **Split references:** Curation decides which resulting work each pending claim applies to.
- **Edited claims:** A claim changed when accepted has `Curated` provenance and stays linked to the claim.
- **Claim activity:** `Claim.activity_id` is required.
- **Claimed properties:** `claimed_property` is controlled.
- **Contradicting evidence:** New evidence does not change a status automatically.
- **Human decisions:** Automated processes do not make curation decisions.
- **Domain-owned status:** The owning domain holds the status, and Provenance holds the decision.
- **Presentation:** Origin and verification status are available wherever a value is presented.
- **Decision types:** `Withdraw Verification` is removed because `Mark Unverified` covers it.

Applied after the final consistency check:

- **Deleted accounts:** Actor identities are replaced with an opaque label, as a stated exception to append-only history.
- **Access restriction:** Content is restricted before it is destroyed, and redaction also reaches copies held in Provenance.
- **Verification policy:** Criteria for a Verify decision are defined in a versioned policy.
- **Evidence scope:** `EvidenceLink` can target accepted values, because values entered directly have no `Claim`.
- **Self-verification scope:** The restriction also covers values a person contributed.
- **Target assignment:** `Assign Target` records when a claim's target is assigned or changed.
- **Asserting person:** The person asserting a Contributor Statement is identified.
- **Superseded claims:** A claim becomes Superseded when a later value replaces its value.
- **Confidence:** Provenance has no general confidence value, and a tool's score never sets a status.

---

# Remaining Open Questions

- Which legal requirements apply to restriction and redaction? This needs legal review before launch.
- How do redaction and account deletion apply to backups?

These questions do not block the conceptual definitions in this document.

---

# Deferred to Other Domain Specifications

- **Attribution Domain:** Define `Credit` status and confidence, how imported author claims become credits, how an attribution source uses `Source` and `EvidenceLink`, and how privacy requests apply to a `Party`.
- **Artifact Domain:** Define `ArtifactDerivation`, checksums, and preservation events, including how they refer to provenance activities, and how restriction and redaction apply to preservation copies.
- **Rights Domain:** Define the copyright complaint procedure that leads to a restriction or redaction.
- **FOLD Domain:** Define FOLD-specific validation rules and how they are stored with a `ValidationReport`.
- **Community Domain:** Define how `VerificationReview` participates in collaborative verification.

---

Omnia Vincit Amor
