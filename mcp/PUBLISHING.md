# Publishing to the Official MCP Registry

Server: `io.github.startekenterprises-ai/agent-commons` (see `server.json` in this folder).
Publishing is an **owner-only, interactive** step — the registry authenticates
via GitHub OAuth/OIDC, so Glenn (owner of the `startekenterprises-ai` GitHub
account) runs it. Estimated time: ~10 minutes once the prerequisites are done.

## Prerequisites (one-time)

1. **GitHub repo exists**: `startekenterprises-ai/agent-commons` (public), with
   this `mcp/` folder committed (README.md, server.py, server.json).
   - The `io.github.startekenterprises-ai` namespace is only claimable by a
     login as that GitHub account (or via GitHub Actions OIDC from that repo).
2. **The package must actually exist in its registry.** `server.json` declares
   a PyPI package named `agent-commons`, and the registry validates that the
   package exists and is owned by the publisher before accepting the listing.
   Two ways to satisfy that:
   - **PyPI (current server.json)**: publish a minimal `agent-commons` PyPI
     package that ships `server.py` (pyproject.toml with the script included).
     The PyPI project description must contain the ownership string:
     `mcp-name: io.github.startekenterprises-ai/agent-commons`
     (it is already in this folder's README.md — keep it in the PyPI long
     description too).
   - **MCPB (alternative)**: bundle `server.py` as an `.mcpb` file, attach it
     to a GitHub release, and change the package entry in `server.json` to
     `"registryType": "mcpb"` with the release-file URL as `identifier` and its
     `fileSha256`. Pick one route; don't publish both half-way.
3. **Install the publisher CLI** (any one of):
   ```sh
   brew install mcp-publisher
   # or download the release binary:
   # https://github.com/modelcontextprotocol/registry/releases
   ```

## Publish

From this folder (the one containing `server.json`):

```sh
mcp-publisher login github        # interactive browser OAuth — owner only
mcp-publisher validate            # optional: schema-check server.json first
mcp-publisher publish
```

Success prints the registry record for
`io.github.startekenterprises-ai/agent-commons` v1.0.0.

## Verify

- https://registry.modelcontextprotocol.io/v0/servers?search=agent-commons
- The listing should appear in downstream directories (Glama, PulseMCP)
  automatically — they import from the Official MCP Registry. PulseMCP was
  previously paused pending this listing; no separate PulseMCP submission is
  needed once the registry record is live.

## Updates

Bump `version` in `server.json` **and** the package version together (they
must match), publish the new package version first, then re-run
`mcp-publisher publish`.
