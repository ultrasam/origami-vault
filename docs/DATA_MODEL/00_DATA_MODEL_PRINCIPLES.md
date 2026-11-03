# 00_DATA_MODEL_PRINCIPLES.md

## Origami Vault Data Model Principles

**Version:** Draft 1.2

**Status:** Foundational Specification

---

# Purpose

Origami Vault is a platform for discovering, sharing, preserving, and learning origami.

The conceptual data model exists to preserve origami knowledge independently of any specific implementation technology, storage model, application, interchange format, or user interface.

The model is intended to:

- Represent origami knowledge.
- Preserve works, editions, instructions, and artifacts.
- Support attribution and rights management.
- Support preservation and provenance.
- Support educational instruction.
- Support community participation.
- Support future geometry, simulation, and interoperability features.

The conceptual model is the canonical description of Origami Vault knowledge.

---

# Architectural Principles

## Principle 1: OrigamiWork, OrigamiEdition, and DigitalArtifact Are Different Things

```text
OrigamiWork
    = conceptual design

OrigamiEdition
    = distinct published, translated, reconstructed, adapted,
      or otherwise curated expression of that design

DigitalArtifact
    = file, media asset, or digital representation
```

A work is not a file.

An edition is not a file.

A file may represent a work or edition, but it is never the work itself.

---

## Principle 2: Knowledge Is Independent of Format

The internal knowledge model must remain independent of any single external format.

Supported formats may include:

- FOLD
- PDF
- SVG
- PNG
- JPEG
- Video formats
- Future interchange formats

The existence, limitations, or structure of a format must not determine the conceptual structure of Origami Vault.

---

## Principle 3: Instruction Is Separate from Geometry

Educational instruction and geometric representation are related but distinct concepts.

```text
InstructionStep ≠ FoldFrame
```

An instructional step may link to one or more geometric states.

A geometric state does not automatically define:

- learner actions
- pedagogical intent
- warnings
- tips
- annotations
- camera guidance
- instructional sequencing

Instruction and geometry remain independently modeled domains.

---

## Principle 4: FOLD Is a First-Class Artifact, Not the Canonical Model

FOLD is an important origami interchange format and preservation artifact.

However:

```text
Origami Vault Model ≠ FOLD File
```

Origami Vault preserves FOLD artifacts, parses them, validates them, derives interpretations from them, and links them to knowledge records.

FOLD remains one representation among many.

---

## Principle 5: Imported Metadata Is a Claim

Imported metadata must never automatically become authoritative knowledge.

Examples:

- Imported title text
- Imported author text
- Imported classifications
- Imported descriptions

Imported information should be preserved as source data and may later become curated or verified knowledge.

Rules:

- Imported information remains a claim until it is explicitly accepted.
- Direct entry by an authorized contributor counts as acceptance, with its provenance recorded.
- Acceptance does not mean verification. Verification status remains separate.
- A domain may keep explicit claims as its own records when those claims are its subject matter, as the Catalog Domain does with work relationships. An import alone never creates such a record.

---

## Principle 6: Provenance Must Be Preserved

Every important derived interpretation should retain provenance.

Examples:

- Uploaded values
- Imported values
- Inherited values
- Computed values
- Converted values
- Inferred values
- Curated values
- Verified values

Users and maintainers should be able to determine where information came from and how it was produced.

---

## Principle 7: Preservation Is Preferred Over Destructive Transformation

Original uploaded artifacts are preservation records.

Rules:

- Preserve original files.
- Never overwrite preservation copies.
- Store derived representations separately.
- Record transformations and conversions.
- Preserve unknown data whenever practical.

Information discarded during import cannot be recovered later.

---

## Principle 8: Attribution Is First-Class Knowledge

Authorship, contribution, and participation are independent concepts.

Origami Vault distinguishes among:

- Party receiving credit
- Contribution role
- Attribution target
- Attribution source
- Attribution confidence
- Attribution status

Attribution is modeled explicitly rather than embedded inside content records.

---

## Principle 9: User Identity and Attribution Identity Are Independent

```text
User ≠ Party
```

Examples:

```text
Historical designer
    Party only

Community participant
    User only

Participating designer
    User and Party
```

Authentication and preserved authorship are intentionally separate concerns.

---
## Principle 10: Progressive Metadata Is Allowed

Knowledge may be incomplete.

Minimum publication information should remain small.

Additional metadata can be added over time.

Unknown values are valid states.

Incomplete information should not prevent preservation.

---

## Principle 11: Community Features Support Preservation

Community functionality exists to support:

