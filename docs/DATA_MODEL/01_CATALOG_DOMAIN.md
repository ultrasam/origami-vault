# 01_CATALOG_DOMAIN.md

## Origami Vault Catalog Domain

**Version:** 2.4

**Status:** Frozen

- Architecture Review (Draft 2.0): Passed
- Architecture Review (Draft 2.2): Passed with conditions
- Freeze Conditions (Draft 2.3): Resolved; see [Post-Review Amendments](#post-review-amendments)
- Conceptual Entity Pass: Completed
- Post-Review Amendments: Applied; see [Resolved Decisions](#resolved-decisions)
- FOLD Dependency Review (Version 2.4): Completed

This document supersedes `01_CATALOG_DOMAIN_CONCEPTUAL_ENTITY_PASS.md` and incorporates the corrections from `01_CATALOG_DOMAIN_v2_FREEZE_CANDIDATE.md`, with the post-review amendments recorded under [Resolved Decisions](#resolved-decisions).

---

# Purpose

The Catalog Domain represents conceptual origami knowledge and publication structure.

It provides the canonical identity and organization of works, editions, titles, revisions, and explicit relationships among works.

The Catalog Domain is the identity foundation used by the Attribution, Rights, Instruction, Artifact, Provenance, Taxonomy, External Reference, and Community domains.

The FOLD Domain has no direct references to Catalog entities. It interprets preserved artifact versions, and those artifacts may be linked to works or editions. Text extracted from FOLD files, such as `file_title` and `file_author`, reaches Catalog only through Provenance and curated acceptance.

The Notation Domain does not depend on Catalog identity.

---

# Scope

The Catalog Domain answers:

- What conceptual origami design is being described?
- Which distinct published, translated, reconstructed, adapted, or otherwise curated expressions exist for that design?
- Which titles are associated with the design?
- How is the design related to other designs?
- Which significant revisions belong to a particular edition?

The Catalog Domain does not own:

- Parties or credits.
- Licenses or rights statements.
- Instruction sets or steps.
- Digital files or artifact versions.
- FOLD documents or frames.
- Import, validation, transformation, or value-provenance records.
- Techniques, bases, categories, or tags.
- Community accounts or participation records.
- Recommended paper information, which is owned by the Instruction Domain.

Those concepts are owned by their respective domains and may reference Catalog entities.

---

# Domain Ownership

The Catalog Domain owns:

- `OrigamiWork`
- `OrigamiEdition`
- `WorkTitle`
- `WorkRelationship`
- `Revision`

The Catalog Domain will also own these approved future entities:

- `EditionTitle`
- `PublicationContainer`
- `PublicationOccurrence`

---

# Core Conceptual Decisions

## Work Is the Stable Conceptual Identity

`OrigamiWork` represents the identity of a conceptual origami design.

A work is independent of:

- A particular title.
- A particular language.
- A publication or release.
- A set of instructions.
- A diagram, photograph, video, PDF, FOLD file, or other artifact.
- A particular folded specimen created by a community member.

The work may be recognized through its intended design identity, but the Catalog Domain does not define that identity solely as a folding sequence, final physical specimen, geometric state, or file.

## Edition Is a Distinct Expression of One Work

`OrigamiEdition` represents one distinct published, translated, reconstructed, adapted, or otherwise curated expression of exactly one `OrigamiWork`.

A revised release constitutes a new edition only when it is intentionally assigned a distinct edition or publication identity.

An edition may organize or contextualize instructions, artifacts, credits, rights, and external references, but those records remain owned by their respective domains.

## Title Is Separate from Work Identity

`WorkTitle` owns title values. A title is not the identity of the work.

This allows alternate, historical, translated, and multilingual titles to coexist without creating duplicate works.

## Relationships Are Explicit Claims

`WorkRelationship` records an explicit relationship claim between two works. Relationships are not inferred merely from similar names, geometry, subjects, bases, or techniques.

## Revision Is Edition-Level Catalog History

`Revision` records a named or significant revision of an edition.

Revision does not replace artifact versioning, transformation provenance, audit history, or value-level provenance.

## Imported Metadata Is Not Catalog Knowledge

Catalog does not own imported title claims.

```text
Imported metadata
        ↓
Provenance / Extracted Metadata
        ↓
Curated Acceptance
        ↓
WorkTitle / EditionTitle
```

Imported metadata never becomes authoritative Catalog knowledge automatically.

Until `EditionTitle` is implemented, a curated edition title is recorded in the `OrigamiEdition.title` display field.

## Curated Values and Competing Claims

Catalog holds the curated value. Competing claims, such as a disputed publication date, remain preserved in the Provenance Domain.

Provenance records may attach to any Catalog entity or value, including `WorkTitle`, `WorkRelationship`, and `Revision`.

## Unknown Is a Valid State

Catalog values may be unknown. An unknown value is recorded explicitly rather than left ambiguous or replaced with an assumption.

This applies to `OrigamiEdition.languages`, `OrigamiEdition.publication_date`, `WorkTitle.language`, and `Revision.revision_date`.

Dates may be exact, partial (year or year and month), approximate, or unknown, so that historical knowledge is recorded at the precision the evidence supports. An unknown language is recorded with the BCP 47 tag `und`.

---

# Implementation Support Fields

The following fields are implementation support fields rather than purely conceptual attributes. They are listed separately for each entity that uses them.

- `slug`: stable URL-safe identifier used by applications.
- `created_at`: time the record was created.
- `updated_at`: time the record was last updated.

---

# Entity: OrigamiWork

## Definition

A stable Catalog identity representing one conceptual origami design.

## Identity Boundary

`OrigamiWork` answers:

```text
Which conceptual design is this?
```

It does not answer:

```text
Which publication is this?
Which file is this?
Which instructional workflow is this?
Which folded specimen is this?
Which geometric frame is this?
```

## Conceptual Attributes

- `id`
- `description`
- `status`
- `notes`
- `merged_into_work_id`

The preferred display title is resolved through `WorkTitle`; it is not a second authoritative title field on `OrigamiWork`.

## Implementation Support Fields

- `slug`
- `created_at`
- `updated_at`

## Lifecycle Status

- **Draft:** in preparation and not yet presented as published Catalog knowledge.
- **Published:** presented as current Catalog knowledge.
- **Archived:** valid and preserved, but no longer actively maintained.
- **Deprecated:** preserved, but no longer recommended because it is erroneous or superseded.
- **Merged:** preserved, with its identity resolving to the surviving work.

## Relationships

```text
OrigamiWork
  1 ─── 0..* OrigamiEdition
  1 ─── 0..* WorkTitle
  1 ─── 0..* WorkRelationship (outgoing)
  1 ─── 0..* WorkRelationship (incoming)
  * ─── 0..1 OrigamiWork (merged into)
```

Cross-domain relationships may include credits, licenses, classifications, techniques, bases, digital artifacts, external references, community activity, provenance records, and other records owned outside Catalog. See [External Reference Scope](#external-reference-scope).

## Integrity Rules

- A published work must have at least one `WorkTitle`.
- A draft or preservation-first work may temporarily have incomplete metadata.
- Missing attribution, license, classification, geometry, or instructions must not invalidate the work.
- Imported title or author text must not silently overwrite curated Catalog or Attribution records.
- Similarity alone must not automatically merge two work identities.
- A merge or split of work identities requires an explicit curated process with preserved provenance.
- `merged_into_work_id` is required when status is Merged and must otherwise be absent.
- `merged_into_work_id` must identify a different existing work, and merge references must not form a cycle.
- A merged work remains preserved, and its identity continues to resolve to the surviving work.
- A split retains the original work. Each new work records a `Split From` relationship to the original.
- A `DigitalArtifact` may link directly to a work when it is not known to represent a particular edition. A direct link does not establish an edition.
- When an artifact is known to represent a particular edition, it links to that edition.
- An artifact may represent several works or editions. The Artifact Domain owns that link, including any locator identifying the relevant part of the artifact.

## Merge and Split Effects

When a work is merged into a surviving work:

- Its editions move to the surviving work, with their revisions.
- Its relationships move to the surviving work and are reconciled with any equivalent relationships already there.
- Relationships between the two merged works do not move. They remain on the merged work with the status Deprecated.
- Its titles remain on the merged work and are copied to the surviving work, except where the same title and language already exist. Copied titles keep their type, except that `Primary` becomes `Alternative`, and they are not marked as preferred.
- Records owned by other domains reach the surviving work through `merged_into_work_id`, unless their owning domain defines otherwise.

When a work is split, curation assigns each edition to exactly one resulting work.

Provenance records every move, copy, and assignment, so that the prior state can be reconstructed.

## Same Work Versus New Work

The following normally remain the same work:

- A translated title.
- A new diagram set.
- A new instructional language.
- A new video or photograph.
- A corrected file.
- A new publication of the same conceptual design.

These changes do not normally create a new `OrigamiWork`. Some of them may create a new `OrigamiEdition` when they establish a distinct published, translated, reconstructed, or otherwise curated expression of the work.

The following may justify a new work when the design identity is intentionally changed:

- A substantial variation.
- A simplification that changes the design identity.
- An expansion or remix recognized as a distinct design.
- A derivative design with independent identity.

When uncertain, preserve separate records and record a `Possibly Same As` or other `WorkRelationship` rather than forcing an irreversible merge.

---

# Entity: OrigamiEdition

## Definition

A distinct published, translated, reconstructed, adapted, or otherwise curated expression of exactly one `OrigamiWork`.

A revised release constitutes a new edition only when it is intentionally assigned a distinct edition or publication identity.

## Identity Boundary

`OrigamiEdition` answers:

```text
Which expression or release of the work is this?
```

An edition may correspond to:

- An original publication.
- A translation.
- A reconstructed presentation.
- A revised instructional release that has been intentionally assigned a distinct edition or publication identity.
- An interactive or mixed-media presentation.
- A community-curated edition.

A container such as a book, magazine, or website is not automatically an edition by itself. The edition is the expression of the work represented or published through that container.

## Conceptual Attributes

- `id`
- `work_id`
- `title`
- `edition_type`
- `description`
- `languages`
- `publication_date`
- `publication_statement`
- `status`

`OrigamiEdition.title` is retained in the MVP as an optional display field. It may identify a release or presentation, but it does not replace the work's `WorkTitle` records. When it is absent, the edition displays the work's resolved title; see [Title Resolution](#title-resolution).

`languages` is the set of languages in which the edition is expressed, recorded as BCP 47 tags. The set must not be empty. Two values are reserved, and each must be the only value in the set when used:

- `und`: the language is unknown or undetermined.
- `zxx`: the edition has no linguistic content, such as a wordless, symbol-only diagram.

`publication_date` records the first known public release of the edition.

`publication_statement` is an optional, curated transcription of where and how the edition was published, for example `Book title, publisher, year, pp. 40–47`. It is an MVP field. It does not establish a `Credit` or a `PublicationContainer` identity.

## Edition Types

Controlled `edition_type` values:

- Original Publication
- Translation
- Reconstruction
- Adaptation
- Revised Release
- Interactive or Mixed Media
- Community Curated
- Unknown

An edition has one `edition_type`, which records its principal character. The vocabulary may be extended through controlled records rather than arbitrary free text.

## Implementation Support Fields

- `created_at`
- `updated_at`

## Lifecycle Status

- **Draft:** in preparation and not yet presented as published Catalog knowledge.
- **Published:** presented as current Catalog knowledge.
- **Archived:** valid and preserved, but no longer actively maintained.
- **Deprecated:** preserved, but no longer recommended because it is erroneous or superseded.

## Relationships

```text
OrigamiWork
  1 ─── 0..* OrigamiEdition

OrigamiEdition
  * ─── 1 OrigamiWork
  1 ─── 0..* Revision
```

Cross-domain relationships may include credits, licenses, instruction sets, digital artifacts, external references, completed folds, and provenance records.

## Integrity Rules

- Every edition belongs to exactly one work.
- An edition must not belong to multiple works.
- A work may exist without an edition during preservation or curation.
- Replacing, correcting, or deriving a file does not by itself create a new edition.
- A language change, substantive instructional reconstruction, or intentionally distinct publication expression may create a new edition.
- Imported file metadata may suggest an edition but does not automatically establish one as verified Catalog knowledge.
- Imported publication text remains a source claim until curated into `publication_statement`.
- An edition may be Published only while its work is Published.
- When a work leaves Published status, its Published editions must move to Archived or Deprecated, unless a merge moves them to a Published surviving work.
- An edition expressed in several languages remains one edition. Translating it into a further language may create a new edition.

## New Edition Versus New Artifact Version

Create or consider a new edition when the expression itself changes, for example:

- Translation into another language.
- Substantive reconstruction of instructions.
- Deliberately distinct release or presentation.
- Material editorial revision that is intended as a new expression and intentionally assigned a distinct edition or publication identity.

Create a new `ArtifactVersion`, not a new edition, when only the preserved file changes, for example:

- Corrected scan.
- Re-encoded video.
- Re-exported PDF.
- New checksum-equivalent preservation copy.
- Generated thumbnail or preview.

Artifact-version semantics are owned by the Artifact Domain.

---

# Entity: WorkTitle

## Definition

A title associated with one `OrigamiWork`.

## Purpose

`WorkTitle` preserves:

- Primary titles.
- Alternative titles.
- Historical titles.
- Translated titles.
- Aliases.
- Curator-supplied titles.
- One preferred display title per language.

## Conceptual Attributes

- `id`
- `work_id`
- `title`
- `language`
- `title_type`
- `preferred`

`language` is a single BCP 47 tag, because a title is written in one language.

```text
title_type
    = semantic role

preferred
    = display selection
```

## Title Resolution

The display title for a requested language resolves in this order:

1. The preferred `WorkTitle` for that language.
2. The work's `Primary` title.
3. The earliest-created remaining `WorkTitle` of the work, so that the same title is displayed consistently.

A work with no `WorkTitle` has no resolved title. An application may show a placeholder, but the placeholder must not be stored as a `WorkTitle`. When no original title is known, a curator may assign a descriptive title with the `Supplied` title type.

## Implementation Support Fields

- `created_at`
- `updated_at`

## Controlled Title Types

- Primary
- Alternative
- Historical
- Translated
- Alias
- Supplied

`Supplied` identifies a title assigned by a curator because no original title is known. The vocabulary may be extended through controlled records rather than arbitrary free text.

## Relationship

```text
OrigamiWork
  1 ─── 0..* WorkTitle

WorkTitle
  * ─── 1 OrigamiWork
```

## Integrity Rules

- Every title belongs to exactly one work.
- A work may have multiple titles.
- At most one `WorkTitle` may be preferred for the same work and language. The `und` tag counts as one language value for this rule.
- At most one `WorkTitle` of a work may have the `Primary` title type.
- If multiple preference contexts are later required, the model must introduce an explicit context entity rather than rely on application-specific behavior.
- Historical and alternate titles should be preserved rather than overwritten.
- Matching title text does not prove that two records represent the same work.
- Imported title text remains a source claim until curated into a `WorkTitle` or edition title.

---

# Entity: WorkRelationship

## Definition

An explicit relationship claim connecting a source work to a target work.

## Conceptual Attributes

- `id`
- `source_work_id`
- `target_work_id`
- `relationship_type`
- `notes`
- `relationship_status`

Provenance for the relationship claim is owned by the Provenance Domain and may be attached through cross-domain records.

## Implementation Support Fields

- `created_at`
- `updated_at`

## Initial Controlled Relationship Types

- Variation Of
- Derived From
- Remix Of
- Simplification Of
- Expansion Of
- Split From
- Component Of
- Related To
- Possibly Same As

The vocabulary may be extended through controlled records rather than arbitrary free text.

## Relationship Status

Controlled `relationship_status` values:

- **Claimed:** recorded from a source or contributor and not yet reviewed.
- **Unverified:** reviewed, but the available evidence is insufficient to confirm or reject the claim.
- **Verified:** reviewed and confirmed by the available evidence.
- **Disputed:** reviewed and subject to credible conflicting evidence.
- **Deprecated:** retired from current use and preserved as a historical record.

`relationship_status` records the review state of a claim, not whether the claim exists. Changing the status must not remove the claim or its evidence.

Provenance state remains external to Catalog and belongs to the Provenance Domain.

## Directionality

Directional relationships are interpreted as:

```text
source_work_id → target_work_id
```

Examples:

```text
source Variation Of target
source Derived From target
source Simplification Of target
source Expansion Of target
source Remix Of target
source Split From target
source Component Of target
```

`Related To` is symmetric and is stored once. Conceptually:

```text
Work A Related To Work B
```

is identical to:

```text
Work B Related To Work A
```

Relational implementations should normalize endpoint ordering and prevent duplicate reciprocal records.

`Component Of` records physical composition, such as a named unit used in a modular design. A part may belong to many composites, and a composite may have many parts. The quantity of units and the way they are assembled belong to the Instruction Domain. An edition that teaches a composite is an edition of the composite.

`Possibly Same As` is symmetric and follows the same storage rule. It records an open question of whether two records represent one work. If curation concludes that they do, the works are merged. If curation concludes that they do not, the relationship becomes Deprecated.

## Relationships

```text
OrigamiWork
  1 ─── 0..* WorkRelationship (as source)
  1 ─── 0..* WorkRelationship (as target)

WorkRelationship
  * ─── 1 OrigamiWork (source)
  * ─── 1 OrigamiWork (target)
```

## Integrity Rules

- Source and target must both identify existing works.
- Source and target must not be the same work.
- Relationship type must be controlled.
- Relationship status must be controlled.
- `Possibly Same As` must not merge two works by itself.
- `Component Of` chains must not form a cycle.
- A part is recorded as a separate work only when it has an independent design identity, such as a named or reused unit. Otherwise, the composite remains one work.
- Conflicting relationship claims may coexist when their provenance and status remain distinguishable.
- A relationship must not be inferred solely from matching titles, shared technique, shared base, similar geometry, or imported metadata.
- Duplicate equivalent relationships should be prevented or reconciled during curation.

---

# Entity: Revision

## Definition

A preserved record of a named or significant revision to one `OrigamiEdition`.

## Ownership Decision

`Revision` remains Catalog-owned because it describes the revision identity and history of an edition.

The Provenance Domain owns the evidence and process records explaining who changed what, how, why, and from which source. The Artifact Domain owns immutable file versions and derivations.

`OrigamiWork` has no `Revision` entity. Changes to a work's record are preserved through the Provenance Domain, and changes to its identity through the merge and split process.

## Conceptual Attributes

- `id`
- `edition_id`
- `revision_number`
- `revision_label`
- `revision_date`
- `summary`

Additional audit and provenance fields are defined outside Catalog.

## Implementation Support Fields

- `created_at`

## Lifecycle Status

No MVP status is required.

Future candidates:

- Draft
- Published
- Superseded
- Withdrawn

## Relationship

```text
OrigamiEdition
  1 ─── 0..* Revision

Revision
  * ─── 1 OrigamiEdition
```

## Integrity Rules

- Every revision belongs to exactly one edition.
- At least one of `revision_number` or `revision_label` must be supplied.
- `revision_number` and `revision_label` are each unique within an edition when supplied.
- A revision records significant edition-level change; it is not a substitute for an audit log.
- A corrected or replaced file is represented through `ArtifactVersion` unless the edition expression itself changed.
- A materially distinct expression may require a new edition rather than another revision.
- Superseded revisions may remain preserved.

## Revision Versus New Edition

Use a revision when:

- The same edition identity is retained.
- Corrections or bounded editorial changes are recorded.
- The release is explicitly presented as a revision of the edition.

Use a new edition when:

- The expression is intentionally distinct.
- Translation changes the instructional language.
- Instructions are substantially reconstructed.
- The release has an independent publication identity.

The exact operational threshold is a curation policy and may evolve without changing the conceptual distinction.

---

# Consolidated Relationships

```text
OrigamiWork
  1 ─── 0..* WorkTitle
  1 ─── 0..* OrigamiEdition
  1 ─── 0..* WorkRelationship (outgoing)
  1 ─── 0..* WorkRelationship (incoming)
  * ─── 0..1 OrigamiWork (merged into)

OrigamiEdition
  * ─── 1 OrigamiWork
  1 ─── 0..* Revision

WorkTitle
  * ─── 1 OrigamiWork

WorkRelationship
  * ─── 1 OrigamiWork (source)
  * ─── 1 OrigamiWork (target)

Revision
  * ─── 1 OrigamiEdition
```

Cross-domain references:

```text
OrigamiWork
  ├── Credit
  ├── License
  ├── Category
  ├── Tag
  ├── Technique
  ├── Base
  ├── DigitalArtifact
  ├── ExternalReference
  ├── Community records
  └── Provenance records

OrigamiEdition
  ├── Credit
  ├── License
  ├── InstructionSet
  ├── DigitalArtifact
  ├── ExternalReference
  ├── CompletedFold
  └── Provenance records
```

The referenced concepts remain owned by their respective domains.

## External Reference Scope

Phase 1:

```text
ExternalReference
  → OrigamiWork
  → OrigamiEdition
```

The External Reference Domain is part of the MVP. A work may exist without an edition, so a preserved reference must be attachable directly to the work without creating a placeholder edition.

Future:

```text
ExternalReference
  → OrigamiWork
  → OrigamiEdition
  → Party
  → Technique
  → Base
```

---

# Domain Boundaries

Catalog owns:

- Work identity.
- Edition identity.
- Work titles.
- Work-to-work relationship claims.
- Edition-level revision identity.

Catalog does not own:

- Creator identity or credit semantics.
- Licensing or permissions.
- Instructional sequence or pedagogy.
- Artifact bytes, versions, checksums, or derivations.
- FOLD geometry or frames.
- Imported claims, provenance evidence, transformations, or validation reports.
- Taxonomy vocabulary.
- Community identity or participation.
- Recommended paper information.

---

# Conceptual Integrity Summary

```text
OrigamiWork ≠ OrigamiEdition ≠ DigitalArtifact

WorkTitle identifies a work by name;
it does not constitute the work's identity.

Revision belongs to OrigamiEdition;
ArtifactVersion belongs to DigitalArtifact.

WorkRelationship is explicit;
similarity does not create it automatically.

Imported metadata is a claim;
curated acceptance is required before it becomes Catalog knowledge.
```

---

# MVP Scope

MVP Catalog entities:

- `OrigamiWork`
- `OrigamiEdition`
- `WorkTitle`
- `WorkRelationship`
- `Revision`

The MVP workflow may progressively collect metadata, but every record that exists must satisfy its integrity rules.

Phase 1 in this document corresponds to Version 0.1 in `ROADMAP.md`. Phase 2 denotes a later release that has not yet been assigned to a roadmap version.

---

# Future Expansion

## EditionTitle

Decision: Yes.

```text
OrigamiEdition
  1 ─── 0..* EditionTitle
```

Status:

```text
Conceptually approved
Phase 2 implementation
```

In the MVP, `OrigamiEdition.title` is retained as a display field.

## PublicationContainer and PublicationOccurrence

Decision: Yes.

Examples of publication containers:

- Book
- Magazine Issue
- Website
- Repository
- Exhibition Catalog
- Proceedings Volume

```text
PublicationContainer
  1 ─── 0..* PublicationOccurrence

OrigamiEdition
  1 ─── 0..* PublicationOccurrence
```

Status:

```text
Future Catalog Expansion
```

When these entities are implemented, `publication_statement` values migrate into occurrences, which hold each appearance's own date and pages. The original statement text is preserved as provenance, and `OrigamiEdition.publication_date` continues to record the first known public release.

## Other Potential Concepts

- `Series`
- `WorkSeriesMembership`
- `EditionRelationship`

---

# Key Conceptual Answers

## What Is an OrigamiWork?

A stable conceptual design identity, not merely a folding sequence, final specimen, geometric state, title, instruction set, or file.

## What Is an OrigamiEdition?

A distinct expression or release of exactly one work.

## Can a File Create a New Edition?

Not by itself. File replacement or derivation normally creates an artifact version or derivation. A new edition requires a distinct expression or release identity.

## Where Does Revision Belong?

Catalog owns edition-level revision identity. Provenance owns change evidence and process history. Artifact owns immutable file versions.

## How Are Ambiguous Work Identities Handled?

Preserve uncertainty, avoid automatic merges, and use explicit relationships, such as `Possibly Same As`, and provenance until curation resolves the identity question.

---

# Resolved Decisions

- **Edition multilingual titles:** Introduce `EditionTitle` as a future entity. Retain `OrigamiEdition.title` as an MVP display field.
- **PublicationContainer strategy:** Introduce `PublicationContainer` and `PublicationOccurrence` as a future Catalog expansion.
- **Lifecycle status vocabulary:** Standardized for `OrigamiWork`, `OrigamiEdition`, and `WorkRelationship`. `Revision` requires no MVP status.
- **Symmetric relationship storage:** `Related To` is stored once, with normalized endpoint ordering and no duplicate reciprocal records.

## Post-Review Amendments

- **External reference scope:** Phase 1 includes `OrigamiWork` as well as `OrigamiEdition`. This amends correction 8 of the Freeze Candidate, so that reference-only works do not need placeholder editions.
- **Work cross-domain references:** `OrigamiWork` may be referenced by `License`, `Technique`, `ExternalReference`, and provenance records.
- **Unknown values:** Language and date values may be explicitly unknown, and dates may be partial or approximate.
- **Description naming:** `OrigamiWork` and `OrigamiEdition` use `description`. `Revision` keeps `summary`, which describes the change.
- **Work merges and splits:** Merged works identify the surviving work through `merged_into_work_id`. Splits are recorded with a `Split From` relationship.
- **Relationship status definitions:** All five Freeze Candidate values are retained and defined by review state.
- **Title resolution:** Display titles resolve through a defined order, a work has at most one `Primary` title, and `OrigamiEdition.title` is optional.
- **Edition publication information:** `publication_statement` records curated publication text until `PublicationOccurrence` is implemented, and `publication_date` means the first known public release.
- **Edition languages:** `OrigamiEdition.language` is replaced by `languages`, a non-empty set of BCP 47 tags with reserved `und` and `zxx` values. `WorkTitle.language` remains a single tag.
- **Revision identity:** At least one of `revision_number` or `revision_label` is required, and each is unique within an edition when supplied.
- **Artifacts on works:** A `DigitalArtifact` may link directly to a work when it is not known to represent a particular edition.
- **Merge and split effects:** Editions and relationships move to the surviving work, and titles are copied. Curation assigns editions during a split.
- **Identity uncertainty:** `Possibly Same As` records an open identity question.
- **Controlled vocabularies:** `edition_type` and `title_type` are controlled, and `OrigamiWork` and `OrigamiEdition` status values are defined.
- **Work-level changes:** `Revision` remains edition-level. Work record changes are preserved through Provenance.
- **Status consistency:** A Published edition requires a Published work.
- **Untitled works:** The `Supplied` title type records curator-assigned titles.
- **Provenance scope:** Provenance may attach to any Catalog entity or value, and competing claims remain in Provenance.
- **Phase mapping:** Phase 1 corresponds to Version 0.1, and the External Reference Domain is part of the MVP.
- **Title resolution fallback:** The final fallback is the earliest-created remaining `WorkTitle`.
- **Work taxonomy references:** `Base` is added to the work's cross-domain references.
- **Domain dependencies:** The FOLD Domain has no direct references to Catalog entities, and text extracted from FOLD files reaches Catalog only through Provenance and curated acceptance. The Notation Domain does not depend on Catalog identity.
- **Multi-subject artifacts:** An artifact may represent several works or editions. The Artifact Domain owns the link and any locator, such as a frame range, page range, or timecode.
- **Recommended paper information:** This is owned by the Instruction Domain.
- **Composite works:** `Component Of` records that one work is a part of another. A part is a separate work only when it has an independent design identity, and quantities and assembly belong to the Instruction Domain.

---

# Remaining Open Questions

- What is the exact `PublicationContainer` subtype hierarchy?
- When should `EditionTitle` be implemented?
- Does `Series` belong in Catalog Phase 2 or later?

These questions do not block:

- The core conceptual definitions.
- The distinction between Catalog revision, Artifact versioning, and Provenance history.
- The boundaries between Catalog and all referencing domains.

---

# Deferred to Other Domain Specifications

- **Artifact Domain:** Define the link between artifacts and Catalog entities, including its locator, and whether an artifact may exist before it is linked to a work or edition.
- **Instruction Domain:** Define whether an instruction step may link to a frame from an artifact linked to a different work or edition.

---

Omnia Vincit Amor
