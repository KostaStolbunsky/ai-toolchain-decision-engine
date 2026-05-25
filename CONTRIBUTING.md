# Contributing to ATDE

Thank you for contributing to the AI Toolchain Decision Engine.

---

## How to contribute

### Adding or updating a tool in the registry

1. Copy the template from `registry/tools/seed/` as a reference.
2. Fill all required fields per the schema in `model/tool-registry.md`.
3. Set `status: pending` and `data_source: user_submitted`.
4. Fill `contributed_by` with your GitHub username.
5. Declare any affiliation with the tool in the PR description.
6. Open a pull request to `main`.

### Updating model or architecture files

1. All model changes must be discussed in an issue first.
2. Breaking changes to `model/METAMODEL.md` require a version bump.
3. New enum values must be added to the relevant file in `model/types/` — do not hardcode values.

### Updating type registries

1. Adding a new value → minor version bump in the type file.
2. Removing or renaming a value → major version bump + deprecation notice.

---

## Security review checklist

Every contribution that touches the following areas **must** include a security review before merge:

### Registry entries
- [ ] Primary source link provided (official docs or pricing page — not third-party)
- [ ] No affiliation conflict declared or properly disclosed
- [ ] `status: pending` set — not `active`
- [ ] `last_verified` date is current

### Scoring or model changes
- [ ] Change is backwards-compatible or version-bumped
- [ ] No criteria scores modified without corresponding source reference
- [ ] EvaluationFrame weight changes are logged

### API or data flow changes
- [ ] No new PII collected without explicit justification
- [ ] Rate limiting considered for new endpoints
- [ ] New write operations are audit-logged

### Audit trail or override log changes
- [ ] Append-only constraint preserved
- [ ] No delete or update operations introduced on log records

---

## Code of conduct

Be direct, respectful, and constructive. Contributions that misrepresent tools, contain false information, or are designed to manipulate recommendations will be rejected.
