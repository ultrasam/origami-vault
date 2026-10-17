# FOLD Format Research

## Final Specification-Aligned Research for Origami Vault

**Specification reviewed:** FOLD 1.2  
**Project:** Origami Vault  
**Status:** Complete for conceptual data-model design; implementation validation remains open.

---

## 1. Purpose

This document evaluates the FOLD format and its relevance to Origami Vault. It combines findings from the FOLD 1.2 specification, specification history, reference API documentation, repository tutorial, and prior Origami Vault research.

Its goals are to:

- Explain what FOLD represents.
- Distinguish specification-defined data from reference-library behavior.
- Identify what FOLD does and does not provide for Origami Vault.
- Establish preservation, parsing, validation, conversion, and provenance principles.
- Define the architectural boundary between FOLD artifacts and the Origami Vault knowledge model.
- Record the remaining implementation-validation work.

This document does not replace the official FOLD specification. The specification describes itself as a rough draft whose definition may evolve.

---

## 2. Executive Summary

FOLD, or Flexible Origami List Datastructure, is a JSON-based format for describing folded structures. It can represent one or more frames containing geometric, topological, classification, unit, and layer-order information. Its flat, parallel-array design covers vertices, edges, faces, and relationships among them, while namespaced custom properties allow applications to preserve additional data.

FOLD is important to Origami Vault, but a FOLD file is not a complete representation of origami knowledge. Origami Vault also requires verified attribution, rights and licensing, educational instruction semantics, media, provenance, revisions, community information, preservation history, and external references.

The central architectural decision is therefore:

> An Origami Vault work or edition may have zero, one, or many FOLD artifacts. A FOLD artifact is an important machine-readable representation, but it is not the canonical definition of the work itself.

Every uploaded FOLD artifact should be preserved immutably. Parsing, normalization, validation, topology derivation, format conversion, visualization, and export should produce traceable interpretations or derived artifacts rather than altering the preservation copy.

---

## 3. Source Authority

The reviewed materials have different authority levels:

1. **FOLD specification 1.2** — normative source for standardized property names and semantics.
2. **FOLD specification history** — authoritative record of documented changes between 1.0, 1.1, and 1.2.
3. **FOLD API documentation** — documentation for one reference implementation and its operations; it does not independently define the standard.
4. **FOLD tutorial/README** — explanatory material and examples; useful for understanding the intended mental model, but incomplete and non-normative.
5. **Origami Vault research** — project-specific architectural interpretation and recommendations.

If these sources conflict, the applicable specification version should govern format interpretation. Behavior of real software must still be tested.

---

## 4. Specification Maturity and Version History

The reviewed FOLD specification is version 1.2 and identifies itself as a rough draft. It states that version numbers will change when breaking changes or major features are added.

### Version 1.0

- Initial public draft.

### Version 1.1

- Renamed `edges_foldAngles` to `edges_foldAngle`.
- Renamed `edges_lengths` to `edges_length`.

### Version 1.2

- Added `J` join-edge assignments.
- Added optional `C` cut/slit assignments.
- Added `joins` / `noJoins` and `cuts` / `noCuts` frame attributes.
- Added `convexFaces` / `nonConvexFaces` frame attributes.
- Defined `vertices_edges` and `faces_faces`.
- Allowed null values in `vertices_faces`, `edges_faces`, and `faces_faces` to support consistent order and orientation.
- Clarified that faces correspond to the material being folded and normally exclude the unbounded exterior face of a planar graph.
- Clarified that multiple-frame support is optional.

### Origami Vault compatibility rule

Origami Vault must preserve:

- The original `file_spec` value.
- Original property names and values.
- The parser target version.
- Any version-specific aliases encountered.
- The normalization profile used.

Legacy names may be normalized in an interpretation layer, but the original artifact must not be destructively rewritten.

---

## 5. Format Design

A `.fold` file is a JSON object. It may contain file metadata and frame properties. FOLD uses zero-indexed, parallel arrays: entries at the same index describe the same vertex, edge, or face.

