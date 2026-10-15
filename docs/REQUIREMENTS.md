# Requirements

## Purpose

This document defines the functional and non-functional requirements for Origami Vault.

The purpose of these requirements is to guide product design, architecture, and implementation decisions while remaining aligned with the project's vision:

> Preserve, share, celebrate, and advance the knowledge and beauty of origami.

These requirements represent the current understanding of the platform and will evolve as the project and community mature.

---

# User Roles

Origami Vault serves multiple audiences and perspectives.

## Visitors

Users who browse content without creating an account.

Goals:

- Discover origami models
- Learn about creators
- Explore folding techniques
- Search and browse content

---

## Registered Users

Users who create accounts and participate in the platform.

Goals:

- Save favorites
- Create collections
- Follow creators
- Participate in community activities

---

## Creators

Individuals or organizations contributing origami knowledge.

Goals:

- Publish models
- Share instructions
- Preserve creations
- Receive attribution
- Collaborate with the community

---

## Researchers

Users interested in the study, preservation, and digital representation of origami.

Goals:

- Access structured information
- Explore datasets
- Analyze folding systems
- Study standards and formats
- Build new tools and technologies

---

# Core Functional Requirements

## User Management

The platform shall support:

- User registration
- Authentication
- User profiles
- Profile customization
- Account management
- Password recovery

Future versions may support:

- Social login providers
- Multi-factor authentication

---

## Origami Model Management

The platform shall support:

- Creating model entries
- Editing model entries
- Publishing models
- Draft models
- Archiving models
- Attribution management

Each model shall support:

- Name
- Description
- Creator information
- Categories
- Tags
- Difficulty level
- Recommended paper information
- Licensing information
- Source references

---

## Metadata Management

Origami Vault shall preserve structured metadata whenever possible.

Examples include:

- Creator information
- Publication information
- Format information
- Fold complexity
- Estimated folding time
- Historical information
- Licensing information

Metadata should remain searchable and exportable.

---

## Instruction Management

The platform shall support:

- Step-by-step instructions
- Text instructions
- Instruction notes
- Instruction revisions
- Instruction media

Instructions should remain useful regardless of future visualization technologies.

---

## Search and Discovery

The platform shall support:

- Search by model name
- Search by creator
- Search by category
- Search by tags
- Search by metadata fields

Future versions may support:

- Search by folding techniques
- Search by structural characteristics
- Search by origami format data
- Search by machine-readable representations

---

## Collections

The platform shall support:

- Favorites
- Personal collections
- Public collections
- Curated collections

---

## Attribution

Origami Vault shall prioritize creator attribution.

Supported attribution entities may include:

- Original designer
- Diagram creator
- Photographer
- Editor
- Contributor
- Collaborator
- Organization

Attribution should be treated as first-class data.

---

# Creator Experience Requirements

## Purpose

Origami Vault should be as welcoming to creators as it is to learners.

The long-term success of the platform depends on reducing the effort required to contribute and preserve origami knowledge.

Creators should spend their time sharing and refining their work rather than performing repetitive data entry.

---

## Guiding Principle

Creators should never be forced to manually enter information that already exists elsewhere.

Whenever possible, Origami Vault should assist with importing, organizing, extracting, and reusing information from existing resources.

The platform should reduce friction, not create it.

---

## Supported Contribution Workflows

No single contribution workflow should be mandatory.

### Manual Creation

Creators should be able to create models manually using guided forms.

Supported information may include:

- Metadata
- Instructions
- Images
- Licensing
- References

---

### Image-Based Workflow

Creators should be able to upload image collections.

Future capabilities may include:

- Bulk uploads
- Step ordering suggestions
- Image organization
- AI-assisted instruction creation

---

### PDF-Based Workflow

Creators should be able to upload existing PDF instructions.

Future capabilities may include:

- Metadata extraction
- Image extraction
- Instruction extraction
- AI-assisted conversion into editable content

