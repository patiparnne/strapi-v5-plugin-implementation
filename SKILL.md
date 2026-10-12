---
name: strapi-v5-plugin-implementation
description: Create, extend, debug, or review reusable Strapi 5 plugins, plugin admin pages, custom fields, package compatibility, and host-project src/extensions customizations. Use for plugin implementation and plugin extension work; ordinary application-owned APIs and business logic belong to strapi-v5-implementation.
---

# Strapi 5 plugin implementation

Build reusable, API-ready plugins with native admin UX and support from Strapi 5.0.0 through the 5.x line. Also use this skill to extend plugins at project level without contaminating their reusable core. Respect the requested mode and authorization; creating a plugin does not authorize publishing or production changes.

## Establish ownership and compatibility first

- Inspect the plugin's package, registered ID, admin/server exports, configuration, content-types, routes, services, custom fields, tests, and consumers. Preserve existing changes and independent/nested repository boundaries.
- Decide whether the requested behavior is reusable plugin functionality or host integration. Keep host content-type names, business fields, tenant models, credentials, and project defaults out of the reusable plugin unless explicitly requested.
- Offer a configurable content-type/field mapper first. Explicit static mapping is acceptable; preserve that user choice and isolate host-specific bindings in project configuration/extensions where possible. Do not force a settings UI for a plugin that needs no host mapping.
- Read target-version primary docs and installed source/types. Later Strapi 5 APIs, Design System props, package ranges, and scaffold defaults are not automatically available in 5.0.0.

## Required conventions

- Evaluate content-types first for independently managed addable data, including settings and data edited by custom fields. Components/JSON are acceptable for parent-owned values with a stated reason. Decide admin visibility, private attributes, API exposure, and authorization separately.
- Every authored schema JSON includes meaningful `config.metadatas`. Verify the rendered Content Manager and preserve intentional saved configuration.
- Project schema extensions always include `src/extensions/<plugin-id>/content-types/<type>/schema.json`; generated declarations and runtime-only mutation are not the schema source. Preserve base attributes/config and check upgrade drift.
- Use Strapi Design System strictly for UI and native Strapi page layouts. Home actions align in the right header slot; inner pages without a plugin navigation panel default to native breadcrumbs in `Layouts.Action startActions` (see the admin-design reference). Always provide Back on inner plugin pages in `navigationAction` using Design System `Link` integrated with the admin router, including pages with breadcrumbs; never use a custom Back button; Save/Create/Submit use `primaryAction`. Workspace bodies avoid decorative widget nesting; settings may use grouped panels.
- Repeatable item editors default to Accordion with **+ Add** right of the group label, a labelled trash action in every item header, and a six-dot reorder handle before the label only when ordering is required. Buttons beside normal fields use `size="L"`; verify alignment and accessibility.
- Keep domain operations in server services and make appropriate functionality available through permission-controlled APIs. Register/enforce admin RBAC separately from Content API token/end-user permissions; UI hiding and schema visibility do not secure endpoints.
- Target `>=5.0.0 <6.0.0`. Verify packaged installation, compilation, boot, and representative runtime/admin behavior on 5.0.0 and a current stable 5.x. Do not silently raise the minimum or use forced installs to hide incompatibility.

The UI and mandatory metadata policies above are user-selected conventions. Versioned framework behavior and conditional implementation choices are explained in the references.

- Listing pages use a localized `Layouts.Header subtitle` such as "2 entries found" when the result total is positive (singular for one); preserve the existing descriptive subtitle for zero results. Use the filtered total, not only the current page length. Inner detail pages put useful document/session IDs in the header subtitle instead of a separate body row. Header `primaryAction` buttons keep the native default size; do not set `size="M"`.
- Search uses native `Searchbar` with the built-in admin interaction: at medium (768px) and above, start with a fixed-width search icon button matching the searchbar/companion-field height; companion fields stretch into available space. Click opens and focuses the searchbar. Blur collapses only an empty query; preserve nonempty queries. Below medium, always show a full-width searchbar without the icon toggle. Preserve Enter, clear/Escape, accessible labels, and pagination reset. See the admin-design reference.

## Component selection and loading

- Use Strapi Design System `EmptyStateLayout` with a localized `content` message for empty admin collections, archive lists, and table results. Keep empty states distinct from loading, errors, and permission denial; preserve meaningful messages for no matching results versus unavailable data. Use optional native `icon` or `action` only when the workflow calls for it.
- For section-level loading, prefer `EmptyStateLayout` with localized loading `content` and a native `Loader` in its supported `icon` prop (`icon={<Loader>Loading label</Loader>}`), not `startIcon`. Give the loading region `role="status"` and `aria-busy="true"`; show no-results content only after loading completes. Keep native full-page loading for page initialization and retain existing results during background refreshes when appropriate. Verify props against the installed Design System version.

- Give installed Strapi Design System components the highest priority for admin UI. Use the matching native control, including `Searchbar` for search and `TimePicker` for time selection. Fall back to a custom control only when the installed Design System has no suitable component or its capabilities do not match the actual requirement; document the mismatch and preserve native tokens and accessibility. Inspect supported props and match companion field heights at every responsive breakpoint.
- Always use Strapi Design System `Pagination` with its native `PreviousLink`, `PageLink`, `NextLink`, and `Dots` controls for admin pagination. Hide pagination when the filtered total fits in one page (`pageCount <= 1`, including exactly one full page); derive the threshold from the API page size, not a hard-coded item count. Preserve query filters in navigation links, support URL state and browser Back/Forward, and respect cursor APIs without inventing total page counts. Verify boundary states, keyboard access, and responsive layout against installed types.
- Standard `Button` uses its native `loading` prop. Do not put a custom `Loader` in its children or `startIcon`; retain the action label and normal icon.
- `IconButton` has no `loading` prop in the current Design System. A small 24px by 24px `Loader` is allowed, but preserve the original outer dimensions by reducing padding: S/M 32px square with 4px padding; L 40px square with 8px padding. Ordinary icons are 16px square. Check border-box sizing and rendered dimensions against the installed version; loading must not enlarge the button.

## Read references by task

| Task | Reference |
|---|---|
| New plugin, host mapping, data ownership, custom fields, routes/permissions | [Architecture and mapping](references/architecture-and-mapping.md) |
| Any content-type/component schema or metadata change | [Schemas and metadata](references/schemas-and-metadata.md) |
| Any admin UI | [Admin design](references/admin-design.md) |
| Project-level plugin extension | [Project extensions](references/project-extensions.md) |
| Scaffolding, dependencies, distribution, all-v5 support | [Compatibility and packaging](references/compatibility-and-packaging.md) |
| Acceptance cases, skill evaluation, source provenance | [Validation and sources](references/validation-and-sources.md) |

## Complete the actual contract

Check both a clean generic host and the intended host. For UI, inspect actual routes and interactions, not build output alone. Report compatibility evidence per tested version and distinguish install, build, runtime, and rendered proof. Neither access to the originating project nor an external model/MCP is required to use this skill.
