# Architecture

## Purpose

This document defines the high-level architecture of Origami Vault.

Its purpose is to guide technical decisions while supporting the project's mission:

> Preserve, share, celebrate, and advance the knowledge and beauty of origami.

This document focuses on architectural principles, system structure, responsibilities, and long-term design decisions rather than implementation details.

Architecture decisions should support both current platform goals and future possibilities while prioritizing simplicity, maintainability, interoperability, and preservation.

---

# Architectural Principles

## Preservation First

Origami Vault exists primarily to preserve origami knowledge.

Technology choices should prioritize:

- Long-term accessibility
- Data portability
- Knowledge preservation
- Sustainability
- Maintainability

Knowledge should remain usable even as technologies change.

---

## Knowledge-Centric Design

Origami Vault is not primarily a website, application, or file repository.

Origami Vault is a platform dedicated to preserving origami knowledge.

Web applications, APIs, visualization systems, importers, exporters, simulations, educational experiences, research tools, and future technologies should be viewed as interfaces to the underlying knowledge base.

Architecture decisions should prioritize preservation and portability of knowledge over convenience of implementation.

---

## Open Standards

Whenever practical, Origami Vault should support and encourage open standards.

The project should:

- Avoid unnecessary vendor lock-in
- Encourage interoperability
- Build on existing community standards
- Preserve compatibility whenever possible

---

## Interoperability

Origami Vault should exchange information with external systems.

Examples include:

- FOLD-compatible tools
- ORIPA
- Ori Studio
- Educational platforms
- Research systems
- Future origami software ecosystems

---

## Progressive Complexity

The simplest solution that satisfies current requirements should be preferred.

Advanced technologies should only be introduced when justified by value.

Examples include:

- Simulation
- Geometry generation
- Structured fold languages
- Robotics integration
- AI-assisted workflows

---

## Creator-Friendly Design

Architecture decisions should reduce friction for contributors.

The platform should support multiple contribution workflows and accommodate creators with varying levels of technical expertise.

---

# System Overview

Origami Vault should be organized around a central knowledge repository.

```text
                   Users
                     │
                     ▼
          Web Application (Rails)
                     │
                     ▼
                 REST API
                     │
                     ▼
             Knowledge Layer
                     │
                     ▼
                PostgreSQL
                     │
                     ▼
              Object Storage
```

Future clients:

```text
Godot Client

Mobile Applications

Educational Applications

Research Tools

Third-Party Integrations

Future Visualization Systems
```

All clients should interact through APIs rather than directly accessing persistence layers.

---

# Repository Structure

```text
origami-vault

├── docs
│   ├── DATA_MODEL
│   └── research
├── backend
│   └── rails-api
├── frontend
│   └── godot-client
├── shared
│   ├── schemas
│   └── standards
├── prototypes
└── assets
```

The repository will initially be maintained as a monorepo.

---

# Deployment Strategy

## Initial Deployment

Origami Vault will begin as a modular monolith.

The Rails application will contain:

- Authentication and user accounts
- Domain modules (Catalog, Attribution, Rights, Instruction, Artifact, FOLD, Provenance, External Reference, Taxonomy)
- Contribution and curation workflows
- Search
- APIs
- Administration

Module boundaries should follow domain boundaries so that domains can be separated later if needed.

This reduces operational complexity and encourages rapid iteration.

---

## Future Scaling

Future service separation may occur when justified by:

- Performance requirements
- Team growth
- Operational needs
- Community size

Premature microservices should be avoided.

---

# Domain Architecture

Origami Vault centers around preserving origami knowledge.

The canonical knowledge model is defined in:

```text
docs/DATA_MODEL/00_DATA_MODEL_PRINCIPLES.md
```

This section summarizes how that model shapes the architecture. Where the two documents differ, the data model documents are authoritative.

---

## Core Distinction

The architecture is built around three distinct concepts:

```text
OrigamiWork
    = conceptual design
        │
        ▼
OrigamiEdition
    = distinct published, translated, reconstructed, adapted,
      or otherwise curated expression of that design
        │
        ▼
DigitalArtifact
    = file, media asset, or digital representation
```

