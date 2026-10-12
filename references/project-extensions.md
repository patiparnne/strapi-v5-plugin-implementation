# Project-level plugin extensions

## Select the ownership boundary

Use an extension when an installed plugin needs project-specific schema or server behavior. Use plugin configuration/mapping first when it already supports the desired integration. Change the reusable package itself only for genuinely reusable functionality or an explicitly requested package change.

Identify the registered plugin ID and content-type singular name. Persist schema changes at:

```text
src/extensions/<plugin-id>/content-types/<singular-name>/schema.json
```

Create this JSON even when an existing project uses programmatic schema mutation. Generated `contentTypes.d.ts` describes the loaded system; it cannot define a field. Do not edit package files under `node_modules` as the implementation.

## Preserve the effective schema

The inspected Strapi 5.42.1 loader applies `{ ...baseSchema, ...extensionSchema }`. It does not recursively merge `attributes` or `config`. When overriding one of those objects, persist the complete intended object: preserved base entries plus host additions/overrides. Preserve collection identity, required constraints, relations, options, custom-field references, and metadata unless the requested change deliberately alters them.

Example: if the base declares `name` and `enabled`, an extension containing only `attributes.organization` is unsafe under this loader. Persist `name`, `enabled`, and `organization` with the intended metadata, then verify the runtime model contains all three. Merely passing a JSON parser or TypeScript check does not prove the database/runtime contract.

Follow the actual loading order: base plugin → extension JSON → extension server interface → application registration. Look for later mutations that override the persisted declaration. Avoid two conflicting sources for the same host field. An existing runtime hook can remain for necessary behavior; it must not silently negate the JSON schema.

## Extend behavior safely

Use `src/extensions/<plugin-id>/strapi-server.ts` (or the project's JS equivalent) for controllers, services, routes, or policies. Preserve unrelated members. If the plugin exports controller/service factories, wrap the factory and resolve its original result; do not assign methods onto an unresolved factory. Verify signatures on the installed plugin version.

Preserve auth configuration when extending routes. Keep host authorization and tenant fields in the host extension/adapters. Do not silently widen route access or suppress upstream validation. Do not assume `src/extensions` supports arbitrary React replacement: use documented admin extension points where available; source patches/forks require a justified maintenance plan and must not be presented as a stable extension API.

## Upgrade and metadata checks

- Record the upstream package/version used to derive each overridden object in project-maintained notes/tests, not unsupported schema properties.
- Compare the new upstream schema with the prior base on upgrades. Detect added/changed/removed fields, constraints, relations, and metadata. A copied attributes object can hide new upstream fields indefinitely.
- Preserve deliberate host overrides while incorporating new upstream requirements. Do not automatically drop or recreate data when resolving drift.
- Include `config.metadatas` for the resulting declared attributes. See [Schemas and metadata](schemas-and-metadata.md) for saved configuration precedence and targeted reconciliation.
- Rebuild the project/plugin as appropriate so extension JSON is present in the loaded output. Check for stale compiled runtime hooks. Regenerate types and inspect the effective model after restart in an isolated environment.
- Test clean install and existing data, relation operations, API behavior, and rendered Content Manager fields. Verify both base fields and host additions survive.

## Sources

- [Official extension mechanisms and order](https://docs.strapi.io/cms/plugins-development/plugins-extension)
- [Server API](https://docs.strapi.io/cms/plugins-development/server-api)

Research baseline: 2026-09-29. Shallow merging was confirmed in installed 5.42.1 source; inspect the target loader and rerun extension tests when supporting another version.
