# ai-code-marker

Claude Code plugin that marks AI-generated code with comment annotations. Adds `@ai-generated` / `@end-ai-generated` markers to track which parts of your codebase were written by AI.

## Features

- **Toggle mode**: Enable/disable automatic marking per session
- **Retroactive scan**: Mark already-changed files based on git diff
- **40+ languages**: Supports all major languages with correct comment syntax
- **Non-invasive**: Only adds comments, never modifies code logic

## Installation

### From GitHub (recommended)

Add the marketplace:

```
/plugin marketplace add wraithyy/claude-ai-code-marker
```

Install the plugin:

```
/plugin install ai-code-marker@ai-code-marker-marketplace
```

### Local testing

```bash
claude --plugin-dir ./path/to/claude-ai-code-marker
```

## Usage

### Toggle marking on/off

```
/ai-code-marker:mark on
```

From this point forward, all code Claude generates will be wrapped with markers:

```typescript
// @ai-generated
export function processData(input: string[]): Record<string, number> {
  return input.reduce((acc, item) => ({
    ...acc,
    [item]: (acc[item] ?? 0) + 1,
  }), {} as Record<string, number>)
}
// @end-ai-generated
```

To disable:

```
/ai-code-marker:mark off
```

### Retroactive scan

Mark files that were already modified:

```
/ai-code-marker:mark-scan
```

Or target specific files:

```
/ai-code-marker:mark-scan src/utils.ts src/api/handler.ts
```

## Marker format

The plugin uses language-appropriate comment syntax:

| Language | Markers |
|----------|---------|
| JS/TS/Java/Go/Rust/C/C++ | `// @ai-generated` ... `// @end-ai-generated` |
| Python/Ruby/Shell/YAML | `# @ai-generated` ... `# @end-ai-generated` |
| HTML/XML/Vue/Svelte | `<!-- @ai-generated -->` ... `<!-- @end-ai-generated -->` |
| CSS/SCSS/Less | `/* @ai-generated */` ... `/* @end-ai-generated */` |
| SQL/Lua/Haskell | `-- @ai-generated` ... `-- @end-ai-generated` |
| Clojure/Lisp/Scheme | `; @ai-generated` ... `; @end-ai-generated` |

## How it works

- **Default**: OFF. The plugin does nothing until you activate it.
- **`/ai-code-marker:mark on`**: Injects a session-level instruction that tells Claude to wrap all generated code with markers.
- **`/ai-code-marker:mark off`**: Removes the instruction for the rest of the session.
- **`/ai-code-marker:mark-scan`**: One-shot command that reads git diff, identifies AI-written blocks, and adds markers retroactively.

## Rules the plugin follows

1. Never modifies code logic, only adds comments
2. Does not mark trivial changes (typo fixes, single value changes)
3. Does not mark non-code files (JSON, markdown, plain text)
4. Does not double-wrap already-marked sections
5. Matches indentation of the wrapped code
6. Respects the file's whitespace style (tabs vs spaces)

## License

MIT
