---
name: mark-scan
description: Retroactively scan modified files and add @ai-generated markers to AI-written code blocks
disable-model-invocation: true
argument-hint: "[file paths...]"
---

# AI Code Marker - Retroactive Scan

Scan files and annotate AI-generated code blocks with start/end comment markers.

## Target Files

$ARGUMENTS

**If files are specified**: Process only those files.

**If no files specified**: Identify all modified files using git:

```bash
git diff --name-only HEAD
git diff --name-only --cached
git diff --name-only
```

If not in a git repository, ask the user which files to process.

## Comment Syntax Reference

| File Extensions | Start Marker | End Marker |
|----------------|-------------|-----------|
| .js .ts .jsx .tsx .mjs .mts .cjs .cts .java .c .cpp .h .hpp .cs .go .rs .swift .kt .kts .scala .groovy .php .m .mm | `// @ai-generated` | `// @end-ai-generated` |
| .py .pyi .rb .sh .bash .zsh .fish .yaml .yml .toml .r .pl .pm .ps1 .ex .exs .tf .hcl Dockerfile | `# @ai-generated` | `# @end-ai-generated` |
| .html .xml .svg .vue .svelte .astro | `<!-- @ai-generated -->` | `<!-- @end-ai-generated -->` |
| .css .scss .sass .less .styl | `/* @ai-generated */` | `/* @end-ai-generated */` |
| .sql .lua .hs .elm | `-- @ai-generated` | `-- @end-ai-generated` |
| .clj .cljs .cljc .edn .lisp .el .scm .rkt | `; @ai-generated` | `; @end-ai-generated` |
| .erl .hrl | `% @ai-generated` | `% @end-ai-generated` |
| .vim .vimrc | `" @ai-generated` | `" @end-ai-generated` |
| .bat .cmd | `REM @ai-generated` | `REM @end-ai-generated` |

## Procedure

1. Identify target files (from arguments or git diff)
2. Skip non-code files: .json, .md, .txt, .lock, .png, .jpg, .svg (binary), .gitignore
3. For each code file:
   a. Read the file content
   b. Run `git diff HEAD -- <file>` to identify added/modified lines (lines starting with `+`)
   c. Group contiguous added lines into blocks
   d. Wrap each block with the appropriate start/end markers
   e. Skip blocks that already have `@ai-generated` markers
4. Use the Edit tool to add markers
5. Print summary:
   ```
   AI Code Marker - Scan Complete
   Files scanned: X
   Sections marked: Y
   Files skipped (already marked): Z
   ```

## Rules

- Do NOT modify code logic - only add comment markers
- Do NOT add markers inside strings or existing comments
- Do NOT double-wrap sections that already have `@ai-generated` markers
- Place markers on their own lines at the same indentation level as the code
- If an entire file is new (all additions in git diff), wrap the entire content
- For partial modifications, wrap only the added/modified contiguous blocks
- Preserve the file's existing whitespace style
- If a modified block is trivial (single-line import, single value change), skip it

## Example

Given a git diff showing these additions in `utils.ts`:

```diff
+function calculateTotal(items: Item[]): number {
+  return items.reduce((sum, item) => sum + item.price * item.quantity, 0)
+}
+
+function formatCurrency(amount: number): string {
+  return new Intl.NumberFormat('en-US', {
+    style: 'currency',
+    currency: 'USD',
+  }).format(amount)
+}
```

The result after marking:

```typescript
// @ai-generated
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price * item.quantity, 0)
}

function formatCurrency(amount: number): string {
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
  }).format(amount)
}
// @end-ai-generated
```
