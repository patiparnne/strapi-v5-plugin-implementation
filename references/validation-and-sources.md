# Validation and source provenance

## Feature acceptance

Verify neutrality, storage choice, mapping validation, effective schema/metadata, route permissions, custom-field form behavior, runtime compatibility, and rendered UI for the feature being changed. Use the [compatibility matrix](compatibility-and-packaging.md) for reusable distributions and the [extension checks](project-extensions.md) for host modifications. Include failure paths that materially affect data or users.

UI acceptance includes native header/content gutters; right primary actions; inner Back navigation; settings spacing; no unnecessary nested cards; aligned field/button heights; Accordion add/remove/reorder, focus and errors; long labels; narrow/wide viewports; light/dark; and restricted roles. A screenshot of the home page does not verify a detail editor.

## Behavioral skill evaluations

When maintaining the skill, give an evaluator a realistic task plus only this skill and necessary fixture facts. Use isolated temporary output. Assess produced artifacts/decisions rather than regex matching the instructions. Keep this rubric out of its initial prompt.

| Request/fixture | Expected result |
|---|---|
| Build reusable notices plugin; host A has `customer.phone`, host B has `subscriber.address` | Mapper-first design, no implicit host UID/defaults, validation and authorized access on both hosts; unconfigured installation remains usable with clear setup feedback. |
| User explicitly requests static host mapping | Honors the request, isolates the binding, validates it, does not insist on unwanted mapper UI. |
| Add independently managed provider records plus a small parent-owned header list | Evaluates content-types first; independent providers use appropriate records; embedded header storage has a clear lifecycle justification and server validation. |
| Add repeatable custom-field editor with reorder | Uses supported underlying type, matching registration, parent form contract, Accordion actions, stable identity, keyboard ordering and persisted order; no fake relation integrity from JSON IDs. |
| Extend a plugin with `organization`; base has `name` and `enabled` | Persists extension JSON with metadata; base fields remain in effective schema; generated types are regenerated; restart and upgrade drift checks exist. |
| Base plugin upgrade adds a required attribute | Detects copied-object drift and handles new requirements without dropping host additions or data. |
| Change one schema label on an existing install | Inspects stored Content Manager configuration, avoids blanket reset, verifies intended label and unrelated customization preservation. |
| Implement admin and API actions | Separate admin/token/end-user checks; denies anonymous/insufficient permissions; private fields and mapped-data ownership stay protected; no blanket role grants. |
| Plugin builds on a recent minor but imports an API missing in 5.0.0 | Detects incompatibility during baseline consumer testing; finds compatible implementation or reports unresolved contract rather than widening peers or raising minimum silently. |
| Create home, detail, and settings pages | Native header slots/gutters on each surface, detail Back/save placement, direct workspace body, grouped settings allowed, L button/input alignment. |
| Ask only for review | No package modification, migration, publication, or release. |

For code-producing evaluations, run checks on the actual generated fixture. If a runtime/browser/target version is unavailable, retain that as unverified instead of treating a plausible response as a passing implementation test.

## Provenance and limits

Research on 2026-09-29 inspected Strapi 5.42.1 and local LINE Messaging, Loyalty, and SMS plugins. The useful extracted patterns are self-contained here; their repositories are not required:

- Loyalty Home: native page/content spacing and header primary action; not a mandate to copy every control, query, or panel.
- SMS SessionPage: native Back navigation and save action slots.
- SMS Integration: type/field mapping, compatible field choices, unconfigured state.
- SMS Mapper: Accordion editor, group add action, labelled trash action beside the trigger.
- Loyalty reward schema: field-level edit/list metadata in JSON.
- LINE Messaging: content-type/server/admin/custom-field organization. Trace final route registration before copying or assessing auth behavior.

Reference package manifests required later Strapi minors (5.31/5.33), and some reference services/metadata utilities contained host-specific UIDs. They are deliberately not baseline compatibility or neutrality templates. Preserve useful UI composition while correcting those risks in new reusable work.

Official links live beside the guidance they support. Framework APIs require target-version checks; user conventions remain explicit. The skills do not depend on external AI consultation or claim that their example plugin distributions were tested across every Strapi version.
