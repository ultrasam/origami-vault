# VERIFICATION_POLICY.md

## Origami Vault Verification Policy

**Version:** Draft 1.0

**Status:** Draft

---

# Purpose

This policy defines when a value or a domain-owned claim, such as a credit, may be marked Verified, as described in `docs/DATA_MODEL/02_PROVENANCE_DOMAIN.md`.

A `CurationDecision` that verifies a value identifies the version of this policy that it applied.

---

# Criteria

A value or claim may be marked Verified only when all of these are true:

- **Evidence is inspectable:** At least one source is linked with a locator, such as a page or timecode.
- **The source is strong enough:** There is one primary source or two independent secondary sources.
- **The reviewer is independent:** An authorized curator who did not assert the claim or contribute the value makes the decision and records why.
- **No credible contradiction is unresolved:** Otherwise, the value is marked Disputed.

---

# Source Strength

- **Primary sources** include the first publication and the designer's own record.
- **Secondary sources** include later publications and other works that report on the subject.
- **Independent sources** do not rely on one another. Two copies of the same claim count as one source.

---

# What Is Never Enough

These do not verify a value on their own:

- Imported metadata.
- Popularity or votes.
- Reputation.

---

# Values That Cannot Be Verified

A value that does not meet these criteria remains Unreviewed or Unverified. Incomplete knowledge is a valid state and must not prevent preservation.

---

Omnia Vincit Amor
