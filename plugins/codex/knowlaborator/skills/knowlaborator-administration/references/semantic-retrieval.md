# Knowledge policy and semantic retrieval

Use these operations only for an explicit administrator request. Treat policy, coverage
and retrieval configuration as infrastructure state, not as ordinary knowledge content.

- Read `get_knowledge_policy` before `update_knowledge_policy`, and report the resulting
  policy revision.
- Read `get_embedding_profile_coverage` for aggregate indexing coverage of one embedding
  profile. Coverage is operational status, not evidence about private content.
- Embedding providers, profiles, activation and Workspace assignments are configured in
  the browser under **Settings -> Knowledge and search**. Route those requests there.
  Never request, display, log or persist a credential, token, provider response body or
  private content sample.
