- Feature Name: dead-code-lint-depraction
- Start Date: 2/28/2026
- RFC PR: [rust-lang/rfcs#0000](https://github.com/rust-lang/rfcs/pull/0000)
- Rust Issue: [rust-lang/rust#0000](https://github.com/rust-lang/rust/issues/0000)

<!-- 
    If you edit this make sure than any mention of the unused lint category has 
    "unused" in a code block so its clear that its not saying the lint(s) 
    themselves are unused.
-->

## Summary
[summary]: #summary

Deprecate the `dead_code` lint and give each item linted by `dead_code` its own item-specific `unused` lint.

## Motivation
[motivation]: #motivation

In many cases, a user will want to silence a `dead_code` warning for
a specific category of items, such as all imports while refactoring or
dead variants of not dead functions. Since this is not possible,
they will have to choose between allowing all things covered by `dead_code`
or in that context or not silencing the warning.
For functions specifically, it is very rare to wish to silence all `dead_code`
lints inside the function, most often it is the function itself that it is
dead and silencing all `dead_code` lints on its interior is a negative side-effect.
The same is likely true for dead modules but they are far rarer so there is
little to say either way.
The reason for deprecation and not giving dead code its own category, is that
many, if not most programers new to Rust but not programming in general,
upon seeing a `dead_code` lint name, will assume it covers most, if not all,
of the things in the `unused` category, especially so for the `unreachable_code`
lint.
There is also no apparent rhyme or reason as to why a given item gets its own `unused`
lint right now or is grouped in with `dead_code`, i.e. why are imports not in `dead_code`?
It makes it clear what kind of `dead_code` is expected in a given context when reading code.

## Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

For existing Rust programmers:
The `dead_code` lint is now deprecated, each of the different items it covered
have their own lints now so if you have a dead `struct` you'd use an
`#[expect(unused_data_structures)]` to expect that instead of `dead_code`. 
The exact list can be found [here](https://doc.rust-lang.org/rustc/lints/listing/warn-by-default.html#dead-code).
(The dead code lint docs would be updated to have a table with each thing covered and what now covers that).
For new Rust programmers:
Each type of unused code has its own lint, but they can be collectively toggled
by the `unused` lint, the exact list of lints for unused code can be found [here](https://doc.rust-lang.org/beta/rustc/lints/groups.html).
(There'd be nothing to distinguish between `unused` lints currently under `dead_code` and ones not under it.).

## Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

The proposed list of items linted and the lint:

Modules......................................`unused_module`<br>
Functions....................................`unused_functions`<br>
Type Aliases...............................`unused_type_alias`<br>
Structs, Enums, Unions..........`unused_data_structures`<br>
Consts/Statics............................`unused_compile_time_var`<br>
Traits..............................................`unused_traits`<br>
Macros.........................................`unused_macros`<br>


During the transition period, `dead_code` would be changed to act 
as a subcategory of `unused` that contains these lints and emit a 
deprecation warning.

## Drawbacks
[drawbacks]: #drawbacks

More lint names for everyone to remember, though this can be 
mostly negated by adding the name to warning.
There is no way at this time to silence a warning on an attribute,
so it would be impossible to silence the deprecation warning without
silencing all deprecation warnings in the context.
Given that the codebase would be a legacy codebase, it is likely that
it'd already make sense to silence deprecations or there is little 
concern in doing so. 
There would be no other solution for this in versions of Rust
with this deprecation, as an individual version of Rust can't
get an update, that'd be a different version of Rust.

## Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives

Provide a way of applying a lint to a block/item itself rather than the item and everything inside of it.
This would also be useful outside of this but doesn't fix most of the other motivations.

## Prior art
[prior-art]: #prior-art

I have not found anything notable in my research.

## Unresolved questions
[unresolved-questions]: #unresolved-questions

- What should the name of the lint for const and static variables be, 
the currently proposed name is not very good? Or should they be separated
instead?

## Future possibilities
[future-possibilities]: #future-possibilities

- Allow applying a lint to a block itself without applying to all statements/items in the block.
