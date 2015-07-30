# 0.9.2

* Added `Reusability.caseClass[A]` macro and deprecated `Reusability.caseclassN(A.unapply)`.
  Also comes with `caseClassDebug[A]` if you want to see the code it generates.
* In the Router2 route-building DSL, added `caseClass[A]` and deprecated `caseclassN(A)(A.unapply)`.
  Also comes with `caseClassDebug[A]` if you want to see the code it generates.
* Test simulation data objects `{Change,Keyboard,Mouse}EventData` no longer wrap
  args in `UndefOr` with `undefined` as the default. This crashes PhantomJS.
  All args have default values now.
* Add `onError` attribute for use with `<img>`.
* Add `ReactComponent.initialStateC` which provides access to the component (albeit without the `Backend` type set).
  Useful for self-managing state, a minimalistic example of which you can read in
  [SelfManagedStateTest.scala](../test/src/test/scala/japgolly/scalajs/react/SelfManagedStateTest.scala).

```
// Reusability.caseClass
find . -name '*.scala' -exec perl -pi -e 's/Reusability.caseclass\d+\s*\(\s*(\S+)\s*\.\s*unapply\s*\)/Reusability.caseClass[$1]/' {} +

// DSL route.caseClass
find . -name '*.scala' -exec perl -pi -e 's/\.caseclass\d+\s*\(\s*(\S+)\s*\)\s*\([^)]+\.\s*unapply\s*\)/.caseClass[$1]/' {} +
```


# 0.9.1 ([commit log](https://github.com/japgolly/scalajs-react/compare/v0.9.0...v0.9.1))

* Fixed `onDblClick` so that it uses `onDoubleClick` as React expects, and
  added a `onDoubleClick` alias.
* Make Scalatags' `AttrValue` and `StyleValue` public. Package is `japgolly.scalajs.react.vdom`.
* Add:
  * `uuid` to the Router2 route building DSL.
  * `ReactEventTA` aliases for TextArea events.
  * nice `.toString` methods to `Px` classes.
  * `ReusableFn.renderComponent` which recreates a `Props ~=> ReactElement`.
  * `ReactComponentB.configureSpec` - useful for JS interop.
  * [`ReusableVal2`](../extra/PERF.md#reusableval2): A lazy value whose reusability is determined by another value.
* Upgrade:
  * [scala-js-dom](https://github.com/scala-js/scala-js-dom) to 0.8.1.
  * Scalaz to 7.1.3.


# 0.9.0 ([commit log](https://github.com/japgolly/scalajs-react/compare/v0.8.4...v0.9.0))

##### Breaking
* On `ReactComponentB`, you now specify `domType` *before* lifecycle callbacks.
  This means that `getDOMNode()` will have the right type in lifecycle callback bodies.
* Relaxed `TopNode`, (the greatest-upper-bound on component DOM node types), from `HTMLElement` to `Element` as React components can render SVG too.
* `TagMod` composition is now associative: constituents are applied in the order they're composed in.
* Renamed `ComponentStateFocus` to `CompStateFocus`.
* Changed `CompStateAccess` shape and internals for improved type-inference and usage [[35ec0b22](https://github.com/japgolly/scalajs-react/commit/35ec0b22dc8790d4966895d84d2ac7accb457a6b)].
  <br>If you don't use this directly, you won't be affected.

##### Changes
* Upgrade Scalaz from 7.1.1 to 7.1.2.
* Tiny performance improvement composing `EmptyTag`.

##### New
* Added `EventListener`. ([live demo](https://japgolly.github.io/scalajs-react/#examples/event-listener))
* `ReactComponentB` now has `.mixinJS` method; components can now mixin pure-JS React mixins.
  <br>*Note: This doesn't mean JS mixins will work as expected, however.
  Some won't work correctly as they make assumptions that don't hold for Scala.
  If a mixin expects to inspect your props or state, forget about it; all your data is belong to Scala.*
* `ReactComponentB` now has a `.reRender` method for Scala mixins to customise a component's output.
* More Scalaz `IO` support:
  * `ReactComponentB` now supports `IO`-aware lifecycle callbacks.
    <br>Example: `.componentWillUpdateIO` instead of `.componentWillUpdate`.
  * `SetInterval` learned `setIntervalIO`.

## Performance Management
A number of new tools and utilities have been introduced for you to manage the performance of your React app.

Detail with examples are here: [extra/PERF.md](../extra/PERF.md).

* Scala facade for `React.addons.Perf`. See https://facebook.github.io/react/docs/perf.html.
* `Reusability` for fast, easy & safe `shouldComponentUpdate` management.
* `ReusableFn` for stable callbacks that don't sabatage `shouldComponentUpdate` or go stale.
* `ReusableVal` for specifying a value and its explicit reusability (in special/individual cases).
* `ReusableVar` for safe R/W access to a variable, that works with `shouldComponentUpdate`.
  <br>(Basically, `ExternalVar` + `Reusability`.)
* `Px` for fast & effecient caching of graphs (data with dependencies).

## Router v2

This release comes with a new and improved router.

The design of [the v1 Router](../extra/ROUTER.md)
made certain features very hard to accommodate:
[#96](https://github.com/japgolly/scalajs-react/issues/96),
[#103](https://github.com/japgolly/scalajs-react/issues/103),
[#107](https://github.com/japgolly/scalajs-react/issues/107),
[#100](https://github.com/japgolly/scalajs-react/issues/100),
[#94](https://github.com/japgolly/scalajs-react/issues/94),
[#69](https://github.com/japgolly/scalajs-react/issues/69).

In contrast, [the v2 Router](../extra/ROUTER2.md) has a different design that:

* Uses a user-provided data representation of your pages to identify routes and their parameters.
* Similarly the Router can now indicate the current page with precision, faciliating dynamic menus and breadcrumbs even in the presence of complex, dynamic routes.
* Routes can be stateful and conditional.
* Routes can be nested and modularised.
* Routes can be manipulated in bulk.
* Has a better API such that usage previously recommended against, is now impossible. Noteworthy is that `Router` is now just a `ReactComponent`, and `RouterCtl` is the client API.

Detail with examples are here: [extra/ROUTER2.md](../extra/ROUTER2.md).