A work is not a file.

An edition is not a file.

A file may represent a work or edition, but it is never the work itself.

---

## Identity and Attribution

User accounts and attribution identities are independent:

```text
User ≠ Party
```

- A **User** is an account that authenticates and participates in the platform.
- A **Party** is a person or organization that can receive credit.

A historical designer may exist only as a Party.

A community participant may exist only as a User.

A participating designer may be both.

---

## Domains

The knowledge model is organized into domains. Each domain owns the concepts it defines, and other domains reference those concepts rather than redefining them.

```text
Origami Vault
├── Catalog Domain              (MVP)
├── Attribution Domain          (MVP)
├── Rights Domain               (MVP)
├── Instruction Domain          (MVP)
├── Artifact Domain             (MVP)
├── FOLD Domain                 (MVP)
├── Provenance Domain           (MVP)
├── Taxonomy Domain             (MVP: categories and tags)
├── Notation Domain
├── External Reference Domain   (MVP)
└── Community Domain
```

| Domain             | Owns                                                                            |
|--------------------|---------------------------------------------------------------------------------|
| Catalog            | Works, editions, titles, relationships between works, and edition-level revisions |
| Attribution        | Parties, credits, contribution roles, attribution evidence and status           |
| Rights             | Licenses, permissions, and rights statements                                    |
| Instruction        | Instructional sequences, steps, actions, annotations, and learning metadata     |
| Artifact           | Preserved files, versions, derivatives, checksums, and preservation copies      |
| FOLD               | Parsed, validated, and interpreted FOLD-specific information                    |
| Provenance         | Sources, imports, derivations, transformations, validation and curation history |
| Taxonomy           | Techniques, bases, classifications, and controlled vocabularies                 |
| Notation           | Notation systems, meanings, variants, and conventions                           |
| External Reference | Citations, bibliographic references, and external identifiers                   |
| Community          | Collections, contributions, completed folds, and collaboration                  |

Detailed specifications for each domain will be maintained in `docs/DATA_MODEL/`.

---

## Instruction and Geometry

Instructional knowledge and geometric representation are modeled separately:

```text
InstructionStep ≠ FoldFrame
```

An instruction step may link to one or more geometric states, but geometry alone does not define learner actions, pedagogical intent, warnings, tips, or sequencing.

---

# Knowledge Preservation Strategy

Knowledge should never be tightly coupled to a specific file format.

Files are representations of knowledge.

Knowledge itself should remain independently preserved whenever practical.

Examples:

- Images
- PDFs
- FOLD files
- Videos

should be viewed as representations of preserved information rather than the information itself.

Whenever practical, important information should be captured in structured forms.

Structured information should survive even if source files become unavailable.

---

# Backend Architecture

## Technology

Primary Backend:

- Ruby on Rails

Primary Database:

- PostgreSQL

---

## Responsibilities

The backend is the authoritative source of truth.

Responsibilities include:

- Authentication
- Authorization
- User account management
- Catalog management (works and editions)
- Attribution management (parties and credits)
- Rights management
- Instruction management
- Artifact preservation and file management
- FOLD parsing and validation
- Provenance recording
- External reference management
- Taxonomy management (categories and tags)
- Revision management
- Search
- API delivery

---

# Authentication Strategy

Initial authentication:

- Email/password accounts

Future authentication:

- GitHub
- Google
- Microsoft

Authentication should remain loosely coupled from domain models.

User accounts are not attribution identities. A User may be linked to a Party, but credit is always recorded against the Party in the Attribution Domain.

---

# Frontend Architecture

## Web Platform

Primary interface.

Responsibilities:

- Search
- Browsing
- Discovery
- Community features
- Creator workflows
- Administration

---

## Visualization Clients

Origami Vault may provide multiple visualization experiences.

Potential clients include:

- Rails web application
- Godot desktop application
- Mobile applications
- Educational experiences
- Research tooling

The architecture should remain independent of any specific client technology.

---

## Godot Client

Godot is currently the preferred platform for advanced visualization.

