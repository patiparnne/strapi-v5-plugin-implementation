# Plugin architecture, mapping, and permissions

## Keep the package independent

Plugin-owned content-types, custom fields, service rules, and operational records stay in the plugin. Host-specific relations, identifiers, tenant rules, endpoints, and field conventions belong to host configuration/adapters/extensions. Use namespaced collections, routes, settings, translations, and custom-field identifiers; keep the registered plugin ID consistent across admin/server/configuration/extension paths. A directory name or npm package name is not necessarily that ID.

Use supported configuration defaults and validation for server options; expose admin-editable integration settings only when operators need them. Optional integrations must declare/check dependencies and have useful unconfigured states. Do not silently require another private plugin, a particular database, provider, or queue. If a real technical constraint is unavoidable, document it and confirm it against the requested reuse contract rather than implying universal support.

Avoid imports from outside the package, hidden host bootstrap requirements, hard-coded `api::` UIDs in generic runtime logic, and tests that only work with the originating application's schema. Explicitly requested static bindings are a supported exception, not permission to spread business coupling across the plugin.

## Mapper-first integration

Offer a configuration flow that selects a content-type, then compatible fields for the plugin's domain roles. For example, an audience plugin selects a recipient collection and address/display-name/consent fields instead of assuming a `customer.phone` schema. Keep mapping discovery and execution in a reusable server service.

- Restrict schema discovery to authorized administrators and eligible models/fields. Do not expose arbitrary schema metadata, private attributes, or all records through a convenience endpoint.
- Validate UIDs, field existence, value types, relation traversal, and scalar/multiple cardinality on save and use. A saved mapping can become stale after a schema change.
- Define behavior for optional mappings, null values, deleted fields, and ambiguous paths. Fail with an actionable configuration error rather than silently switching to guessed host fields.
- Nested paths are optional capability, not a requirement to build a universal query language. Use a compatible existing mechanism when appropriate; declare optional dependencies. Bound traversal, population, record counts, and user filters.
- Treat mappings as data, not executable expressions. Never evaluate arbitrary code supplied as a mapper.
- Apply both plugin operation authorization and the intended mapped-data access policy. If a workflow deliberately reads under a service identity, define that privilege explicitly; a mapping must not silently bypass tenant/owner restrictions or leak private fields to lower-privileged callers.
- Validate explicit static mappings by the same rules; do not reintroduce a mapper UI against the user's instruction.

## Storage and lifecycle

Evaluate content-types first for addable records with independent identity, querying, reuse, relations, permissions, or lifecycle. This includes configurable providers, rules, channels, and independent custom-field option sources when the feature needs them. Choose public/private exposure based on the feature; internal operational types normally need no public CRUD.

Embedded components or JSON fit values owned and saved with a parent, such as a small request-header list. Explain the choice and implement server-side shape validation. A global settings record is appropriate for scalar configuration, not automatically for growing collections of independent entities. Secrets should not be returned simply because their record is editable in admin.

Use Document Service for document operations by default. Treat document IDs, locale/publication variants, and relation semantics correctly. Low-level operations need explicit reasoning and equivalent safeguards. Register document middleware and custom fields at the appropriate lifecycle stage; start necessary workers after initialization and stop them in `destroy`. Inspect target-version lifecycle contracts rather than assuming ordering.

For external side effects, cover duplicate requests, concurrency, bounded retries, partial failure, safe logs, and restart handling as relevant. Never turn a UI save or plugin bootstrap into an unbounded job or unsolicited external send.

## API-ready means enforceable server functionality

Use server services for core operations so admin and authorized Content API callers share domain rules. Define/export route groups, controllers, policies, validation, and sanitization deliberately. Not every settings secret or internal content-type needs a public endpoint.

| Boundary | Required behavior |
|---|---|
| Admin | Register named plugin actions, expose them in role configuration, protect server routes with authentication and action checks, and gate menu/pages/actions consistently. |
| Content API tokens | Expose appropriate actions/scopes and enforce them server-side. Verify limited tokens as well as full tokens. |
| Users & Permissions | If supported, verify role configuration and end-user authorization separately from admin RBAC; do not require this optional plugin without declaring it. |
| Anonymous/public | Deny by default for privileged operations. Intentionally public callbacks/webhooks require their own signature/state/replay or equivalent feature-specific verification. |

Return only necessary fields; sanitize using the actual content-type and caller. Do not assume Document Service enforces HTTP permissions or that `private` protects manually constructed response objects. A route file named `content-api` might be remapped as admin: trace final registration before drawing conclusions. Do not automatically grant permissions to every role during bootstrap.

## Custom-field contract

Register matching name, plugin identifier, and supported underlying type on server and admin. Custom fields cannot introduce new database types or use relation/media/component/dynamic-zone types as their underlying storage. A JSON editor may store parent-owned values, but JSON IDs are not managed Strapi relations and do not gain referential integrity automatically.

Use the installed custom-field input contract for value, change events, name, disabled/required, errors, internationalized labels, focus/ref handling, and input sizing. Preserve the parent's save/cancel behavior; do not independently persist each keystroke unless that workflow is explicitly designed. Validate at the server for all write paths, not only in React.

## Sources

- [Server API](https://docs.strapi.io/cms/plugins-development/server-api)
- [Admin Panel API](https://docs.strapi.io/cms/plugins-development/admin-panel-api)
- [Custom fields](https://docs.strapi.io/cms/features/custom-fields)
- [Users & Permissions](https://docs.strapi.io/cms/features/users-permissions)
- [Controllers and sanitization](https://docs.strapi.io/cms/backend-customization/controllers)

Research baseline: 2026-09-29. Verify APIs and supported capabilities against every claimed compatibility target.