The specification defines these property families:

- `file_...` — metadata about the file.
- `frame_...` — metadata about a frame.
- `vertices_...` — information indexed by vertex ID.
- `edges_...` — information indexed by edge ID.
- `faces_...` — information indexed by face ID.
- `faceOrders` and `edgeOrders` — ordering relationships.

All properties are optional, although several are recommended or strongly recommended. A single-frame file can place frame properties directly in the top-level object and omit `file_frames`.

---

## 6. File Metadata

Specification-defined file metadata includes:

- `file_spec` — numeric specification version; strongly recommended.
- `file_creator` — software that created the file; recommended for software output.
- `file_author` — human-readable author text.
- `file_title` — file title.
- `file_description` — file description.
- `file_classes` — subjective classification of the file.
- `file_frames` — additional frame objects after the key frame.

Standard file classes include:

- `singleModel`
- `multiModel`
- `animation`
- `diagrams`

Imported author and title strings are claims from the source artifact. They must not automatically create a verified Origami Vault creator relationship or overwrite curated catalog metadata.

---

## 7. Frame Metadata, Attributes, and Units

Specification-defined frame metadata includes:

- `frame_author`
- `frame_title`
- `frame_description`
- `frame_classes`
- `frame_attributes`
- `frame_unit`

Standard frame classes include:

- `creasePattern`
- `foldedForm`
- `graph`
- `linkage`

Standard frame attributes include pairs or values describing:

- `2D`, `3D`, or `abstract`
- `manifold` / `nonManifold`
- `orientable` / `nonOrientable`
- `selfTouching` / `nonSelfTouching`
- `selfIntersecting` / `nonSelfIntersecting`
- `cuts` / `noCuts`
- `joins` / `noJoins`
- `convexFaces` / `nonConvexFaces`

Standard `frame_unit` values are:

- `unit`
- `in`
- `pt`
- `m`
- `cm`
- `mm`
- `um`
- `nm`

A custom unit string is permitted, but other software may not understand it.

Origami Vault should preserve the source unit exactly. Any display-unit conversion or engine scaling must be a derived interpretation.

---

## 8. Vertex Information

Specification-defined vertex properties include:

- `vertices_coords` — coordinate arrays such as `[x, y]` or `[x, y, z]`. Trailing unspecified coordinates are implicitly zero. Recommended except for abstract frames.
- `vertices_vertices` — adjacent vertex IDs. Recommended when `edges_vertices` is absent.
- `vertices_edges` — incident edge IDs.
- `vertices_faces` — incident face IDs, possibly including null values.

For orientable manifolds and planar linkages, adjacency ordering has counterclockwise semantics. When related arrays coexist, their order and entries must align.

---

## 9. Edge Information

Specification-defined edge properties include:

- `edges_vertices` — endpoint IDs `[u, v]`; the order defines edge orientation. Recommended whenever edge properties are present.
- `edges_faces` — incident face IDs, with orientation-dependent ordering semantics.
- `edges_assignment` — edge role.
- `edges_foldAngle` — deviation from flatness in degrees.
- `edges_length` — edge length, especially useful for abstract complexes.

Standard assignment values in version 1.2 are:

- `B` — boundary
- `M` — mountain
- `V` — valley
- `F` — flat
- `U` — unassigned or unknown
- `C` — cut or slit
- `J` — join

`C` and `J` were added in version 1.2. Support for cut edges is optional.

`edges_foldAngle` uses degrees from −180 through +180. Positive values indicate valley folds, negative values indicate mountain folds, and zero indicates flat, unassigned, or boundary edges. If assignment and angle are both present, their meanings should agree.

---

## 10. Face Information and Orientation

Specification-defined face properties include:

- `faces_vertices` — face vertex IDs in counterclockwise order; recommended when faces exist.
- `faces_edges` — boundary edge IDs in counterclockwise order and aligned with `faces_vertices`.
- `faces_faces` — adjacent face IDs, possibly including null values.

Counterclockwise face ordering defines the side or sign of the face normal. Faces represent the material being folded and normally exclude the unbounded exterior face of a planar graph.