Potential responsibilities:

- Interactive model viewing
- Fold visualization
- Educational experiences
- Experimental interfaces
- Future simulation systems

The Godot client should consume APIs and never directly access databases.

---

# Data Architecture

## Conceptual Model and Persistence

The conceptual data model is the canonical description of Origami Vault knowledge.

Database schemas, Rails models, API payloads, and file formats are implementations of that model, not definitions of it.

Implementation rules:

- Each domain in the Domain Architecture should map to a clearly bounded module within the Rails application.
- Cross-domain relationships should be explicit references, not duplicated data.
- Each concept should have a single authoritative record within its owning domain.
- Persistence choices may change over time without changing the conceptual model.

---

## Claims, Provenance, and Unknown Values

The persistence layer must be able to represent:

- **Claims**: imported information preserved as source data until it is accepted. Direct entry by an authorized contributor counts as acceptance, with its provenance recorded, and acceptance does not mean verification.
- **Provenance**: where each important value came from and how it was produced (uploaded, imported, inherited, computed, converted, inferred, curated, or verified).
- **Unknown values**: explicit records that information is unknown, rather than empty fields or assumed values.
- **Conflicting claims**: multiple claims about the same fact, preserved side by side with their evidence.

Verification status should remain separate from claim existence.

---

## Domain Specifications

Detailed entity definitions, fields, and relationships are maintained per domain:

```text
docs/DATA_MODEL/
├── 00_DATA_MODEL_PRINCIPLES.md
├── 01_CATALOG_DOMAIN.md
└── ...
```

---

# Metadata Strategy

Metadata should be treated as first-class information.

Examples:

- Titles (Catalog)
- Descriptions (Catalog)
- Credits and attribution (Attribution)
- Licensing (Rights)
- Techniques, bases, and classifications (Taxonomy)
- Difficulty and recommended paper (Instruction)
- Source references (External Reference)
- Historical information (Provenance)

Each metadata concept is owned by a single domain.

Metadata should be:

- Searchable
- Queryable
- Exportable
- Versionable
- Traceable to its source
- Preserved long term

Imported metadata is a claim, not authoritative knowledge. It should be preserved as source data and may later become curated or verified.

Metadata is progressive. Minimum information should remain small, and unknown values are valid states.

---

# Search Strategy

Search is a core platform capability.

## Version 0.1

- PostgreSQL full-text search

## Future Evaluation

- Elasticsearch
- OpenSearch
- Meilisearch

Search infrastructure should remain replaceable without significant changes to business logic.

---

# Media Storage Strategy

Metadata and media should remain separated.

## PostgreSQL

Stores:

- Records
- Metadata
- Relationships
- Revisions
- Attribution
- References

## Object Storage

Stores:

- Images
- PDFs
- FOLD files
- Diagrams
- Future format files

Potential providers:

- Cloudflare R2
- Backblaze B2
- AWS S3

Development environments may use local storage.

---

# Import and Export Architecture

Origami Vault should support multiple methods of importing and exporting knowledge.

Examples:

- Manual entry
- Image collections
- PDF instructions
- FOLD files
- External references

Importers and exporters should remain separate from domain models.

Internal knowledge representations should remain stable even as supported formats evolve.

---

# Standards Layer

## FOLD Strategy

Origami Vault recognizes FOLD (Flexible Origami List Datastructure) as a primary machine-readable interchange format within the computational origami ecosystem.

The platform shall:

- Treat FOLD files as first-class digital artifacts.
- Preserve original FOLD artifacts immutably.
- Preserve declared FOLD specification versions.
- Preserve supported and unknown namespaced properties whenever practical.
- Record parsing, validation, conversion, and transformation provenance.
- Support interoperability with existing origami software ecosystems.

FOLD is an important representation format, but it is not the canonical representation of origami knowledge within Origami Vault.

The internal knowledge model must remain independent of any single external format.

For detailed analysis and implementation guidance, see:

```text
docs/research/FOLD_FORMAT_RESEARCH.md
```

Supported formats may include:

- FOLD
- ORIPA exports
- Future community standards

