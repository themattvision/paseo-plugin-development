# Architecture and UI rules

Use the scaffolder supplied by the target daemon. Its current template decides the exact package versions, manifest shape, contract helpers, and supported registrations.

## Module boundary

```text
plugin/
├── paseo-plugin.json
├── index.client.tsx       # app registrations
├── index.server.ts        # daemon registrations, optional
├── client/                # app-only code
├── server/                # daemon-only code
└── shared/                # contracts and pure types
```

`client/` compiles into every Paseo client, including mobile. `server/` compiles into the daemon subprocess. `shared/` is the only place for code imported by both. Do not move ordinary code files to the plugin root, import server modules from the client, or import client modules from the server.

`index.client.tsx` and `index.server.ts` default-export a registration function and return cleanup:

```ts
export default function contribute(context: PluginClientContext) {
  // registrations
  return () => {};
}
```

The server entry is optional for UI-only plugins. The client entry is optional for daemon-only plugins.

## Selecting a contribution

| Need | Prefer |
| --- | --- |
| Persistent, top-level tool | surface plus sidebar item |
| Workspace-specific information or workflow | workspace panel |
| A discoverable, occasional action | Command Center item |
| Contextual composition action | composer pill or attachment source |
| Data or an OS/API action unavailable to the client | server RPC |
| Agent integration as a first-class provider | provider plugin |

Use one primary entry point. Add a second entry point only when it makes the requested task easier to find or use.

## UI that works across clients

- Use React Native primitives and APIs exposed by `@getpaseo/plugin/client`.
- Browser globals belong only in `client/web.ts`. Check `Platform.OS` before their use. On a non-browser client, show a useful native path or an honest unavailable state.
- Drive color from `theme.colors`, not hard-coded colors. Use `layout.compact` for padding, gaps, and text size. `useWorkspace(workspaceId, selector)` is the preferred way for a workspace panel to read only the fields it renders.
- A Lucide icon name is not automatically supported by every Paseo version. Reuse an icon accepted by the generated template or verify it against the installed SDK.

## RPCs and daemon code

Keep shared contracts in `shared/` and handle them in the server entry. Validate input at the daemon boundary, keep error text actionable, and log operational context without tokens, credentials, or private content.

Use a daemon RPC for real daemon-side work, such as a local filesystem operation or a remote integration that must keep credentials out of the client. Do not create an RPC merely to move a pure UI calculation to the server.

## Provider plugins

A provider plugin is a plugin integration, not a reason to emulate Paseo internals. Read the provider documentation for the installed Paseo release before choosing the protocol or timeline contribution. Test provider connection, agent creation, streaming, errors, and cleanup separately from visual rendering.
