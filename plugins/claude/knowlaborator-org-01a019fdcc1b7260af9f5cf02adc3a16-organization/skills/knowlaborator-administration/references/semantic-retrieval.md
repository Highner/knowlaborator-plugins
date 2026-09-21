# Knowledge policy and semantic retrieval administration

Use these operations only for an explicit administrator request. Treat policy,
provider, profile, coverage, and Workspace assignment as infrastructure state,
not as ordinary knowledge content.

## Inspect first

- Read `get_knowledge_policy` before `update_knowledge_policy`.
- Use `list_embedding_providers` and `get_embedding_provider` to resolve one
  exact configured provider. Use `test_embedding_provider` only on explicit
  request; report safe availability and validation state, never response bodies
  or credentials.
- Use `list_embedding_profiles` before creating or activating a profile. Read
  `get_embedding_profile_coverage` for aggregate operational coverage.
- Read `get_workspace_embedding_profile` before changing one exact Workspace
  with `update_workspace_embedding_profile`.

Provider connections requiring credentials are created through the browser or
REST administration flow. Never request, display, log, or persist a credential,
token, provider response body, or private content sample.

## Change safely

Preserve exact provider, profile, Workspace, model, dimension, distance,
normalization, projection-version, and revision identifiers returned by live
tools. Create profiles as immutable configurations. Before
`activate_embedding_profile`, explain the organization-wide indexing and
retrieval effect. Before changing a Workspace profile, name the exact Workspace
and current and proposed profiles.

Treat coverage as aggregate operational status, not evidence about private
content. Coverage and profile activation include current Document chunks,
knowledge revisions, and persisted Cases in the selected Workspaces. Report the
resulting policy revision, profile activation, Workspace assignment, coverage,
and any safe degradation state.
