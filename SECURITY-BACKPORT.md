# GLib 0.17 security backport for Festapp

Branch `festapp/glib-0.17-security` starts at upstream 0.17 commit
`6b109fb807237b5d07aff9a541ca68e9c2191abd` (GLib 0.17.10).

It backports the two-line `VariantStrIter::impl_get` correction from upstream
commit `05dff0ee696f9bcd8617cd48c4b812d046d440cb` / PR #1343:
https://github.com/gtk-rs/gtk-rs-core/pull/1343
https://rustsec.org/advisories/RUSTSEC-2024-0429.html

The mutable C out-pointer is declared mutable and passed as `&mut p`.
No public API, crate version or GTK ABI changes.

Only GLib is a workspace member. Its four internal dependency paths are removed
so a pinned Git patch consumes the same released glib-sys, gobject-sys, gio-sys
and glib-macros packages as the published GLib crate. This avoids mixing Git and
registry copies of the same native-link bindings in the downstream GTK graph.

A downstream release-mode public-API probe covering forward/reverse iteration,
`nth`, `nth_back` and `last` exited with SIGSEGV (139) before the fix and passes
after it. Festapp keeps this probe under `automation/native-security` and runs
it in CI. Existing warnings in the old GLib source are outside this backport.
