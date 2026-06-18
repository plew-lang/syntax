# Plew/Syntax

The shared **syntax layer** for the [Plew](https://github.com/plew-lang) language:
the one lexer, the value-tree AST, the declaration/body parser, and the source
builder — used by **both** the Plew compiler frontend and user-defined
metaprogramming (`plew gen` derive macros).

Keeping a single syntax package means a macro and the compiler can never drift on
tokenization or the shape of the AST (the "two-parser problem" Rust lives with).
Because a derive macro's AST types never cross the `String` boundary of the `plew
gen` runner, multiple versions of this package can coexist in one build — which is
exactly why it lives outside `@Std` (where everything must be single-version).

## Layout

- `src/_.pw` — module root: AST value types (`Kind`/`Tok`/`DeclAst`/`ExprAst`/…),
  the parser free functions (`lex`/`parseProgramAst`/`parseItem`), and the
  `Derive`/`ParameterizedDerive` interfaces.
- `src/Lexer.pw` — the lexer's `impl` (`bytes -> [Token]`).
- `src/Parser.pw` — the parser's `impl` (`[Token] -> AST`).
- `src/Build.pw` — the source builder's `impl` (output side, `Src`).

## Usage

Depend on it from a `Plew.toml`:

```toml
[dependencies]
"Plew/Syntax" = { git = "git@github.com:plew-lang/syntax.git", version = "0.1.0" }
```

then `import @Plew/Syntax with { ... }`.

See the Plew spec (`spec/04-execution/16-metaprogramming.md`) for the derive model.
