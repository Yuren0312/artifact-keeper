---
section: Security
issues: [#5456]
---
- **An approved MCP server version is held when a higher-priority Virtual member later shadows its pinned package** (#5456). npm, PyPI, NuGet and Cargo packages are served through `registryBaseUrl` set to the mapped repository, and a Virtual serves a version from the first member, by priority, that holds it. Publishing different bytes at the pinned coordinate in a member that outranks the pinned one changed what the advertised Virtual URL delivered, while the stored pin, the artifact the gate evaluated and the MCP read all still said "approved": quarantining the approved bytes hid the server while the new bytes stayed downloadable, and quarantining the new bytes left the server listed while the download was refused.

  The exact read, the listings and `latest` now check that the Virtual still serves the pinned member's rows. When a higher-priority member holds the same version (published, pulled through or in its proxy cache, at any quarantine state), or the pinned member has left the Virtual, the version is held as `mcp_unresolvable`. The pin never moves: the version serves again once the shadowing version is removed, or publish a new server version to pin what the Virtual serves now. OCI pins (digest-addressed) and MCPB bundles are unaffected.
