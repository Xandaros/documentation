# Modules

All code in PureScript is contained in a module. Modules are introduced using the `module` keyword:

```purescript
module A where
  
id x = x
```

## Importing Modules

A module can be imported using the `import` keyword. This is called an "open import" - it will create aliases for all of the values and types in the imported module:

```purescript
module B where
  
import A
```

Alternatively, a list of names to import can be provided in parentheses:

```purescript
module B where
  
import A (runFoo)
```

Values, type constructors, data constructors, and type classes can all be explicitly imported. A type constructor can be followed by a list of associated data constructors to import in parentheses. A double dot (`..`) can be used to import all data constructors for a given type constructor:

```purescript
module B where

import A (runFoo, Foo(..), Bar(Bar))
```

Type classes are imported using the `class` keyword:

```purescript
module B where

import A (class Fab)
```

### Hiding imports

It is also possible to exclude some names from an open import with the `hiding` keyword. This is useful to avoid import conflicts between modules:

```purescript
module C where
  
import A hiding (runFoo)
import B (runFoo)
```

## Qualified Imports
  
Modules can also be imported qualified, which means that their names will not be brought directly into scope, but rather, aliased to a different module name. This can be helpful when avoiding naming conflicts:

```purescript
module Main where
  
import Data.Array as Array
  
null = ...

test = Array.null [1, 2, 3]
```
  
Here, the name ``null`` would ordinarily conflict with ``null`` from ``Data.Array``, but the qualified import solves this problem. ``Data.Array.null`` can be referenced using ``Array.null`` instead.

## Module Exports

You can control what gets exported from a module by using an export list. When an export list is used, other modules will only be able to see things which are in the export list. For example:

```purescript
module A (exported) where

exported :: Int -> Int
exported = [...]

notExported :: Int -> Int
notExported = [...]
```

In this case, modules importing `A` will not be able to see the `notExported` function.

The types of names which can be exported is the same as for module imports.

Imported modules can be re-exported in their entirety:

```purescript
module A (module B) where

import B
```

When re-exporting other modules, all local values and types can also be exported by specifying the module itself as an export:

```purescript
module A (module A, module B) where

import B

data ...
```

### Exporting type classes

To export a type class, simply add it to the list, together with all of its members. Unfortunately there is no short-hand for exporting all type class members in one go.

For example, suppose we have the following:

```purescript
class Foldable f where
  foldr :: forall a b. (a -> b -> b) -> b -> (f a) -> b
  foldl :: forall a b. (b -> a -> b) -> b -> (f a) -> b
  foldMap :: forall a m. (Monoid m) => (a -> m) -> (f a) -> m
```

Then you can export the class with:

```purescript
module Test (class Foldable, foldr, foldl, foldMap) where
```

If a type class is exported, then all of its members must also be exported. Likewise, if a type class member is exported, the type class it belongs to must also be exported.
