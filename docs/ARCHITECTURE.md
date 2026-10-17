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

- Authentication
- User management
- Creator tools
- Search
- Metadata management
- Collections
- APIs
- Administration

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

Current core hierarchy:

```text
Creator
    │
    ▼
Origami Model
    │
    ▼
Instructions
    │
    ▼
Instruction Steps
    │
    ▼
Media
```

Supporting entities:

```text
Users
Collections
Licenses
References
Format Files
Revisions
Tags
Categories
```

Future entities may emerge as the platform evolves.

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
- User management
- Creator management
- Model management
- Revision management
- Metadata management
- Search
- File management
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

## Core Domains

### Users

Platform members.

### Creators

Designers, artists, organizations, and contributors.

Creators may or may not be registered users.

### Models

Origami creations and associated metadata.

### Instructions

Folding knowledge.

### Steps

Individual instructional stages.

### Media

Images, PDFs, diagrams, videos, and supporting files.

### Collections

Curated groupings of models.

### References

External resources and citations.

### Licenses

Ownership and usage rights.

### Format Files

Machine-readable representations.

Examples:

- FOLD
- ORIPA exports
- Future standards

---

# Metadata Strategy

Metadata should be treated as first-class information.

Examples:

- Creator
- Title
- Description
- Tags
- Categories
- Difficulty
- Attribution
- Licensing
- Source references
- Historical information

Metadata should be:

- Searchable
- Queryable
- Exportable
- Versionable
- Preserved long term

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

API domains may include:

- Models
- Creators
- Instructions
- Collections
- Search
- Metadata
- Licensing
- Formats

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

- Model revisions
- Metadata revisions
- Instruction revisions
- Attribution history
- Historical snapshots

Version history should be considered part of the preserved knowledge.

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