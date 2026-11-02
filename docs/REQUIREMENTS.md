# Requirements

## Purpose

This document defines the functional and non-functional requirements for Origami Vault.

The purpose of these requirements is to guide product design, architecture, and implementation decisions while remaining aligned with the project's vision:

> Preserve, share, celebrate, and advance the knowledge and beauty of origami.

These requirements represent the current understanding of the platform and will evolve as the project and community mature.

The conceptual data model in `docs/DATA_MODEL/` is authoritative for the meaning of the concepts referenced here.

---

# Terminology

User-facing language and model terminology may differ. This document uses the following terms:

| Term             | Meaning                                                                               | Owning Domain                                |
|------------------|---------------------------------------------------------------------------------------|----------------------------------------------|
| Model            | User-facing term for an origami design. Corresponds to an **OrigamiWork**.            | Catalog                                      |
| Work             | The conceptual origami design.                                                        | Catalog                                      |
| Edition          | A published expression of a work (book, diagram, video, etc.).                        | Catalog                                      |
| Digital Artifact | A file, media asset, or digital representation. Never the work itself.                | Artifact                                     |
| User             | An account on the platform.                                                           | Authentication (outside the knowledge model) |
| Party            | A person or organization that can receive credit. May or may not be a User.           | Attribution                                  |
| Credit           | A record linking a Party to a work, edition, or artifact through a contribution role. | Attribution                                  |
| Claim            | Information that has been submitted or imported but not necessarily verified.         | Provenance                                   |

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

Creators who contribute to the platform are Users linked to a Party. Designers who never use the platform, including historical designers, are represented as Parties only and still receive full attribution.

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

## Origami Work Management

The platform shall support:

- Creating work entries
- Editing work entries
- Publishing works
- Draft works
- Archiving works
- Recording editions of a work
- Recording relationships between works
- Attribution management

Each work shall support:

- Title (Catalog)
- Description (Catalog)
- Credits (Attribution)
- Classifications, techniques, and tags (Taxonomy)
- Difficulty level (Instruction)
- Recommended paper information
- Licensing information (Rights)
- Source references (External Reference)

Each edition shall support:

- Link to its work
- Edition title, if different from the work
- Publication information
- Credits specific to the edition (for example, Diagrammer or editor)
- Licensing information
- Linked digital artifacts

Work entries shall be distinct from the files that represent them. Uploading a file never creates a work by itself without a work record.

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

Metadata shall also:

- Record its provenance (uploaded, imported, inherited, computed, converted, inferred, curated, or verified).
- Treat imported metadata as a claim rather than authoritative knowledge.
- Allow values to be explicitly recorded as unknown.
- Allow conflicting claims to coexist when evidence supports multiple interpretations.

---

## Instruction Management

The platform shall support:

- Step-by-step instructions
- Text instructions
- Instruction notes
- Instruction revisions
- Instruction media

Instructions should remain useful regardless of future visualization technologies.

Instruction steps shall be modeled independently of geometry. A step may link to one or more geometric states (for example, FOLD frames), but geometry shall not be required to create an instruction.

---

## Search and Discovery

The platform shall support:

- Search by work title, including alternate titles
- Search by credited party
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

Credit shall be given to Parties, which may be individuals or organizations.

Supported contribution roles may include:

- Original designer
- Diagram creator
- Photographer
- Editor
- Contributor
- Collaborator

Attribution should be treated as first-class data.

Each credit shall support:

- Party receiving credit
- Contribution role
- Attribution target (work, edition, or artifact)
- Attribution source
- Attribution confidence
- Attribution status

The platform shall support:

- Parties that are not registered users, including historical designers
- Unknown designers as an explicit, valid state
- Multiple conflicting attribution claims with their evidence
- Attribution history

---

# Creator Experience Requirements

## Purpose

Origami Vault should be as welcoming to creators as it is to learners.

The long-term success of the platform depends on reducing the effort required to contribute and preserve origami knowledge.

Creators should spend their time-sharing and refining their work rather than performing repetitive data entry.

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
- Creator (which may be recorded as unknown)
- Description
- Cover image

Additional information can be added later.

Incomplete information shall never prevent preservation.

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
- Unknown

Licensing may differ between a work, its editions, and individual artifacts, and shall be recordable at each level.

Licensing information should:

- Be preserved as metadata
- Be searchable
- Be visible to users
- Remain attached to exported data whenever possible

---

# Revision History Requirements

The platform should preserve the evolution of origami knowledge over time.

Future versions should support:

- Work and edition revisions
- Instruction revisions
- Metadata revisions
- Contributor attribution
- Change history
- Historical record preservation

Historical versions should remain accessible whenever practical.

---

# External Resource Requirements

Works and editions may reference external resources.

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

# Artifact Preservation Requirements

Uploaded files are preservation records.

The platform shall:

- Preserve original uploaded artifacts without modification.
- Never overwrite preservation copies.
- Record checksums for preserved artifacts.
- Store derived representations (previews, thumbnails, conversions) separately from originals.
- Link derived representations to their source artifacts.
- Preserve unknown or unsupported data within artifacts whenever practical.

For FOLD artifacts, the platform shall additionally preserve the declared specification version and any namespaced properties, including unrecognized ones, whenever practical.

---

# Provenance Requirements

Users and maintainers should be able to determine where information came from and how it was produced.

The platform shall record provenance for:

- Uploads
- Imports
- Format conversions
- Metadata extraction
- Validation results
- Curation and verification decisions

Transformation records should include the source, output, tool, tool version, timestamp, and any warnings.

Curated or verified conclusions shall not destroy the claims and evidence they were derived from.

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