- discovery
- learning
- contribution
- verification
- preservation
- collaboration

Popularity should not determine preservation value.

---

## Principle 12: Structured Metadata Is Optional Where Appropriate

Creators and contributors should not be required to provide every possible metadata field.

Examples of optional enrichment:

- notation metadata
- detailed attribution evidence
- geometry links
- external references
- difficulty assessments

The model should support rich metadata without requiring it.

---

## Principle 13: Domains Own Concepts

Each concept has a primary owning domain.

Rules:

- Domains own the concepts they define.
- Other domains may reference those concepts.
- Concepts should not be duplicated across multiple domains.
- Cross-domain relationships should be explicit.

Examples:

- Technique is owned by the Taxonomy Domain.
- Credit is owned by the Attribution Domain.
- NotationMeaning is owned by the Notation Domain.

---

## Principle 14: Canonical Ownership

Every significant concept should have a single authoritative source within the model.

Examples:

- Work titles belong to the Catalog Domain.
- Credits belong to the Attribution Domain.
- Techniques belong to the Taxonomy Domain.
- Notation meanings belong to the Notation Domain.

Domains may reference these concepts but should not redefine them.

---

## Principle 15: Knowledge Is Versioned

Knowledge evolves over time.

Rules:

- Important revisions should be preserved.
- Historical interpretations may remain valuable even when superseded.
- Changes should be traceable.
- Revision history should be retained whenever practical.

---

## Principle 16: Unknown Is a Valid State

The absence of knowledge is itself meaningful information.

Examples:

- Unknown designer
- Unknown publication date
- Unknown license
- Unknown base

Unknown values should be preserved explicitly rather than replaced with assumptions.

---

## Principle 17: Conflicting Claims May Coexist

Origami history and attribution may contain conflicting evidence.

Rules:

- Multiple claims may be preserved simultaneously.
- Claims should retain provenance.
- Curated conclusions should not destroy historical evidence.
- Verification status should remain separate from claim existence.

Examples include attribution disputes, publication history disputes, and competing historical interpretations.

---

# Domain Overview

```text
Origami Vault
├── Catalog Domain
├── Attribution Domain
├── Rights Domain
├── Instruction Domain
├── Artifact Domain
├── FOLD Domain
├── Provenance Domain
├── Taxonomy Domain
├── Notation Domain
├── External Reference Domain
└── Community Domain
```

---
# Domain Responsibilities

## Catalog Domain

Represents conceptual origami knowledge, publication structure, work identity, editions, titles, relationships between works, and edition-level revisions.

## Attribution Domain

Represents identity, authorship, credit, contribution, attribution evidence, and attribution status.

## Rights Domain

Represents licensing, permissions, rights statements, and future rights-management metadata.

## Instruction Domain

Represents educational and pedagogical knowledge, instructional sequencing, actions, references to techniques, annotations, media, and learning-oriented metadata.

## Artifact Domain

Represents preserved digital assets, versions, derivatives, checksums, preservation copies, and artifact lifecycle management.

## FOLD Domain

Represents parsed, validated, and interpreted FOLD-specific information while remaining separate from the canonical knowledge model.

## Provenance Domain

Represents source evidence, import history, derivations, transformations, validation results, curation history, and traceability.

## Taxonomy Domain

Represents reusable classifications, controlled vocabularies, terminology, techniques, bases, and categorization structures.

## Notation Domain

Represents notation semantics, notation systems, notation meanings, notation variants, and instructional conventions.

## External Reference Domain

Represents citations, bibliographic references, external identifiers, and supporting sources outside Origami Vault.

## Community Domain

Represents participation, collections, contributions, completed folds, social interactions, collaboration, and future reputation systems.

---

# MVP and Future Scope

## MVP Priorities

The MVP should prioritize preservation, cataloging, attribution, instructional content, provenance, and interoperability.

Primary focus domains:

- Catalog
- Attribution
- Rights
- Instruction
- Artifact
- FOLD
- Provenance
- External Reference
- Taxonomy (categories and tags)

## Future Expansion

Future releases may expand:

- Community features
- Taxonomy techniques and bases
- Reputation systems
- Achievement systems
- Advanced notation support
- Geometry editing
- Simulation support
- Additional interchange formats
- Enhanced research and preservation workflows

---

# Final Architectural Decision

Origami Vault preserves source artifacts, derives interpretations from them, records provenance for every transformation, and maintains a knowledge model independent of any single external format.

FOLD is a first-class preservation artifact and interoperability format, but it is not the canonical representation of origami knowledge.

Omnia Vincit Amor