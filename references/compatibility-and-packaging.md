# Strapi 5 compatibility and packaging

## Contract

The plugin support target is `>=5.0.0 <6.0.0`. The consuming project's version is an additional test target, not a substitute for the oldest release. Latest documentation and current SDK scaffolds can use APIs absent in early Strapi 5. Do not silently raise the minimum to match copied examples. If an essential capability cannot meet the range, explain the incompatibility and resolve the requirement before claiming completion.

Inspect package exports, source/build entries, shipped declarations/assets, plugin ID, lockfile, Node engines, and the actual package manager. Use the Plugin SDK where appropriate, but verify the chosen SDK/toolchain and generated APIs against the promised runtime range. Do not retrofit an existing plugin solely to match a scaffold.

## Dependency investigation

Query current registry metadata and inspect the actual dependency trees for Strapi/admin 5.0.0, the host, and current stable 5.x. Check React/React DOM, router, styled-components, Design System/icons, internationalization, TypeScript/build tools, and Node compatibility together. Use official package metadata/source instead of a timeless dependency-version table.

- Set peer ranges from demonstrated compatibility; broad ranges alone are not evidence.
- Build-only SDK and compiler tools normally belong in devDependencies, not consumer peers.
- Avoid bundling duplicate host React/context/theme runtimes; verify SDK externalization and package ownership rather than indiscriminately moving every dependency to peers.
- Do not conceal conflicts with `--force`, `--legacy-peer-deps`, or unrelated application-wide overrides.
- Match development typings to the runtime React/API line. If declaration generation fails inside a compiler API, inspect tool/compiler compatibility before patching application code.
- Use release-appropriate Node and dependency versions in each fixture; distinguish an unsupported local engine from a genuine plugin peer/API defect.
- If a user reports an install failure, verify the exact published artifact/package name first. Local fixes do not prove the registry artifact changed.

## Evidence matrix

| Layer | Required evidence |
|---|---|
| Package contract | Valid export paths, plugin metadata, package contents, generated declarations, no host-relative imports or accidentally shipped secrets. |
| Dependency resolution | Real packed plugin resolves in isolated 5.0.0 and current stable 5.x consumers without force flags. A lockfile-only install proves only resolution. |
| Compilation | Admin/server no-emit type checks and plugin build/SDK verification where provided; build the consumer admin against each target dependency line. |
| Runtime | Isolated host boots and loads schemas, extensions, custom fields, services, routes, and permissions; representative allowed/denied API operations run. |
| UI | Actual home/detail/settings/custom-field routes load and behave correctly on target versions; no duplicate context errors or unavailable imports. Check supported themes and responsiveness. |
| Host integration | Intended host works, and a clean host with different mapped fields does not need source changes. |

Test intermediate versions when the plugin uses APIs that changed between endpoints. Passing the oldest and newest releases supports the contract but does not prove every future release. Document exact tested versions and API assumptions, and keep CI coverage aligned with maintained support.

## Practical checks

Use existing scripts after inspecting their effects. Typical checks, run from the plugin directory when present:

```sh
npm run build
npm run verify
./node_modules/.bin/tsc -p admin/tsconfig.json --noEmit
./node_modules/.bin/tsc -p server/tsconfig.json --noEmit
npm pack --dry-run --json
```

Use the repository's package manager equivalents. Do not run a wrapper requiring an unavailable command merely because its name looks standard. `npm pack --dry-run` verifies contents but does not supply an installable archive: pack to an isolated output directory for consumer testing. Inspect lifecycle scripts before running them. Never point compatibility fixtures at a production database or real sending credentials.

Document each target, resolved dependencies, checks and limits. A local build is not publication, and package installation is not UI/runtime proof. Publishing, committing, and deployment remain separately authorized actions.

## Sources

- [Plugin creation and setup](https://docs.strapi.io/cms/plugins-development/create-a-plugin)
- [Plugin SDK commands](https://docs.strapi.io/cms/plugins-development/plugin-sdk)
- [v4 helper-plugin replacements](https://docs.strapi.io/cms/migration/v4-to-v5/additional-resources/helper-plugin)
- [Strapi source and version tags](https://github.com/strapi/strapi)
- [Design System source and releases](https://github.com/strapi/design-system)

Research baseline: 2026-09-29. No example in this skill constitutes an independently runtime-tested plugin distribution. Run the matrix on the plugin being implemented; do not inherit a compatibility claim from this document.