---

## 11. Layer-Order Information

FOLD represents layer ordering through whole-face or whole-edge relationships rather than arbitrary overlapping points.

### `faceOrders`

An array of triples `[f, g, s]`:

- `+1` — face `f` is above face `g`, relative to `g`'s normal.
- `-1` — face `f` is below face `g`.
- `0` — the order is unknown.

Omitting a face pair is equivalent to unknown. Cycles may exist. Faces overlapping in multiple disconnected regions may need subdivision for a consistent relationship.

### `edgeOrders`

An array of triples describing local left/right relationships between overlapping edges. This property is meaningful only in two-dimensional linkage configurations.

Ordering information records constraints. It does not perform collision detection, prevent self-intersection, or prove physical foldability.

---

## 12. Multiple Frames and Inheritance

The top-level non-file properties describe the key frame, frame 0.

`file_frames` contains only later frames:

```text
file_frames[0] = frame 1
file_frames[1] = frame 2
...
```

A non-key frame may specify:

- `frame_parent` — parent frame ID.
- `frame_inherit` — if true, non-overridden parent properties are inherited recursively.

Multiple-frame support is optional. Software that does not support multiple frames may use only the key frame.

A file class of `diagrams` can identify a sequence of folding-step frames, and `animation` can identify an animation sequence. Nevertheless, frame order alone does not provide the complete pedagogical meaning of the transition between states.

Origami Vault must therefore model `InstructionStep` and `FoldFrame` separately, with optional links between them.

---

## 13. Custom Properties and Extensions

Custom property keys must contain a colon. The prefix identifies the defining application or namespace—for example, `edges_tm:structuralType`.

Property keys without colons are reserved for current or future specification use. Custom frame classes and attributes follow the same namespaced pattern.

Origami Vault should classify properties as:

- Specification-defined
- Legacy specification property
- Namespaced extension
- Reference-implementation property
- Application-specific property
- Unknown property

Unknown and namespaced properties should be preserved losslessly whenever syntactically safe, even when Origami Vault cannot interpret them.

---

## 14. Specification Versus Reference Implementation

The specification defines standardized fields and semantics. The reference API documents one implementation that can view, filter, transform, derive, and convert FOLD objects.

The API is organized into modules for:

- Browser SVG viewing
- Filtering and mutation
- Conversion and topology derivation
- Filesystem loading, saving, and conversion in Node environments
- Geometry tools

A property mentioned by the API is not automatically part of the official specification. For example, implementation code may recognize experimental or application-specific fields that do not appear in specification 1.2.

Origami Vault must use the selected specification as the basis for standards classification and treat implementation behavior as separately versioned evidence.

---

## 15. Source, Inherited, and Derived Properties

The reference API can compute optional topology from other data, including:

- Vertex adjacency from edges
- Incident edge arrays from edges
- Counterclockwise-sorted adjacency from two-dimensional geometry
- Faces from sorted adjacency
- Face edges from faces and edges
- Edges and related data from face definitions

Origami Vault must distinguish the provenance of every interpreted value:

- `uploaded` — present in the source artifact
- `inherited` — obtained through FOLD frame inheritance
- `computed` — derived algorithmically
- `converted` — produced by format conversion
- `inferred` — suggested from incomplete information
- `curated` — edited by a human curator
- `verified` — reviewed and accepted under a defined process

Derived data must never be presented as though it had been supplied in the original artifact.

---

## 16. Mutation and Transformation Safety

The reference API documents in-place operations that can:

- Split crossing or touching edges
- Remove duplicate or loop edges
- Add vertices and edges
- Split cut edges into boundary edges
- Transform coordinates
- Compute or rebuild topology

Some operations may ignore, invalidate, or reconstruct properties. For example, the documented cut-splitting operation can split vertices and ignores face properties.

Origami Vault must never run such transformations against the preservation copy. Every mutation must operate on a working copy and produce a new, traceable derived representation or artifact.

Each transformation should record:

- Operation name
- Implementation name and version
- Input artifact version(s)
- Parameters and tolerance values
- Output artifact version(s)
- Target format or specification version
- Warnings and errors
- Whether the operation is known or believed to be lossless
- Creation time and responsible actor or process

---

## 17. FOLD–OPX Interoperability

The reference API documents conversion between FOLD and ORIPA OPX data and file conversion for `.fold` and `.opx` extensions.

This establishes an explicit conversion path in the reference implementation. It does not prove that every ORIPA version natively imports FOLD, that every FOLD feature maps to OPX, or that a round trip preserves all data.

Origami Vault should treat format conversion as a versioned, potentially lossy transformation and maintain compatibility-test and round-trip-test results.

---

## 18. Tutorial Reliability

The reviewed tutorial is useful for understanding:

- The JSON object model
- Optional metadata
- Zero-indexed vertex and edge IDs
- The relationship among vertices, edges, and faces
- The distinction between human-readable text and machine-oriented classes

However, the tutorial is unfinished. It includes placeholders and incomplete sections for faces, frames, three-dimensional geometry, and face ordering. Its coordinate explanation also conflicts with its displayed coordinate array in at least one place.

The tutorial should therefore be treated as explanatory material, not as a normative validation source. The selected specification and validated sample files take precedence.

---

## 19. What FOLD Does Not Provide by Itself

FOLD does not by itself provide:

- Verified creator identity or authoritative attribution relationships
- Complete rights, licensing, consent, and permissions workflows
- Community accounts, comments, favorites, collections, moderation, or social relationships
- A complete educational instruction language
- Hand placement, selected flap, participating layers, arrows, camera direction, timing, narration, warnings, or pedagogical intent
- Guaranteed collision-free motion or physical foldability
- A complete physical model of thickness, stiffness, friction, plastic deformation, wet folding, or self-contact
- Preservation controls such as immutable repository versions, checksums, backups, provenance records, and curation history

These concerns belong to Origami Vault’s broader knowledge model and platform architecture.

---

## 20. Core Origami Vault Data-Model Decisions

1. An Origami Vault model is not a FOLD file.
2. `OrigamiWork` represents the conceptual design.
3. `OrigamiEdition` represents a particular published or revised expression of a work.
4. An edition may have zero, one, or many FOLD artifacts.
5. Every uploaded artifact is immutable; replacement creates a new artifact version.
6. Source bytes, parsed JSON, normalized metadata, validation results, and derived outputs are separate concerns.
7. `InstructionStep` and `FoldFrame` are separate but linkable.
8. Imported metadata is a claim, not automatically authoritative.
9. Unknown and namespaced properties should be preserved losslessly when safe.
10. The internal knowledge model must remain independent of FOLD and other external formats.
11. Format conformance does not prove physical foldability.
12. Specification, parser, validator, converter, transformation, and normalization versions must be recorded.
13. Derived values must retain value-level or process-level provenance.
14. Format conversions must be represented as derivations, not overwrites.

---

## 21. Recommended Data Domains

### Catalog

- `OrigamiWork`
- `OrigamiEdition`
- `Category`
- `Tag`
- `Technique`
- `DifficultyAssessment`

### Attribution and Rights

- `Party`
- `Person`
- `Organization`
- `Credit`
- `CreditRole`
- `License`
- `RightsStatement`

### Instructions

- `InstructionSet`
- `InstructionStep`
- `StepAnnotation`
- `StepMedia`
- `StepFrameLink`

### Digital Artifacts

- `DigitalArtifact`
- `ArtifactVersion`
- `ArtifactDerivation`
- `FoldArtifact`
- `PdfArtifact`
- `ImageArtifact`
- `ExternalArtifact`
- `Checksum`

### Import, Interpretation, and Validation

- `ImportRecord`
- `ParseRecord`
- `ValidationReport`
- `ValueProvenance`
- `TransformationRecord`

### FOLD-Specific Interpretation

- `FoldDocument`
- `FoldFrame`
- `FoldValidationResult`
- `FoldDerivedRepresentation`

