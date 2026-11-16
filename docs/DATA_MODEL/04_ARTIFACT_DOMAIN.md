# 04_ARTIFACT_DOMAIN.md

## Origami Vault Artifact Domain

**Version:** 1.1

**Status:** Frozen

- Conceptual Entity Pass: Completed
- Architecture Review (Draft 1.0): Passed with conditions
- Review Conditions (Draft 1.1): Resolved; see [Review Corrections](#review-corrections)
- Cross-Domain Review (Version 1.1): Completed

This document resolves the Artifact questions deferred by the Catalog and Provenance Domains.

---

# Purpose

The Artifact Domain preserves the digital files and media that represent origami knowledge.

It keeps each file's exact bytes safe and verifiable, separates originals from anything derived from them, and connects files to the works and editions they represent.

A file may represent knowledge, but it is never the knowledge itself.

---

# Scope

The Artifact Domain answers:

- Which files does Origami Vault preserve?
- What are the exact bytes of this version, and are they still intact?
- Which version is the original, and which versions were corrected or derived?
- Which work or edition does this file represent, and which part of the file is relevant?
- Can this file currently be accessed?

The Artifact Domain does not own:

- Works, editions, or titles.
- Credits, or sources and evidence.
- Licenses or permissions.
- Parsed FOLD data, frames, or FOLD-specific validation rules.
- Instruction steps.
- Citations of content that Origami Vault does not host.

Those concepts are owned outside Artifact and may reference it.

---

# Domain Ownership

The Artifact Domain owns:

- `DigitalArtifact`
- `ArtifactVersion`
- `ArtifactLink`
- `ArtifactDerivation`
- `PreservationEvent`

These concepts from the FOLD research are represented without separate entities:

- `FoldArtifact`, `PdfArtifact`, and `ImageArtifact` are `artifact_type` values.
- `Checksum` is part of `ArtifactVersion`.
- `ExternalArtifact` is covered by the External Reference Domain, because Origami Vault does not hold its bytes.

---

# Core Conceptual Decisions

## A File Is Not the Work

Following Principle 1, `OrigamiWork`, `OrigamiEdition`, and `DigitalArtifact` remain different things. Uploading a file does not create a work or an edition.

## Artifact and Version Are Different

```text
DigitalArtifact
    = one preserved file or media asset, as a stable identity

ArtifactVersion
    = one exact, immutable set of bytes
```

## Preservation Copies Are Never Changed

Following Principle 7, an original upload is never overwritten or altered. A correction, conversion, or preview is a separate version.

Legally required redaction is the only case in which preserved bytes are removed. See [Restriction and Redaction](#restriction-and-redaction).

## Derived Files Are Never Presented as Originals

Every derived version identifies its source, and the process that produced it is recorded in the Provenance Domain.

## A File's Embedded Metadata Is a Claim

Following Principle 5, embedded text such as a PDF author or a photo caption stays in the preserved bytes. Anything extracted from it is a Pending `Claim` in the Provenance Domain.

## Links Say What a File Represents

The statement that a file shows a work is itself a claim, because a file can be misidentified. `ArtifactLink` records that statement and its review status.

## Contributors Only Add a File

Following Principle 18, a contributor provides the file, and nearly everything else is automatic. See [Contributor Experience](#contributor-experience).

---

# Implementation Support Fields

The following fields are implementation support fields rather than purely conceptual attributes. They are listed separately for each entity that uses them.

- `storage_reference`: where the bytes are stored, independent of any storage provider.
- `created_at`: time the record was created.
- `updated_at`: time the record was last updated.

---

# Entity: DigitalArtifact

## Definition

One preserved file or media asset, identified independently of its versions.

## Identity Boundary

`DigitalArtifact` answers:

```text
Which preserved file or asset is this?
```

It does not answer:

```text
Which design is this?
Which publication is this?
```

## Conceptual Attributes

- `id`
- `artifact_type`
- `label`
- `description`
- `current_version_id`
- `status`

`current_version_id` identifies the authoritative version, which is an Original or Corrected version. Presentation may use a suitable Derived version of it, such as a thumbnail. Changing `current_version_id` does not diminish any other version.

## Implementation Support Fields

- `created_at`
- `updated_at`

## Controlled Artifact Types

- Image
- PDF
- Video
- Audio
- FOLD
- Other

The vocabulary may be extended through controlled records rather than arbitrary free text.

## Lifecycle Status

- **Active:** preserved and available for linking.
- **Deprecated:** preserved, but no longer recommended because it is erroneous or superseded.

## Relationships

```text
DigitalArtifact
  1 ─── 1..* ArtifactVersion
  1 ─── 0..* ArtifactLink
```

## Integrity Rules

- An artifact must have at least one version.
- `current_version_id` must identify an Original or Corrected version of the same artifact.
- An artifact may exist without an `ArtifactLink`. An unlinked artifact is preserved, but it is not presented as Catalog knowledge.
- An artifact referenced by another domain, such as step media or a credited photograph, may be presented through that domain.
- Adding a Corrected version, changing `current_version_id`, or adding a link follows the rules for changing published information: the original contributor and curators act directly, and other users suggest a change.
- Deprecating an artifact does not delete its bytes.
- Matching file names must not establish that two artifacts are the same.

---

# Entity: ArtifactVersion

## Definition

One exact, immutable set of bytes belonging to one artifact.

## Conceptual Attributes

- `id`
- `artifact_id`
- `version_number`
- `version_type`
- `checksum_algorithm`
- `checksum_value`
- `file_size`
- `media_type`
- `original_filename`
- `access_status`

The uploader, source, and upload time are recorded in the Provenance Domain, through an Upload activity whose `ActivityLink` identifies the version as its output. Rights are recorded in the Rights Domain.

`version_number` is assigned only to Original and Corrected versions, in order, and is unique within an artifact. Derived versions are identified through their `ArtifactDerivation`.

## Implementation Support Fields

- `storage_reference`
- `created_at`
- `updated_at`

## Controlled Version Types

- **Original:** the bytes as first received.
- **Corrected:** a replacement, such as a better scan of the same material, that does not come from a previous version's bytes.
- **Derived:** produced from another version, such as a thumbnail, conversion, or re-encoded video.

## Controlled Access Status

- **Open:** access is not restricted by this domain.
- **Restricted:** hidden, with the bytes kept.
- **Redacted:** the bytes are permanently removed, and the record remains.

## Relationships

```text
DigitalArtifact
  1 ─── 1..* ArtifactVersion

ArtifactVersion
  1 ─── 0..* ArtifactDerivation (as source)
  1 ─── 0..* ArtifactDerivation (as derived)
  1 ─── 0..* PreservationEvent
```

## Integrity Rules

- A version's bytes must never change after it is stored.
- A checksum is recorded when a version is stored. SHA-256 is the initial algorithm.
- Within one artifact, identical bytes do not create a new version.
- Identical bytes in two artifacts must not merge those artifacts automatically. Storage may share the bytes as an implementation detail.
- A file does not need to pass validation to be preserved.
- Unknown or unsupported data within a file is preserved.
- `media_type` may be unknown. An unrecognized file is preserved, and its artifact has the Other type.
- A Derived version must have at least one `ArtifactDerivation`.
- Replacing `current_version_id` does not remove the previous version.
- Access status changes follow a `CurationDecision`, as defined in the Provenance Domain.
- Restricting or redacting an artifact applies to all its versions.

---

# Entity: ArtifactLink

## Definition

A claim that an artifact, or part of it, represents one work or edition.

## Conceptual Attributes

- `id`
- `artifact_id`
- `artifact_version_id`
- `target_reference`
- `link_role`
- `locator`
- `link_status`

`artifact_version_id` is optional. It is used when the claim concerns one specific version.

`target_reference` identifies an `OrigamiWork` or an `OrigamiEdition`.

`locator` identifies the relevant part of the artifact, such as a frame range, page range, or timecode. It is expressed in the artifact's own terms and is not a reference to a `FoldFrame`.

## Implementation Support Fields

- `created_at`
- `updated_at`

## Controlled Link Roles

- Cover
- Photograph
- Diagram
- Instructional Media
- Geometry
- Source Scan
- Other

The vocabulary may be extended through controlled records rather than arbitrary free text.

## Controlled Link Status

- **Claimed:** recorded from a source or contributor and not yet reviewed.
- **Unverified:** reviewed, but the available evidence is insufficient to confirm or reject the link.
- **Verified:** reviewed and confirmed by the available evidence.
- **Disputed:** reviewed and subject to credible conflicting evidence.
- **Deprecated:** retired from current use and preserved as a historical record.

These values match `WorkRelationship.relationship_status`.

## Relationships

```text
DigitalArtifact
  1 ─── 0..* ArtifactLink

ArtifactLink
  * ─── 1 OrigamiWork or OrigamiEdition
```

## Integrity Rules

- Following the Catalog Domain, an artifact links to an edition when it is known to represent that edition, and otherwise may link directly to a work.
- A link to a work does not establish an edition.
- An artifact may have several links, such as a book scan showing many works.
- Conflicting links may coexist while their evidence and status remain distinguishable.
- A link must not be created from a file's name or embedded metadata alone without acceptance.
- Status changes follow a `CurationDecision`.
- The same artifact, target, and link role should not be recorded twice.
- A target has at most one Cover link that is not Deprecated.
- When a file linked to a work is found to represent a specific edition, an edition link is added and the work link becomes Deprecated. Both remain preserved.
- A link to a merged work keeps its original reference and resolves through `merged_into_work_id`. Duplicate links, including a second Cover, are reconciled during curation.
- When a work is split, curation decides which resulting work each link applies to.

---

# Entity: ArtifactDerivation

## Definition

A statement that one artifact version was produced from another.

## Conceptual Attributes

- `id`
- `source_version_id`
- `derived_version_id`
- `derivation_type`
- `activity_id`

`activity_id` refers to the `ProvenanceActivity` that holds the tool, parameters, warnings, and losslessness.

## Implementation Support Fields

- `created_at`

## Controlled Derivation Types

- Thumbnail
- Preview
- Format Conversion
- Re-encoding
- Extraction
- Metadata Removal
- Visualization
- Other

The vocabulary may be extended through controlled records rather than arbitrary free text.

## Relationships

```text
ArtifactDerivation
  * ─── 1 ArtifactVersion (source)
  * ─── 1 ArtifactVersion (derived)
  * ─── 1 ProvenanceActivity
```

## Integrity Rules

- A derived version may have several sources, and a source may have several derived versions.
- Source and derived versions must be different, and derivations must not form a cycle.
- A derivation must not alter its source.
- Restricting or redacting a source also restricts or redacts the versions derived from it, unless a person decides that a derived version does not contain the affected content.

---

# Entity: PreservationEvent

## Definition

A record of an action or check on the preservation of an artifact version.

## Purpose

Preservation events do not change knowledge, so they are separate from `ProvenanceActivity`. They show whether preserved bytes are still intact.

## Conceptual Attributes

- `id`
- `artifact_version_id`
- `event_type`
- `result`
- `event_time`
- `details`

## Implementation Support Fields

- `created_at`

## Controlled Event Types

- Fixity Check
- Replication
- Restoration
- Format Migration

## Controlled Results

- Succeeded
- Failed

## Relationship

```text
ArtifactVersion
  1 ─── 0..* PreservationEvent
```

## Integrity Rules

- A Fixity Check compares the stored bytes with the recorded checksum.
- A failed Fixity Check must remain preserved and must be brought to a maintainer's attention.
- A Restoration must not produce bytes whose checksum differs from the recorded checksum.

---

# Restriction and Redaction

The Artifact Domain applies the process defined in the Provenance Domain:

- **Restrict:** the bytes are hidden and kept.
- **Redact:** the bytes are permanently removed, only when removal is legally required.

Either action also applies to versions derived from the affected bytes, unless a person decides that a derived version does not contain the affected content.

A redacted version keeps its record, including its version number, if it has one, and its checksum. Its `original_filename`, and the artifact's `label` and `description`, are removed or edited when they contain the content or personal data being removed.

Rights decide whether a file may be shared. The Artifact Domain only records and applies the resulting access status.

## Presenting Files

A photo or other file may contain personal metadata, such as a location. Public presentation uses Metadata Removal derived versions, while the original bytes remain preserved.

---

# Contributor Experience

Following Principle 18, a contributor adds a file through a single step:

```text
Add a photo or file
```

## Field Classification

| Field | Classification | Notes |
|---|---|---|
| The file | Worth asking | The only required action |
| What the file shows | Worth asking only when unclear | A plain question for files other than a first photo |
| `description` and `label` | Optional | `label` defaults to the file name |
| `locator` | Optional | Offered for long files, and normally added by curators |
| `artifact_type`, `media_type`, and `file_size` | Automatic | Detected from the file |
| `checksum_algorithm` and `checksum_value` | Automatic | Calculated on storage |
| `version_number` and `version_type` | Automatic | Set when the file is stored |
| `original_filename` | Automatic | Taken from the upload |
| `link_role` | Automatic | A first photo becomes Cover, and curators may correct it |
| `target_reference` | Automatic | Follows the work being entered |
| `link_status` | Automatic | Starts as Claimed |
| Derived versions and preservation events | Automatic | Created by the system |

Rights questions belong to the Rights Domain, and the same wizard may ask them.

---

# Consolidated Relationships

```text
DigitalArtifact
  1 ─── 1..* ArtifactVersion
  1 ─── 0..* ArtifactLink

ArtifactVersion
  * ─── 1 DigitalArtifact
  1 ─── 0..* ArtifactDerivation (as source)
  1 ─── 0..* ArtifactDerivation (as derived)
  1 ─── 0..* PreservationEvent

ArtifactLink
  * ─── 1 DigitalArtifact
  * ─── 0..1 ArtifactVersion

ArtifactDerivation
  * ─── 1 ArtifactVersion (source)
  * ─── 1 ArtifactVersion (derived)
```

Cross-domain references:

```text
ArtifactLink
  → OrigamiWork
  → OrigamiEdition

ArtifactDerivation
  → ProvenanceActivity

Other domains
  ├── Credit → DigitalArtifact
  ├── Source and ValidationReport → ArtifactVersion
  ├── FoldDocument → ArtifactVersion
  └── InstructionStep media → DigitalArtifact
```

Following the Catalog Domain's rule, each cross-domain link is owned by the referencing domain.

---

# Domain Boundaries

| Concern | Owner | Artifact role |
|---|---|---|
| Who uploaded a file, and when | Provenance | References the activity through derivations and history |
| Tool, parameters, and warnings for a conversion | Provenance | Holds the derivation link |
| Named revision of an edition | Catalog | Holds the file versions that may accompany it |
| Who created a photo or diagram | Attribution | Is the target of a credit |
| Whether a file may be shared | Rights | Applies the resulting access status |
| Parsed FOLD data and rules | FOLD | Holds the preserved bytes |
| Content that is not hosted | External Reference | None |
| Storage provider | Implementation | Remains independent of it |

---

# Conceptual Integrity Summary

```text
DigitalArtifact ≠ OrigamiEdition ≠ OrigamiWork

DigitalArtifact is a stable identity;
ArtifactVersion is exact bytes.

Revision belongs to OrigamiEdition;
ArtifactVersion belongs to DigitalArtifact.

Original ≠ Derived;
a derived file never replaces its source.

Identical bytes are one version within an artifact;
a matching file is not a matching identity.

Preservation does not grant permission;
a preserved file is not automatically shared.
```

---

# Validation Scenarios

| Scenario | How it is recorded |
|---|---|
| A contributor adds a first photo | One artifact, an Original version, a Claimed Cover link, and an automatic thumbnail. |
| A curator uploads a better scan | A Corrected version of the same artifact, with the earlier version preserved. |
| A corrected file changes the edition's expression | Curation also considers a `Revision` or new edition in the Catalog Domain. |
| A thumbnail or video re-encoding is generated | A Derived version with an `ArtifactDerivation` and a Provenance activity. |
| A FOLD file is converted to another format | A Derived version. The original FOLD file is unchanged. |
| The same bytes are uploaded to one artifact again | No new version is created. |
| Two people upload the same file separately | Two artifacts remain until curation decides otherwise. |
| A file is uploaded before a work exists | The artifact exists, unlinked, and is not presented as Catalog knowledge. |
| A photo of an unidentified edition exists | The artifact links directly to the work. |
| A book scan shows 20 works | One artifact with 20 links, each with a page-range locator. |
| A FOLD file has the `multiModel` class | One artifact with several links, each with a frame-range locator. |
| A FOLD file fails validation | The artifact remains preserved, and Provenance holds the report. |
| A file is linked to the wrong work | The link becomes Deprecated, and a correct link is added. |
| A copyright complaint is received | A decision restricts access, and the bytes are kept. |
| A court orders a file removed | The bytes and affected derived versions are redacted, and the records remain. |
| A photo contains a home location | Public presentation uses a derived version without it. |
| A periodic check finds corruption | A failed Fixity Check is recorded and raised to a maintainer. |
| A restricted file has a thumbnail | The thumbnail is restricted too, unless a person decides it does not contain the affected content. |
| A curator uploads a second real version after several thumbnails exist | It becomes version 2, because Derived versions are not numbered. |
| A photo linked to a work is found to show a specific edition | An edition link is added, and the work link becomes Deprecated. |
| Two works with photos are merged | Links resolve to the surviving work, and curation reconciles the two Covers. |
| A work with a linked photo is split | Curation decides which resulting work the link applies to. |
| An unrecognized file type is uploaded | It is preserved with the Other type and an unknown media type. |

---

# MVP Scope

MVP Artifact entities:

- `DigitalArtifact`
- `ArtifactVersion`
- `ArtifactLink`
- `ArtifactDerivation`
- `PreservationEvent`

In the MVP, `PreservationEvent` may be limited to Fixity Check events.

---

# Future Expansion

- Separate entities for format-specific information, if they prove necessary.
- Several checksum algorithms for one version.
- Detailed replication and backup procedures.
- Format migration planning.
- Curated merging of duplicate artifacts.
- Storage limits and retention policies.

---

# Key Conceptual Answers

## Does Uploading a File Create a Work?

No. An artifact may exist without a link, and a work requires its own record.

## Is a Thumbnail a New Artifact?

No. It is a Derived version of the same artifact, linked to its source by an `ArtifactDerivation`.

## What Is the Difference Between a Version and a Revision?

An `ArtifactVersion` is exact bytes. A `Revision` is a named or significant change to an edition, owned by the Catalog Domain.

## Can a Preserved File Be Removed?

Access can be restricted. Bytes are removed only when legally required, and the record remains.

## Why Are Preservation Events Not Provenance Activities?

Provenance activities explain changes to knowledge. A fixity check changes no knowledge; it confirms that the bytes are still intact.

---

# Review Corrections

Applied after the Draft 1.0 architecture review:

- **Current version:** `current_version_id` identifies an Original or Corrected version, and presentation may use a Derived version of it.
- **Restriction of derived versions:** Restriction, like redaction, reaches derived versions unless a person decides otherwise.
- **Merges and splits:** Links resolve through `merged_into_work_id`, and curation assigns links after a split.
- **Metadata removal:** `Metadata Removal` is a derivation type.
- **Version numbers:** Only Original and Corrected versions are numbered, and numbers are unique within an artifact.
- **Redaction scope:** Redaction also reaches `label` and `description`.
- **Upload history:** An Upload activity identifies the version through `ActivityLink`.
- **Duplicate links:** Equivalent links are not repeated, and a target has at most one current Cover.
- **Newly identified editions:** An edition link replaces a work link, and both remain preserved.
- **Unrecognized files:** `media_type` may be unknown, and the file is preserved with the Other type.
- **Support fields:** `PreservationEvent` has `created_at`, and `ArtifactVersion` has `updated_at`.

Applied after the cross-domain review:

- **Artifacts used by other domains:** An unlinked artifact may be presented through the domain that references it.
- **Changing files:** Corrected versions, current versions, and links follow the rules for changing published information.
- **Visualizations:** `Visualization` is a derivation type.

---

# Remaining Open Questions

- How do redaction and account deletion apply to backups? This is shared with the Provenance Domain.
- How often should Fixity Checks run?

These questions do not block the conceptual definitions in this document.

---

# Deferred to Other Domain Specifications

- **Rights Domain:** Define permissions for sharing files, including whether original bytes may be publicly downloaded, and what happens to a published file when a contributor has not answered the rights question.
- **FOLD Domain:** Define how FOLD documents and frames refer to an `ArtifactVersion`.
- **Instruction Domain:** Define how step media refers to an artifact, and whether a step may use a frame from an artifact linked to a different work or edition.

---

Omnia Vincit Amor
