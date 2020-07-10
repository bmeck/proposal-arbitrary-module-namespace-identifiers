# Arbitrary Module Namespace Identifiers

Allow the ability to create modules which integrate using non-IdentifierName bindings.

## Problem

Other languages can produce values that do not match the potential strings that match IdentifierName. E.G. due to [name mangling](https://en.wikipedia.org/wiki/Name_mangling).

WebAssembly can already create such bindings since they [only constrain bindings to be valid
utf8 strings](https://webassembly.github.io/spec/core/binary/values.html#binary-utf8).

Non-identifier names can already be imported from ModuleNamespaceObjects since [Abstract Module Records](https://tc39.es/ecma262/#_ref_444) have no constraints on their exported names.

```mjs
import * as odd from 'wasm';
odd['@foo']
```

Non-identifier names can be exported using `export * from` similarly.

```mjs
export * from 'wasm'; // will export "@foo"
```

However, non-identifier names cannot be imported by name, exported by name, nor exported from by name. This proposal seeks to allow integration such that support to create bindings with non-identifier names can be used in those locations.

## Syntax

Alteration of `ExportSpecifier` and `ImportSpecifier` are the only changes proposed:

```mjs
import {"@foo" as foo} from "wasm";
export {foo as "@foo"};
export {"@bar" as bar} from "wasm";
```

### Concerns

WebAssembly has a constraint that names must be valid UTF-8. In order to not break integration literals for the syntax must ensure they produce valid UTF-8. This means an early error must be performed so that any literal in these locations which disallows unpaired surrogates.
