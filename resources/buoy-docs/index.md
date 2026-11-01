---
title: "formatcontrols API"
summary: "Formatting and typed form entry contracts"
category: "Reference"
order: 10
---

# FormatControls 0.1.0

Connects FormatKit, TemporalKit, SettingsKit and FieldKit. The numeric engines,
temporal values and settings stores remain independently usable.

`FormattingPreferences(store)` exposes `Store` (the underlying transactional
Settings engine), `Profile()` (a detached resolved FormatProfile), and `Snapshot()`
(an immutable settings envelope capturing resolved separators and grouping).
Pass snapshots to FieldKit EditSession/CommitField. Persist requested preferences
with `Store.Export()`; snapshots freeze system conventions for one edit.

`FormattingFieldCodec(kind)` implements FieldCodec for `integer`, `number`,
`money`, `percent-ratio`, `percent-points`, `date`, `time`, and `duration`.
`MaskedFieldCodec(pattern, rawInput)` also implements FieldCodec using MaskKit
grammar. The caller selects raw-slot or formatted entry explicitly; canonical
storage contains normalized raw slots, and successful commits insert punctuation.
There is no rewrite during native typing. This is separate from live-mask editing
and Xojo ValidationMask grammar.

`Import formatcontrols.FormatDesigner` adds the parameterless native
`MaskedTextField` for declarative layouts and Studio's Library. Its properties
are `MaskPattern` (empty disables masking), `RawInput` (default true),
`AllowsEmpty` (default true) and `Value` (normalized raw characters). `Placeholder`
and `IsReadOnly` are inherited native properties. An empty Value resets a slotted
field to unset; a required field cannot commit an empty draft. A literal-only
mask displays its constant with an empty raw Value; `ClearValue()` explicitly
unsets it. Explicit clearing (including a nullable empty commit) survives later
mask, entry-mode and required/optional changes until a new value is assigned.
Configure an unset field
before assigning Value when changing to an incompatible mask.

Configuration and programmatic value changes validate before publication; errors
leave the old value and configuration intact. Commit or `CancelEdit()` before
changing them during an edit. `TryCommit()` returns false for invalid/incomplete
drafts and preserves their text and the last committed value. `ValueCommitted`
and `CommitRejected` events report the result. `Session` exposes draft analysis.
For loading another record, `ReplaceValue(rawValue)` validates first, then
explicitly discards any active draft. A failed replacement preserves that draft
and the old value. Ordinary Value/settings assignment during editing still
requires commit or cancellation, preventing accidental draft loss.
Changing `RawInput` does not change the raw storage representation. Oversized
masked input is invalid; masks retain MaskKit's documented limits and ASCII slot
classes. Native typing is not rewritten.

Studio discovers the packaged data-only descriptor and binds its own trusted
pattern editor through `metadata.studioEditorId`. Other designer hosts can use
the ordinary string property editor. Discovery never executes this module.
Studio checks literal Value/MaskPattern compatibility before either edit reaches
source. Computed sibling expressions require editing in Source; they are never
evaluated by the inspector. Both native editors fit the standard 68px slot.
See `examples/formatting-workbench/MaskedContact.bui` in the repository for a
declarative form.

Canonical values are invariant strings. Money retains four decimal places;
integers never pass through Double. Ratio 0.125 means 12.5%, while points 12.5
means 12.5%. Civil dates have no timezone. Time canonical text includes milliseconds;
display may explicitly omit seconds/fractions without changing storage. The
separate time.edit-pattern must preserve all components, including milliseconds.

Settings keys: number.preset (invariant/system/comma/space/indian), language,
date.locale, currency.code, currency.digits (-1 metadata, otherwise 0..4),
currency.symbol (empty means code), currency.suffix and currency.space,
number.grouping/accounting, number.minimum/maximum (0..15), number.rounding (0..4,
MoneyRoundingMode), number.decimal/separator (optional explicit overrides),
number.primary/secondary (0 preset, otherwise 1..9), date.pattern, time.pattern,
time.edit-pattern, time.zone (IANA). Full-profile validation checks numeric conventions, portable
patterns and named zone identity before a settings batch can publish.

Presets describe explicit conventions, not exhaustive locale data. `space` uses
ASCII space. System mode uses FormatKit's host resolution, retaining its platform
and installed-locale constraints. UI language/date locale are stored independently;
portable numeric date/time patterns do not translate month names or AM/PM labels.

`number.grouping=true` permits the resolved convention; a system locale with no
grouping remains ungrouped. A nonempty explicit number.separator supplies a
caller-owned convention and can enable grouping even for that host. Explicit
number.grouping=false always disables it. Snapshots freeze the effective flag,
so the validation-only placeholder separator never becomes active accidentally.
Native long-date styles remain available through the built-in DateTime API.

Field codecs cache the last validated snapshot. A frozen `Snapshot()` normalizes
its number preset to invariant plus explicit resolved conventions; diagnostic
requested/resolved host identity remains available through `Profile()`, and
`Store.Export()` preserves the requested `system` choice for persistence.
Codec kind names are exact lowercase identifiers. Conflicts in the native store
are recovered by constructing a new FormattingPreferences/Settings instance;
there is no automatic native reload or merge.

Native fields must be used on the UI thread. Session/settings/codec instances
are unsynchronized and must each be confined to one thread.

Maintainers run the private validation suite before release. See the repository README for public build instructions.
