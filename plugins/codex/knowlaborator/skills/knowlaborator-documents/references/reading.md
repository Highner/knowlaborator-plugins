# Read Documents and Templates

Use list_documents for bounded discovery; filter purpose=document or template
when relevant. Use get_document for logical metadata and the current version ID,
and get_document_version for exact current or historical version metadata and
processing state. Preserve purpose and description separately from documentType.

Use search_content for discovery, then get_document_file with the exact version
ID when the original must be read or verified. Indexed chunks are never a
substitute for the actual file. The shared MCP file instructions describe the
client's resource/file support. The tool returns a resource link, not the file
bytes in its tool result: read that exact link with the client's MCP resource
reader before inspecting or extracting the file.

A Template's current ready file is an example for local work, not generated
output. Its general description explains what it is; a Playbook reference's
guidance explains how that procedure uses it. Neither grants access or consent.
