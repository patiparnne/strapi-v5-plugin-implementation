# Strapi 5 Plugin Implementation

A Codex skill for creating, extending, debugging, and reviewing reusable Strapi 5 plugins and project-level plugin customizations. It guides architecture, native admin UX, schemas, permissions, host mapping, packaging, and compatibility verification.

## When to use it

- Build or maintain a reusable plugin with server APIs and admin pages.
- Implement custom fields that respect the parent form's save and cancel behavior.
- Integrate a plugin with host content-types through configuration or mapping.
- Customize an installed plugin through a project's `src/extensions` directory.
- Investigate dependency, packaging, or Strapi-version compatibility problems.
- Review plugin architecture, authorization, schemas, or rendered admin behavior.

For ordinary application-owned APIs and business logic, use the companion `strapi-v5-implementation` skill.

## Usage

Invoke the skill in Codex with a concrete task and the relevant project or plugin location:

```text
Use $strapi-v5-plugin-implementation to build a reusable notices plugin.
Support configurable recipient content-type and field mapping, native admin
pages, and permission-controlled APIs.
```

```text
Use $strapi-v5-plugin-implementation to extend this installed plugin with an
organization relation in the host project. Preserve its existing schema and data.
```

```text
Use $strapi-v5-plugin-implementation to review this plugin's packaging and
Strapi 5 compatibility. Do not implement changes yet.
```

Automatic selection is enabled in [agents/openai.yaml](agents/openai.yaml). The skill respects the requested mode: a review or planning request does not authorize implementation. Plugin creation does not authorize publication or production changes.

## Core approach

- **Keep ownership clear.** Reusable plugin behavior belongs in the package; host business fields, tenant rules, credentials, and bindings belong in configuration or project extensions. Prefer configurable mapping while honoring explicitly requested static mappings.
- **Model data intentionally.** Evaluate content-types first for independently managed records. Use components or JSON when values belong to a parent's lifecycle, with server validation.
- **Persist schemas and metadata.** Include meaningful `config.metadatas` in authored schemas. Project schema extensions use `src/extensions/<plugin-id>/content-types/<type>/schema.json`, preserve base fields and configuration, and account for upstream changes.
- **Use native admin controls.** Prioritize the installed Strapi Design System, native page layouts, accessible navigation, localized feedback, and appropriate loading, empty, error, and permission states.
- **Enforce permissions on the server.** Keep domain operations in services and distinguish admin RBAC from Content API token and end-user permissions. Hidden UI controls do not secure endpoints.
- **Verify the compatibility contract.** Target `>=5.0.0 <6.0.0`; check installed source, types, and version-specific APIs rather than assuming current examples work on the earliest release.

The skill includes user-selected UI and metadata conventions. These are documented project expectations, not claims that Strapi itself mandates every design choice.

## Reference guide

[SKILL.md](SKILL.md) is the authoritative entrypoint. Supporting references are loaded according to the task:

| Reference | Covers |
|---|---|
| [Architecture and mapping](references/architecture-and-mapping.md) | Plugin ownership, host mapping, storage, custom fields, services, and permissions |
| [Schemas and metadata](references/schemas-and-metadata.md) | Schema metadata, saved Content Manager configuration, and effective schema checks |
| [Admin design](references/admin-design.md) | Native layouts, navigation, editors, search, pagination, loading, and accessibility |
| [Project extensions](references/project-extensions.md) | Persisted schema overrides, behavior extensions, loading order, and upgrade drift |
| [Compatibility and packaging](references/compatibility-and-packaging.md) | Dependency investigation, package contents, consumer fixtures, and version evidence |
| [Validation and sources](references/validation-and-sources.md) | Acceptance cases, behavioral evaluation, research provenance, and limits |

## Verification expectations

For reusable distributions, verify the packed plugin in isolated Strapi 5.0.0 and current stable 5.x consumers, plus the intended host. Check dependency resolution, compilation, boot, representative API permissions, and actual admin interactions. Use a clean generic host with different mapped fields to verify that integration does not require source changes.

For project extensions, inspect the effective runtime schema and rendered Content Manager, including preservation of upstream fields and existing configuration. For UI changes, check relevant routes, narrow and wide layouts, light and dark themes, keyboard behavior, and failure states.

Report exact tested versions and distinguish installation, build, runtime, and rendered evidence. The support range is a verification target; this skill does not itself certify any plugin's compatibility. Its research baseline is documented in the references, and version-sensitive behavior must be rechecked for the target release.

## Requirements

Use this folder in a Codex environment that supports skills, with access to the relevant repository and its development tools. Runtime and admin verification require suitable isolated Strapi hosts and browser access. Official documentation and package source inform version-sensitive decisions; no originating project repository or external AI/MCP consultation is required.
