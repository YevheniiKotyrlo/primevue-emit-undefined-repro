# InputText emit `| undefined` — False Positive

Reproduction for [primefaces/primevue#8441](https://github.com/primefaces/primevue/issues/8441).

## The bug

`InputText` declares `string | undefined` in its `update:modelValue` emit type, but it only ever emits `string` at runtime — `event.target.value` on a native `<input>` is always `string` per the HTML spec.

With `strictTemplates: true` and `skipLibCheck: false`, `vue-tsc` rejects `<InputText v-model="name" />` when `name` is `Ref<string>`:

```
error TS2322: Type 'string | undefined' is not assignable to type 'string'.
```

This false positive blocks adoption of `strictTemplates` — a key vue-tsc feature for catching real type errors in templates.

## Quick start

```bash
bun install
bun run type-check    # TS2322 — false positive error
```

To apply the fix:

```bash
bun run patch          # Remove unreachable | undefined from emit type
bun run type-check     # 0 errors
bun run unpatch        # Revert patch
```

## Root cause

`InputText` has a single emission path: `onInput(event)` -> `writeValue(event.target.value)` -> `$emit('update:modelValue', value)`. The value originates from `HTMLInputElement.value`, which is always `string`. No `resetValue()`, `clear()`, or watcher re-emission exists anywhere in the inheritance chain.

The `| undefined` in the emit type was likely added as a defensive measure but doesn't match runtime behavior.

Toggle with `bun run patch` / `bun run unpatch` (uses bun's `patchedDependencies`).
