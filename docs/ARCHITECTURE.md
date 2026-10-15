# Architecture

## Purpose

This document defines the high-level architecture of Origami Vault.

Its purpose is to guide technical decisions while supporting the project's mission:

> Preserve, share, celebrate, and advance the knowledge and beauty of origami.

This document focuses on principles, structure, responsibilities, and architectural decisions rather than detailed implementation.

Architecture decisions should evolve with the project while remaining aligned with the vision of preserving origami knowledge for future generations.

---

# Architectural Principles

## Preservation First

Origami Vault exists primarily to preserve origami knowledge.

Technology choices should prioritize:

- Longevity
- Portability
- Accessibility
- Data integrity

Knowledge should remain usable regardless of future technology changes.

---

## Knowledge-Centric Design

Origami Vault is not primarily a website, an application, or a file repository.

Origami Vault is a platform dedicated to preserving origami knowledge.

Web applications, APIs, visualization systems, importers, exporters, simulations, and future tools should be viewed as interfaces to the underlying knowledge base.

Architecture decisions should prioritize preservation and portability of knowledge over convenience of implementation.

---

## Open Standards

Whenever possible, Origami Vault should support and encourage open standards.

The project should:

- Avoid unnecessary vendor lock-in
- Encourage interoperability
- Build upon existing standards when practical

---

## Interoperability

Origami Vault should be capable of exchanging information with external systems.

Examples include:

- FOLD-compatible tools
- ORIPA
- Ori Studio
- Research systems
- Educational platforms
- Future origami software ecosystems

---

## Progressive Complexity

The simplest solution that satisfies current requirements should be preferred.

Advanced features such as:

- Simulation
- Structured fold languages
- Geometry generation
- Robotics integration

should only be introduced when justified by value and maturity.

---

## Creator-Friendly Design

Architecture decisions should reduce friction for contributors.

The system should support multiple contribution workflows and accommodate creators with different levels of technical expertise.

---

# System Overview

The architecture consists of several layers:

```text
+---------------------------+
|       Web Application     |
|       Ruby on Rails       |
+-------------+-------------+
              |
              v
+---------------------------+
|          API Layer        |
+-------------+-------------+
              |
              v
+---------------------------+
|      Knowledge Layer      |
|  Models, Instructions,    |
| Metadata, Collections     |
+-------------+-------------+
              |
              v
+---------------------------+
|        PostgreSQL         |
+-------------+-------------+
              |
              v
+---------------------------+
|       Object Storage      |
| Images, PDFs, Formats     |
+---------------------------+
```

Future Clients:

```text
+---------------------------+
|      Godot Client         |
+---------------------------+

+---------------------------+
| Third-Party Applications  |
+---------------------------+

+---------------------------+
| Educational Platforms     |
+---------------------------+
```

All future clients should communicate through APIs rather than directly accessing the database.

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

Origami Vault will initially be developed as a modular monolith.

Responsibilities will remain within a single Rails application:

- Authentication
- User management
- Content management
- Search
- Collections
- Metadata management
- API delivery

Microservices should not be introduced prematurely.

---

## Scaling Strategy

Future service separation may be considered when justified by:

- Performance requirements
- Development complexity
- Operational needs
- Community growth

Until then, simplicity should be prioritized.

---

# Backend Architecture

## Technology

Primary Backend:

- Ruby on Rails

Primary Database:

- PostgreSQL

---

## Responsibilities

The backend is the source of truth for all preserved knowledge.

Responsibilities include:

- Authentication
- Authorization
- User management
- Creator management
- Model management
- Metadata management
- Search
- Revision history
- API endpoints
- File management

---

# Authentication Strategy

Initial authentication:

- Email and password

Future authentication may include:

- GitHub
- Google
- Microsoft

Authentication systems should remain isolated from domain models whenever practical.

---

# Frontend Architecture

## Web Platform

Primary user interface.

Responsibilities:

- Browsing
- Search
- Content management
- Community features
- Creator workflows

---

## Godot Client

Future component.

Responsibilities:

- Advanced visualization
- Interactive learning experiences
- Fold visualizations
- Experimental interfaces
- 3D exploration

The Godot client should consume APIs rather than directly accessing persistence layers.

---

# Data Architecture

## Core Domains

The core knowledge model should include:

### Users

Platform accounts.

### Creators

Origami designers and contributors.

Creators may or may not be registered users.

### Models

Origami creations and their associated metadata.

### Instructions

Folding knowledge.

### Steps

Individual instruction steps.

### Media

Images, files, diagrams, PDFs, and related assets.

### Collections

User-curated groupings of models.

### References

External resources.

### Licenses

Content ownership and usage rights.

### Format Files

Machine-readable representations such as FOLD.

---

# Metadata Strategy

Metadata should be treated as first-class information.

Examples include:

- Creator
- Title
- Description
- Categories
- Tags
- Difficulty
- Licensing
- Dates
- Source references

Metadata should be:

- Searchable
- Exportable
- Versionable
- Preserved long term

---

# Search Strategy

Search is a core capability of Origami Vault.

Version 0.1:

- PostgreSQL full-text search

Future evaluation:

- Elasticsearch
- OpenSearch
- Meilisearch

Search infrastructure should remain replaceable without modifying business logic.

---

# Media Storage Strategy

Metadata and media should remain separated.

## PostgreSQL

Stores:

- Records
- Metadata
- Relationships
- Attribution
- References

---

## Object Storage

Stores:

- Images
- PDFs
- FOLD files
- Future format files

Potential providers:

- AWS S3
- Backblaze B2
- Cloudflare R2

Development environments may use local storage.

---

# Standards Layer

Origami Vault should maintain a clear separation between:

- Internal knowledge models
- External file formats

Supported formats may include:

- FOLD
- ORIPA exports
- Future community standards

Importers and exporters should serve as translation layers between external standards and internal representations.

The project should prefer interoperability and extension over replacement whenever practical.

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
- Formats

APIs should support future clients without requiring architectural redesign.

---

# Revision Strategy

Knowledge preservation includes preserving history.

Future versions should support:

- Model revisions
- Instruction revisions
- Metadata revisions
- Contributor attribution
- Historical snapshots

Version history should be treated as part of the preserved knowledge.

---

# Future Architectural Directions

The following items are intentionally outside the MVP but should remain architectural possibilities:

- Fold simulation
- Interactive folding engines
- Structured fold languages
- Geometry generation
- Blender export
- STL generation
- Educational systems
- Research tooling
- Public SDKs
- Robotic folding integration

Current decisions should avoid making these future capabilities impossible.

---

# Architectural Success Criteria

The architecture is successful if it:

- Preserves knowledge effectively
- Remains understandable
- Encourages interoperability
- Supports community growth
- Supports future innovation
- Remains maintainable by future contributors

---

# Guiding Principle

The most important asset of Origami Vault is not its code.

It is the knowledge that the platform preserves.

Every architectural decision should prioritize preservation, clarity, interoperability, and long-term sustainability.

---

Created by Sócrates Medina

Bogotá, Colombia

2026

Omnia Vincit Amor