# Schemas and Content Manager metadata

## Storage and exposure

Choose a collection type for independently managed records with their own identity, permissions, lookup, relations, or lifecycle; a single type for a genuinely singleton content record. Evaluate these first for dynamic addable entries, including settings. Parent-owned structured values can use components, repeatable components, or JSON when that lifecycle fits. State the reason rather than hiding independently managed data in a settings blob.

Keep credentials in appropriate server configuration/secret storage. Content-type storage alone does not make sensitive data safe. Decide separately: Content Manager visibility, Content-Type Builder visibility, private attributes, exposed routes, API authorization, and output sanitization. A custom JSON editor does not validate data on the server by itself.

## Required JSON metadata

For every authored schema, include `config.metadatas` in the schema JSON. Give declared fields useful edit/list labels and descriptions where they help editors. Set visibility/editability intentionally; choose search/sort flags supported by the attribute type. Do not put Content Manager labels in arbitrary attribute properties. Avoid stale metadata keys and do not mark unsupported field types searchable just for consistency.

Illustrative schema fragment, not a complete content-type:

```json
{
  "attributes": {
    "name": { "type": "string", "required": true }
  },
  "config": {
    "metadatas": {
      "name": {
        "edit": {
          "label": "Name",
          "description": "Name shown to administrators.",
          "placeholder": "",
          "visible": true,
          "editable": true
        },
        "list": {
          "label": "Name",
          "searchable": true,
          "sortable": true
        }
      }
    }
  }
}
```

Check custom-field registrations and optional i18n dependencies before enabling them in a schema. Preserve deliberate publication/localization behavior; do not enable Draft & Publish on internal jobs/logs by habit.

## Persisted configuration can override defaults

The inspected Strapi 5.42.1 Content Manager creates metadata from schema defaults, then merges existing stored metadata over defaults during synchronization. Changing JSON does not establish that an existing editor changed. Validate new-install and existing-install behavior separately.

Inspect effective Content Manager configuration before repair. Prefer supported configuration mechanisms for the target release. If a targeted migration is necessary, restrict it to owned content-types/fields, preserve unrelated layouts/labels, make it idempotent, and verify readback and actual UI. Do not reset all configuration on every boot or couple a reusable plugin's metadata repair to host models. Internal service/storage APIs require explicit version checks and regression tests.

## Application plugin extensions

Persist extension schemas at `src/extensions/<plugin-id>/content-types/<singular-name>/schema.json`; the extension folder uses the registered plugin ID, which may differ from the package/folder name. Treat generated types as output.

The inspected loader merges schema properties at the top level. An extension's `attributes` or `config` object can replace that whole base object. Preserve base fields and config alongside host additions; do not assume a deep merge. Verify the final loaded schema and maintain an upstream drift check. Use `strapi-server.ts` for behavior when needed, while retaining JSON for schema changes. Do not edit installed package files for a host customization.

## Sources

- [Models](https://docs.strapi.io/cms/backend-customization/models)
- [Content Manager](https://docs.strapi.io/cms/features/content-manager)
- [Plugin extensions and loading order](https://docs.strapi.io/cms/plugins-development/plugins-extension)

Source inspection baseline: Strapi 5.42.1, 2026-09-29, `@strapi/core` plugin loader and `@strapi/content-manager` configuration services. The shallow-merge and saved-metadata observations are implementation evidence for that version; recheck the target release.
