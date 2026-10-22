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

## Tests

`tests/` is a local consumer package whose manifest points to this checkout.
`SupertraitConstraints.pw` checks complete supertrait types through both parsers,
materialization and freezing; its expected output is `SupertraitConstraints.out`.
`DeriveRootBindings.pw` checks that Hash and struct/enum Eq derives emit ordinary
impl-only parts without adding imports; its expected output is `DeriveRootBindings.out`.

## Derive consumer bindings

Generated parts share their module root's imports and cannot declare `use` or
`pub use`. A root using `@[Hash]` must explicitly import both `Hash` and `Hasher`
from `@Std/Hash`; a root using `@[Eq]` must import `Eq` from `@Std/Core`.
These are ordinary root bindings, not dependencies injected by the generator.

## Usage

Depend on it from a `Plew.toml`:

```toml
[dependencies]
"Plew/Syntax" = { git = "git@github.com:plew-lang/syntax.git", version = "0.1.11-dev.6" }
```

then `use @Plew/Syntax only { ... }`.

See the Plew spec (`spec/04-execution/16-metaprogramming.md`) for the derive model.
