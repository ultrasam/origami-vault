# Requirements

## Purpose

This document defines the functional and non-functional requirements of Origami Vault.

The purpose of these requirements is to guide design decisions while ensuring alignment with the project's vision:

> Preserve, share, celebrate, and advance the knowledge and beauty of origami.

Requirements will evolve over time as the platform and community grow.

---

# User Roles

Origami Vault serves several different audiences.

## Visitors

Users who browse content without creating an account.

Goals:

- Discover models
- Search content
- Learn about origami creators
- Explore folding techniques

---

## Registered Users

Users with accounts who interact with the platform.

Goals:

- Save favorites
- Build collections
- Participate in the community
- Follow creators

---

## Creators

Individuals and organizations who contribute origami knowledge.

Goals:

- Publish models
- Share instructions
- Preserve creations
- Receive attribution
- Engage with the community

---

## Researchers

Users interested in the study, preservation, and digital representation of origami.

Goals:

- Access structured information
- Explore datasets
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

---

## Origami Model Management

The platform shall support:

- Creating model entries
- Editing model entries
- Publishing models
- Archiving models
- Draft models
- Attribution management

Each model shall support:

- Name
- Description
- Creator
- Difficulty level
- Categories
- Tags
- Metadata

---

## Instruction Management

The platform shall support:

- Step-by-step instructions
- Textual instructions
- Instruction notes
- Instruction revisions
- Instruction media

Instructions should remain accessible regardless of future visualization technologies.

---

## Search and Discovery

The platform shall support:

- Search by model
- Search by creator
- Search by category
- Search by tag
- Search by metadata

Future versions may support:

- Search by folding techniques
- Search by structural characteristics
- Search by digital representation

---

## Collections

The platform shall support:

- Favorites
- Personal collections
- Public collections
- Curated collections

---

## Attribution

The platform shall prioritize proper attribution.

Supported attribution should include:

- Original designer
- Diagram creator
- Photographer
- Contributor
- Editor
- Collaborator

Attribution data should be treated as first-class information.

---

# Creator Experience Requirements

## Purpose

Origami Vault should be as welcoming and accessible to creators as it is to learners.

The long-term success of the platform depends on reducing the effort required to contribute and preserve origami knowledge.

Creators should spend their time sharing and refining their work, not manually entering repetitive information.

---

## Guiding Principle

Creators should never be forced to manually enter information that already exists elsewhere.

Whenever possible, Origami Vault should assist with importing, organizing, extracting, and reusing information from existing resources.

The platform should reduce friction, not create it.

---

## Supported Contribution Workflows

Origami Vault should support multiple ways of contributing content.

No single workflow should be required.

### Manual Creation

Creators should be able to create a model from scratch using a guided form.

This workflow should support:

- Model metadata
- Cover images
- Step-by-step instructions
- Categories and tags
- Licensing information

---

### Image-Based Workflow

Creators should be able to upload collections of images.

Potential future capabilities:

- Bulk image upload
- Automatic step ordering suggestions
- Automatic image organization
- AI-assisted draft generation

---

### PDF-Based Workflow

Creators should be able to upload existing PDF instructions.

Potential future capabilities:

- Metadata extraction
- Image extraction
- Instruction extraction
- AI-assisted conversion into editable steps

---

### Format-Based Workflow

Creators should be able to upload supported origami formats such as FOLD and other community standards.

The system should preserve as much information as possible while preparing it for future visualization and interoperability features.

---

### Reference-Based Workflow

Creators should be able to create entries that reference external resources.

Examples:

- Personal websites
- Video tutorials
- Research publications
- Existing instruction repositories

Origami Vault should support preservation and attribution even when instructions are not directly hosted by the platform.

---

## Progressive Enhancement

Creators should not be required to provide all available information during initial submission.

A valid model entry may initially contain:

- Title
- Creator
- Description
- Cover image

Additional information can be added later.

The system should encourage gradual improvement rather than demanding completeness from the start.

---

## Future Creator Assistance

Future versions of Origami Vault may provide creator-assistance features, including:

- AI-assisted metadata generation
- AI-assisted instruction extraction
- Image organization
- Diagram enhancement
- Automatic format conversion
- Automatic standard validation

These tools should assist creators rather than replace them.

Creators should always retain final editorial control over their content.

---

## Creator Success Criteria

A creator should be able to contribute useful content to Origami Vault within minutes.

Contributing knowledge should feel rewarding rather than burdensome.

The platform should minimize unnecessary effort while maximizing preservation, attribution
