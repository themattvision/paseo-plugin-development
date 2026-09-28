---
name: paseo-plugin-development
description: "Create, evolve, test, install, debug, or publish TypeScript plugins for Paseo. Use for plugin UI contributions, daemon RPCs, and provider plugins, not edits to Paseo core."
---

# Paseo Plugin Development

Build the smallest plugin that delivers the requested Paseo behavior while keeping it updateable as a separate project.

## Start from the active host

- Establish the target daemon, its installed Paseo version, and whether the request is for a new plugin, an existing plugin, or Paseo core. Keep plugins separate from core unless the user explicitly asks for a core change.
- For a new plugin, scaffold on the daemon with an absolute path: `paseo plugin init /absolute/path/to/plugin`. Then run `npm install`. The template and its type definitions are the authority for the installed version, so do not transplant APIs from an older plugin or an online example blindly.
- Read the local project instructions before editing an existing plugin. Preserve unrelated changes and do not alter daemon configuration, global switches, or installed plugins outside the requested scope.
- Choose contributions by user outcome, not by novelty. A surface and sidebar item fit a persistent tool, a workspace panel fits workspace context, a Command Center item fits an occasional action, and a daemon RPC exists only when the app cannot safely or usefully do the work alone.

## Design and implementation

- Use the generated plugin structure. Keep client code under `client/`, server code under `server/`, and cross-boundary contracts under `shared/`; the documented root entry files are the only root-level code exception.
- Client code runs on desktop and mobile. Do not import `node:` modules or assume browser APIs. Put browser-only code in `client/web.ts`, gate it by platform, and provide a native fallback when the feature needs one.
- When client and server communicate, define a typed shared contract and register one handler in `index.server.ts`. Return an accurate error state rather than pretending an unavailable platform capability succeeded.
- Make UI theme-aware and compact-layout-aware. Use Paseo theme tokens for visible text and surfaces; use `layout.compact` for spacing and size decisions. Validate icon names against the installed scaffold or SDK before using them.
- Keep the default export in each entry focused on registrations and cleanup. Avoid dependencies, abstractions, or a server process unless the requested behavior needs them.

Read [architecture and UI rules](references/architecture.md) before adding UI, RPCs, browser-specific behavior, or a provider. Read [lifecycle, safety, and release](references/lifecycle.md) before installing, enabling, reloading, debugging, updating, or publishing.

## Verify at the correct boundary

1. Run `npm run typecheck` and the project’s focused tests, if it has behavior beyond registrations.
2. Reload or install only on the requested daemon, then confirm `paseo plugin ls` reports the expected status. Inspect `paseo plugin logs <plugin-id>` when startup or an RPC fails.
3. For UI work, open the target Paseo client on the same host and exercise the actual sidebar item, panel, command, or timeline contribution. A successful typecheck or `running` daemon status does not prove a client contribution mounted.
4. State exactly what was verified, separating static checks, daemon load, and visible client behavior.

## Security and release boundary

Plugins are trusted, unsandboxed code in both the daemon and client. Review the requested source and dependencies, avoid secrets in logs, and do not turn on the daemon-wide plugin switch without first reading its current value and obtaining explicit user approval.

Treat public Git, npm publication, and plugin updates as external releases. Confirm the requested destination and visibility, inspect the files that would leave the machine, then report the resulting URL, revision, and observed release status.