### Compatibility and Conversion

- `FormatSpecification`
- `FormatSpecificationVersion`
- `PropertyAlias`
- `FormatConverter`
- `ConversionProfile`
- `CompatibilityTest`
- `RoundTripTestResult`

### Community and Preservation

- `User`
- `Collection`
- `CollectionItem`
- `Favorite`
- `Revision`
- `ExternalReference`
- `PreservationEvent`

Not all of these concepts need separate database tables in the MVP. `DATA_MODEL.md` should classify each as an MVP entity, embedded record, future entity, or research concept.

---

## 22. Recommended Initial Storage Strategy

For each FOLD upload:

1. Store the immutable original file in object storage.
2. Store filename, MIME type, size, checksum, uploader, source, rights information, upload time, and associated edition in relational records.
3. Optionally store parsed JSON in PostgreSQL JSONB while preserving the original file as the source of record.
4. Normalize only useful searchable fields initially:
   - Declared specification version
   - File and frame classes
   - Frame attributes
   - Units
   - Frame count
   - Coordinate dimensionality
   - Vertex, edge, and face counts
   - Assignment types present
   - Validation status
   - Parser version
5. Store warnings and errors outside the artifact itself.
6. Store generated previews, meshes, thumbnails, and converted files as derived artifacts with provenance.
7. Do not initially create one relational row per vertex, edge, or face. Introduce detailed geometry normalization only when editing, geometry search, analytics, or scale requirements justify it.

---

## 23. Required and Recommended Property Matrix

Version 1.2 states that all properties are optional. The following table records explicit recommendations and important interpretation notes.

| Property | Scope | Status in 1.2 | Important note |
|---|---|---|---|
| `file_spec` | File | Strongly recommended | Preserve declared version |
| `file_creator` | File | Recommended for software output | Refers to creating software |
| `file_author` | File | Optional | Human-readable claim |
| `file_title` | File | Optional | Keep separate from curated catalog title |
| `file_description` | File | Optional | Preserve original text |
| `file_classes` | File | Optional | Standard or namespaced values |
| `file_frames` | File | Optional | Contains frames 1 through n; top level is frame 0 |
| `frame_author`, `frame_title`, `frame_description` | Frame | Optional | Frame-specific text |
| `frame_classes` | Frame | Optional | Subjective classification |
| `frame_attributes` | Frame | Optional | Objective properties |
| `frame_unit` | Frame | Optional | Preserve source unit |
| `vertices_coords` | Frame | Recommended except abstract | Coordinates may be 2D, 3D, or higher-dimensional |
| `vertices_vertices` | Frame | Recommended if `edges_vertices` is absent | Adjacency and ordering semantics apply |
| `vertices_edges` | Frame | Optional | Align with adjacency arrays |
| `vertices_faces` | Frame | Optional | Nulls may be present |
| `edges_vertices` | Frame | Recommended when edge properties exist | Defines edge orientation |
| `edges_faces` | Frame | Optional | Left/right ordering has meaning |
| `edges_assignment` | Frame | Optional | `B`, `M`, `V`, `F`, `U`, `C`, `J` |
| `edges_foldAngle` | Frame | Optional | −180 through +180; sign should match assignment |
| `edges_length` | Frame | Optional | Useful for abstract complexes |
| `faces_vertices` | Frame | Recommended when faces exist | Counterclockwise; defines normal |
| `faces_edges` | Frame | Optional | Counterclockwise and aligned |
| `faces_faces` | Frame | Optional | Nulls may be present |
| `faceOrders` | Frame | Recommended for interior-overlapping faces | Cycles may exist |
| `edgeOrders` | Frame | Recommended for overlapping 2D linkage edges | Meaningful only in 2D |
| `frame_parent` | Non-key frame | Optional | Validate parent index and cycles |
| `frame_inherit` | Non-key frame | Optional | Inherit non-overridden properties recursively |
| Namespaced custom property | Custom | Allowed | Colon required |

---

## 24. Relationship Between FOLD Frames and Instruction Data

