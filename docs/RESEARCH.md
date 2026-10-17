# Research

## Purpose

This document tracks research efforts, investigations, open questions, and future areas of study that help guide the development of Origami Vault.

Detailed research findings should be documented in dedicated research documents whenever a topic grows large enough to warrant its own analysis.

---
## Reference Research Documents

```text
docs/research/FOLD_FORMAT_RESEARCH.md
```
# Completed Research

## FOLD Format Research

Status: Completed

Reference:

- docs/research/FOLD_FORMAT_RESEARCH.md

Summary:

The FOLD file format was evaluated as the primary machine-readable origami interchange format currently supported by the computational origami ecosystem.

Key conclusions:

- FOLD is an important interchange format but is not a complete representation of origami knowledge.
- Origami Vault will treat FOLD as a first-class digital artifact.
- Origami Vault's internal knowledge model will remain independent of any individual format.
- Instructional content and FOLD frames should remain separate but linkable.
- Multiple versions of FOLD must be preserved and interpreted through version-aware processing.
- Original uploaded artifacts should remain immutable.
- Unknown and custom namespaced properties should be preserved whenever practical.
- Provenance and transformation history are essential preservation concerns.

See the FOLD research document for details.

---

# Active Research

## ORIPA Research

Status: Not Started

Goals:

- Understand OPX structure
- Identify interoperability opportunities
- Evaluate conversion fidelity between OPX and FOLD

---

## Ori Studio Research

Status: Not Started

Goals:

- Review supported formats
- Review simulation capabilities
- Identify integration opportunities

---

## Origami Simulator Research

Status: Not Started

Goals:

- Understand simulation approaches
- Evaluate suitability for future Origami Vault visualization features

---

## Origami Community & Databases

Status: Not Started

Goals:

- Compare existing repositories
- Identify unmet creator needs
- Identify preservation gaps

---

# Implementation Research

## Ruby Parser Prototype

Status: Planned

Goals:

- Parse FOLD 1.2
- Preserve unknown properties
- Validate specifications
- Generate import metadata

---

## C# / Godot Parser Prototype

Status: Planned

Goals:

- Parse FOLD documents
- Validate frame inheritance
- Build future visualization pipeline

---

## Compatibility Matrix

Status: Planned

Goals:

Track compatibility between:

- FOLD
- OPX
- ORIPA
- Ori Studio
- Origami Simulator
- Future formats

---

# Open Questions

The following questions remain unresolved:

- What coordinate convention should be used for Godot visualization?
- What coordinate convention should be used for Blender exports?
- What subset of FOLD data should be indexed for search?
- How should imported metadata be verified and curated over time?
- What future standards should Origami Vault support beyond FOLD?

---

# Future Research Topics

- Structured fold languages
- Universal origami representations
- Geometry generation
- Blender export
- STL generation
- Educational systems
- Robotic folding systems
- AR/VR origami experiences
- Automatic diagram generation

---

Created by Sócrates Medina

Bogotá, Colombia

2026

Omnia Vincit Amor