Importers and exporters should translate between external formats and internal knowledge models.

The platform should favor extension and interoperability over replacement whenever practical.

---

# API Strategy

Origami Vault should adopt an API-first mindset.

Initial recommendation:

- REST APIs

Future evaluation:

- GraphQL

API resources should follow domain boundaries.

Initial API areas may include:

- Catalog (works, editions)
- Attribution (parties, credits)
- Rights (licenses)
- Instruction (instructions, steps)
- Artifacts (files, derivatives)
- FOLD (parsed and validated FOLD data)
- Provenance
- External References
- Taxonomy (categories, tags)
- Search

Future API areas may include Notation and Community.

APIs should expose provenance and verification status alongside the data they describe.

Future clients should not require architectural redesign.

---

# AI Assistance Strategy

Artificial intelligence should assist creators and curators rather than replace them.

Potential future uses include:

- Metadata generation
- Instruction extraction
- Image organization
- Format conversion
- Validation assistance

AI-generated information should:

- Remain reviewable
- Remain editable
- Never become authoritative without human oversight

Humans remain responsible for published content.

---

# Revision Strategy

Knowledge preservation includes preserving history.

Future versions should support:

- Edition revisions
- Work record change history, preserved through provenance
- Metadata revisions
- Instruction revisions
- Attribution history
- Historical snapshots

Version history should be considered part of the preserved knowledge.

Superseded interpretations may remain valuable and should not be destroyed. Curated conclusions should not erase the claims and evidence they were based on.

---

# Artifact Preservation Architecture

Origami Vault distinguishes between:

- Source artifacts
- Parsed representations
- Normalized metadata
- Validation results
- Derived artifacts

Original uploaded files represent preserved historical records and should never be modified after ingestion.

Derived artifacts such as:

- Previews
- Generated meshes
- Conversions
- Thumbnails
- Visualizations

should be stored separately and linked through provenance relationships.

FOLD artifacts, PDFs, images, videos, and future formats should all be treated as preserved source artifacts.

Interpretations, normalized metadata, validation results, previews, converted files, and future simulation outputs should be stored separately as derived representations linked through provenance records.

---

# Provenance Architecture

Every significant transformation within Origami Vault should be traceable.

Examples include:

- Format conversion
- Metadata extraction
- Validation
- Geometry generation
- Future simulation workflows

Transformation records should preserve:

- Source artifact
- Output artifact
- Tool used
- Tool version
- Timestamp
- Warnings
- Validation results

Preservation of provenance is essential to the long-term mission of preserving origami knowledge.

---

# Preservation Strategy

Origami Vault should prioritize long-term preservation.

Future systems should support:

- Database backups
- Media backups
- Exportable datasets
- Format-independent preservation

Knowledge should never become solely dependent upon proprietary systems whenever practical.

---

# Future Architectural Directions

These capabilities are intentionally outside the MVP:

- Fold simulation
- Structured fold languages
- Machine-readable folding workflows
- Geometry generation
- Blender export
- STL generation
- Public SDKs
- Educational tooling
- Research platforms
- AR/VR experiences
- Robotic folding systems

Current architectural decisions should avoid making these possibilities impossible.

---

# Architectural Success Criteria

The architecture is successful if it:

- Preserves knowledge effectively
- Remains understandable
- Supports contributors
- Supports interoperability
- Supports future innovation
- Scales responsibly
- Remains maintainable by future contributors

---

# Guiding Principle

The most important asset of Origami Vault is not its code.

It is the knowledge that the platform preserves.

Formats such as FOLD are valuable preservation and interoperability tools, but they remain representations of origami knowledge rather than the knowledge itself.

Architecture decisions should preserve the distinction between knowledge, representations, and derived interpretations.

Every architectural decision should prioritize preservation, clarity, interoperability, accessibility, and long-term sustainability.

---

Created by Sócrates Medina

Bogotá, Colombia

2026

Omnia Vincit Amor

A phrase that accompanied me at my high school graduation and continues to remind me why projects worth building are often born from passion, curiosity, and love for creation.