---

### Format-Based Workflow

Creators should be able to upload supported origami formats.

Examples:

- FOLD
- ORIPA exports
- Future community standards

The platform should preserve as much information as possible.

---

### Reference-Based Workflow

Creators should be able to create entries that reference external content.

Examples:

- Personal websites
- YouTube videos
- Research publications
- Existing instruction repositories

Origami Vault should preserve attribution and discovery value even when content is not hosted directly.

---

## Progressive Enhancement

Creators should not be required to provide complete information immediately.

A valid submission may initially contain:

- Title
- Creator
- Description
- Cover image

Additional information can be added later.

The platform should encourage gradual improvement rather than demanding completeness.

---

## Future Creator Assistance

Future versions may provide:

- AI-assisted metadata generation
- AI-assisted instruction extraction
- Image organization
- Diagram enhancement
- Format conversion
- Standards validation

These systems should assist creators rather than replace them.

Creators should always retain final editorial control.

---

## Creator Success Criteria

A creator should be able to contribute useful content within minutes.

Contributing knowledge should feel rewarding rather than burdensome.

---

# Content Licensing Requirements

Origami Vault shall preserve licensing information for all supported content.

Supported licensing models may include:

- All Rights Reserved
- Public Domain
- Creative Commons licenses
- Custom licenses

Licensing information should:

- Be preserved as metadata
- Be searchable
- Be visible to users
- Remain attached to exported data whenever possible

---

# Revision History Requirements

The platform should preserve the evolution of origami knowledge over time.

Future versions should support:

- Model revisions
- Instruction revisions
- Metadata revisions
- Contributor attribution
- Change history
- Historical record preservation

Historical versions should remain accessible whenever practical.

---

# External Resource Requirements

Models may reference external resources.

Supported references may include:

- Videos
- Websites
- Books
- Journals
- Research papers
- Existing repositories

Origami Vault should support preserving references even when the referenced content is not hosted by the platform.

---

# Structured Knowledge Requirements

Whenever practical, origami knowledge should be stored in machine-readable forms.

Examples include:

- Metadata
- Creator information
- Licensing information
- Format references
- Fold representations
- Structural relationships

Structured information should be preferred over purely visual information whenever practical.

---

# Media Requirements

The platform shall support:

- Cover images
- Step images
- Image galleries
- PDFs
- Format files
- External media references

Media should remain separable from metadata to support future migrations and preservation efforts.

---

# Standards and Interoperability Requirements

The platform should encourage interoperability whenever possible.

Goals include:

- Support existing origami formats
- Preserve machine-readable information
- Enable future integrations
- Encourage open standards
- Support knowledge exchange between systems

Support for formats such as FOLD should be researched early and expanded over time.

---

# Future-Oriented Requirements

The platform should remain flexible enough to support future capabilities including:

- Interactive visualization
- Folding simulations
- 3D representations
- Public APIs
- Educational systems
- Research tools
- Machine-readable fold descriptions
- Geometry generation
- Future fabrication workflows
- Technologies not yet imagined

These features should not compromise accessibility and simplicity.

---

# Non-Functional Requirements

## Accessibility

The platform should be accessible to users of varying technical backgrounds and skill levels.

---

## Performance

The platform should remain responsive and usable as content grows significantly.

---

## Preservation

Long-term preservation of origami knowledge should be prioritized whenever possible.

---

## Extensibility

The architecture should support growth without requiring fundamental redesigns.

---

## Interoperability

The platform should avoid unnecessary lock-in and encourage compatibility with external systems.

---

## Maintainability

The system should be understandable and maintainable by future contributors.

Simplicity should be preferred over unnecessary complexity.

---

# Guiding Principle

Technology is not the goal.

The goal is to preserve, share, celebrate, and advance the knowledge and beauty of origami.

Every requirement should support that mission.

---

Created by Sócrates Medina

Bogotá, Colombia

2026

Omnia Vincit Amor