An `InstructionStep` may link to zero or more `FoldFrame` records. A `FoldFrame` may support zero or more steps or instruction sets.

A link should identify its role, such as:

- `beforeState`
- `afterState`
- `keyState`
- `referenceState`
- `previewState`

Even when a file is classified as `diagrams`, Origami Vault should independently preserve:

- Step order
- Instruction text
- Technique or action
- Annotations and arrows
- Moving region or selected layers
- Camera or viewing guidance
- Step media
- Tips, warnings, and expected result

Frame order alone must not be used to infer these semantics.

---

## 25. Validation and Security Requirements

An importer should enforce limits for:

- File size
- JSON nesting depth
- Frame count
- Vertex, edge, and face counts
- Processing time
- Memory consumption

Validation should address:

- JSON syntax
- Finite numeric values
- Parallel-array alignment
- Index bounds
- Coordinate dimensions
- Parent indices and inheritance cycles
- Known assignment values
- Fold-angle range and assignment compatibility
- Face and adjacency ordering where verifiable
- Namespaced versus unnamespaced unknown properties
- Version-specific aliases
- Topology consistency where sufficient data exists

The platform should compute cryptographic checksums and record:

- Validation date
- Declared and interpreted specification versions
- Parser and validator versions
- Warnings and errors
- Transformation history
- Preservation and backup status

---

## 26. Compatibility and Round-Trip Testing

Origami Vault should maintain a compatibility matrix for each application and version:

- Imports FOLD?
- Exports FOLD?
- Supported specification versions?
- Multiple-frame support?
- Inheritance support?
- Cuts and joins support?
- Layer-order support?
- Namespaced-property preservation?
- Ignored or transformed fields?
- Round-trip information loss?

FOLD-to-OPX conversion should be tested with representative artifacts and documented as a conversion profile. The existence of a converter must not be treated as proof of lossless interoperability.

---

## 27. Remaining Validation Work

The conceptual research is complete. Remaining work is implementation validation.

### Sample corpus

Collect and preserve representative files for:

- FOLD 1.0
- FOLD 1.1
- FOLD 1.2
- Key-frame-only files
- Multi-frame files
- Inherited frames
- Diagram and animation classes
- Cuts and joins
- Abstract geometry
- 2D linkages
- 3D folded forms
- Face and edge ordering
- Namespaced custom properties
- OPX-converted data
- Legacy property names
- Intentionally invalid and hostile files

### Parser prototypes

Implement read-only Ruby and C# parsers against the same corpus. Compare their interpretation of:

- Versions and aliases
- Frames and inheritance
- Units and dimensionality
- Assignments and fold angles
- Topology and orientation
- Ordering information
- Custom properties
- Validation results

### Coordinate adapters

Define Origami Vault policies for:

- FOLD-to-Godot axis mapping
- FOLD-to-Blender axis mapping
- Handedness conversion
- Unit scaling
- Face winding and normal handling
- Preservation of original coordinates

### Conversion and round-trip tests

Record source, target, converter version, parameters, warnings, preserved fields, transformed fields, and lost fields.

---

## 28. Final Recommendation

Origami Vault is ready to proceed with `DATA_MODEL.md`.

FOLD should be treated as:

- A supported interchange format
- A preserved digital artifact
- A source of machine-readable geometry and topology
- A possible source of diagram or animation frames
- One representation among several

FOLD should not be treated as:

- The canonical definition of an Origami Vault work
- A complete educational instruction language
- Verified attribution or rights data
- Proof of physical foldability
- A universal, lossless bridge among all origami tools

The initial implementation should favor faithful preservation, version-aware interpretation, and provenance over aggressive normalization. Origami Vault can derive richer representations later, but it cannot recover source data discarded during import.

---

## Sources

- FOLD specification, version 1.2
- FOLD specification history
- FOLD API documentation
- FOLD repository tutorial/README
- Origami Vault corrected FOLD research draft

---

**Created for Origami Vault by Sócrates Medina**

*Omnia Vincit Amor*
