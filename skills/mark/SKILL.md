---
name: mark
description: Toggle AI code marking on/off. When ON, all AI-generated code is wrapped with @ai-generated / @end-ai-generated comment markers. Use when user wants to track which code was written by AI.
disable-model-invocation: true
argument-hint: "[on|off]"
---

# AI Code Marker - Toggle

This skill controls whether AI-generated code is annotated with markers.

## Arguments

- `/ai-code-marker:mark on` - Enable marking for this session
- `/ai-code-marker:mark off` - Disable marking for this session

Parse `$ARGUMENTS` to determine the action.

---

## When "on"

Respond with a confirmation that AI code marking is now ENABLED, then follow these rules for ALL subsequent code generation in this session:

**CRITICAL INSTRUCTION**: From this point forward, every time you write or modify code using the Write or Edit tools, you MUST wrap the AI-generated sections with annotation markers using the correct comment syntax for the file type.

### Comment Syntax Reference

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

### Marking Rules

1. When creating a **new file**, wrap the entire content (after imports/declarations that are obvious boilerplate):
   ```typescript
   import { useState } from 'react'

   // @ai-generated
   export function MyComponent() {
     const [count, setCount] = useState(0)
     return <button onClick={() => setCount(c => c + 1)}>{count}</button>
   }
   // @end-ai-generated
   ```

2. When **editing an existing file**, wrap only the new/modified code block:
   ```python
   def existing_function():
       pass

   # @ai-generated
   def new_ai_function(data: list[int]) -> int:
       return sum(x for x in data if x > 0)
   # @end-ai-generated

   def another_existing():
       pass
   ```

3. **Do NOT** add markers:
   - Inside strings or existing comments
   - Around trivial changes (fixing a typo, changing a single value)
   - Around non-code files (JSON, plain text, markdown, config without logic)
   - If `@ai-generated` markers already exist on that section (no double-wrapping)

4. Match the indentation level of the code being wrapped
5. Respect the file's existing whitespace style (tabs vs spaces)

---

## When "off"

Respond with a confirmation that AI code marking is now DISABLED. From this point forward, generate code normally without any `@ai-generated` / `@end-ai-generated` markers.

---

## When no argument provided

If `$ARGUMENTS` is empty or missing, explain the usage:

```
AI Code Marker - Mark AI-generated code with comment annotations

Usage:
  /ai-code-marker:mark on    Enable marking for this session
  /ai-code-marker:mark off   Disable marking for this session
  /ai-code-marker:mark-scan  Retroactively mark changed files (separate skill)

Current status: marking is OFF (default)
```
