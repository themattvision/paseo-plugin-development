# Lifecycle, safety, and release

## Create and validate

```bash
paseo plugin init /absolute/path/to/my-plugin
cd /absolute/path/to/my-plugin
npm install
npm run typecheck
```

`npm install` supplies development dependencies for typechecking and tests. Paseo supplies its SDK and runtime dependencies when the plugin runs. Do not add runtime dependencies before checking whether the generated project, TypeScript, or a Paseo API already covers the need.

Before calling an install or reload command, make sure the plugin’s manifest ID and its requested daemon are the intended targets.

```bash
paseo plugin install /absolute/path/to/my-plugin
paseo plugin ls
paseo plugin logs my-plugin
```

After source changes:

```bash
npm run typecheck
paseo plugin reload my-plugin
paseo plugin ls
```

If reload fails, use the logs to correct the source and reload again. Do not claim the plugin works because it compiles. Verify daemon status, then exercise the contribution in the actual Paseo client connected to that daemon.

## Plugin switch and trust

Plugins execute with the daemon user’s access and client code runs inside Paseo. Read the source, review dependencies, and inspect update diffs before running unfamiliar code.

`pluginsEnabled` is a daemon-wide switch. If it is off, first read the current daemon configuration. Explain that enabling it affects every plugin on that daemon and ask for explicit approval before changing it. Only then update the configuration and apply it with:

```bash
paseo reload --json
```

Individual plugin enable, disable, reload, log, and removal controls affect only the selected plugin, but still require the user’s intended target to be clear.

## Publication and updates

Public Git repositories and npm packages are releases. Obtain explicit authorization for public visibility or registry publication, check the diff and release metadata, and never include secrets, local configuration, test data, or unrelated files.

For a trusted npm package, installation uses the package source:

```bash
paseo plugin install npm:@scope/package@version
```

Git sources may be added by repository shorthand or URL, with an optional subdirectory and initial ref. Use the current CLI help for exact accepted forms. For existing remote sources, preview an update before accepting it:

```bash
paseo plugin update my-plugin --check
paseo plugin update my-plugin
```

Report the source URL, installed or published revision, plugin ID, version if applicable, and the verification actually observed. Never imply that a reload proves a published package installs successfully on a clean daemon.
