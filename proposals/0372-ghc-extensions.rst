The GHC20XX process
===================

.. author:: Alejandro Serrano, Joachim Breitner
.. date-accepted:: 2020-11-16, amended 2025-05-13
.. highlight:: haskell
.. header:: This proposal was `discussed at this pull request <https://github.com/ghc-proposals/ghc-proposals/pull/372>`_ and `amended by this pull request <https://github.com/ghc-proposals/ghc-proposals/pull/632>`_.
.. sectnum::
.. contents::

This proposal sets up a process to introduce new language versions ``GHC20XX``,
extending ``Haskell2010`` as defined by the Report with several extensions that
are considerd harmless, helpful and uncontroversial.

This proposal lists two alternatives in its specification. Listing these together
will help inform debate in advance of a committee decision.

Motivation
----------

It has almost become a meme that any slightly-complex Haskell module starts
with a dozen lines of ``{-# LANGUAGE #-}`` pragmas. This poses several
problems:

- beginners often see features behind an extension as "advanced" or "obscure",
  even though some like ``LambdaCase`` or ``NamedFieldPuns`` are quite simple;
- some sorts of programming in Haskell – mostly type-level oriented – require
  enables lots of extensions (think of the usual combo
  ``MultiParamTypeClasses`` + ``FlexibleInstances`` + ``FlexibleContexts``).
  Sometimes the fact that there are different extensions, instead of a single
  ``ExtendedTypeClasses`` is mostly historical.

For that reason, it's beneficial to pack those extensions which have
"graduated" in terms of stability and usefulness into a single language
version.

We aim to lean on the conservative side in our selection of extensions: Not
including an extension that we should have is relatively harmless (users of
that extension will have to still set that manually, but they already have to
do that, and we can add it later). Conversely, including an extension that we
should not have hurts more (any detriminal effect on developer experience now
affects many users).

We also aim to have a process that is efficient. For each extension, we are
all very capable of having very long discussions about the merits, up-sides
and downsides, and almost anybody will have an opinion on almost any
extensions. But aiming to include only *uncontroversial* extensions, we aim to
avoid much of these discussion by construction. The process deliberately does
not provide room for long per-extensions deliberations, neither within the
committee or with the overall community, while still involving the community.


Proposed Change Specification
-----------------------------

We propose to introduce a process to develop new "language versions", dubbed
``GHC20XX``, which extend ``Haskell2010`` with some default extensions. This
proposal does not set *which* extensions are included in the first version,
but rather seeks to provide a framework to reach a conclusion in a
constructive and timely manner.

This language version can be used as a language extension (with the sole
effect of implying other language extensions), but also as a language versions
in places where ``Haskell98`` or ``Haskell2010`` is valid (e.g. via Cabal’s
``default-language`` field).

When ``ghc`` is used without an explicit language choice, a default choice is
determined by GHC. This applies in particular to uses of ``ghci``. Typically,
the next major release of GHC after an accepted proposal for ``GHC20XX`` will
change the default language edition to ``GHC20XX``.


Criteria
^^^^^^^^

We define a set of criteria that an extension should guide the committee
members in their process of picking suitable extensions. These are not hard
criteria, but often subjective, and not all need to be satisfied in all cases.

1. The extension is (mostly) *conservative*: All programs that were accepted
   without the extension remain accepted, and with the same meaning. We prefer
   to take this as a strict criteria, and grant exceptions on a case-by-case
   basis.
2. *New failure modes* that become possible with the extension are *rare*
   and/or easy to diagnose. These failure modes include new error messages,
   wrong inferred types, and runtime errors, for example. In other words, the
   *developer experience* should not get worse by adding this extension to the
   default mix.
3. The extensions *complement* the design of standard Haskell. For example,
   ``MultiParamTypeClasses`` extends what's already there, and works well with
   the rest of features. If we suddenly decided you can use Lisp-like syntax,
   that would not complement the current design.
4. The extension has been – and can reasonably be predicted to remain –
   *stable*.
5. The extension is one that users might plausibly want to be on all the time.
   This excludes experimental extensions that deliberately enable
   potentially-unsafe or unstable features, such as ``IncoherentInstances`` or
   ``MagicHash``.
6. The extension has *widespread* usage.
7. The extension is favored by the community, with many in favor, and very few
   opposed to its inclusion.

Process
^^^^^^^

* 4 months before the expected GHC spring release day of 20xx, the committee
  Secretary starts the GHC20xx process.

  They inform the committee, in an email to the mailing list, of all language
  extensions supported by the latest released GHC that are not in GHC20(xx-1),
  which could be added. They also list all extensions *in* GHC20(xx-1), which
  might be omitted in GHC202(xx-1) (likely a rare thing).

* In order to gather data on the criterium “widespread usage”, the secretary
  creates a tally of which extensions are used how often on Hackage.

* In order to gather data on the criterium “community support”, the secretary
  runs a public poll on a suitable platform for one week where anyone can vote
  in favor or against the inclusion of a given extension, or points the
  committee to a suitable existing survey result.

* At the start of the process, the secretary creates a PR with a proposal saying (roughly)

    GHC20xx contains the following extensions in addition to those in
    GHC20(xx-1):

    * (none yet)

    and removes these extensions

    * (none yet)

    This PR is a suitable place, besides the poll, for the wider
    community to weigh in. The community is invited to follow the
    committee votes, rationales and discussion on the public email
    archive, and if the committee is missing an important piece of
    information (e.g. more code breaking than expected), to raise
    such a point.

    We hope, however, that the community poll is sufficient to convey
    the level of community support and demand for specific extensions
    have, and want to discourage lengthy, opinion-based discussions of
    the merits of extensions.

    If you miss your favorite extension in the list, please remember
    that you can still use it (by setting the flag explicitly), and
    that it can still go in next round.
* Within two weeks of the start of the process, every committee member is
  expected to send an initial list of which extensions they expect to be in
  GHC20xx to the mailing list.

  Committee members are expected to take the Hackage statistics and the
  community vote into account.

  These mails may contain justifications for why a certain extension is or is
  not included, but this is not required (or even expected).

  After these two weeks, the PR is continuously updated by the secretary to
  reflect the *current* tally of votes: An extension is included if it is
  listed by at least ⅔ (rounded up) of committee members.

* Within four weeks of the start of the process, committee members can change
  their vote (by email to the list).

  It is absolutely ok to change one’s mind based on the explanations in the
  other members’ emails, or the general comments on the PR.

  Long discussions of individual extensions are discouraged at this point. If
  there is controversy around an extension, it is a strong sign that it should
  simply not be included.

* After these four weeks, the proposal with the current tally gets accepted by
  the secretary, and defines GHC20xx

Cadence
^^^^^^^

Likely, the first iteration of this process will be vastly different from the
following ones: The first one is expected to add a large number of
uncontroversial extensions; so the next iteration will likely only make a
smaller, but more controversial change.

Therefore, this proposal does *not* commit to a fixed cadence. Instead, 6
months after the first release of a version of GHC that supports a GHC20xx
set, we evaluate the outcome, the process, and the perceived need of a next
release. At that time we will refine the processes, if needed, and set a
cadence.


Breaking changes
^^^^^^^^^^^^^^^^

Two concerns are in tension:

* For convenient one-off use, and to encourage users to use the most up to date
  language edition, it is desirable that ``ghc`` and ``ghci`` provide the latest
  language edition by default, and do not nag users excessively.

* Silently switching from one language edition to another may involve breaking
  changes. A key point of the language editions mechanism is that these costs
  are incurred when the user decides to switch edition, rather than when the
  compiler is upgraded.

To balance these concerns:

* GHC will identify a "default language edition" that is enabled by default in
  both ``ghc`` and ``ghci``. Normally, the next major release of GHC after an
  accepted proposal for ``GHC20xx`` will both add support for ``GHC20xx`` and
  change the default language edition to ``GHC20xx``. (This may not always be
  the case, for example, GHC 9.10 added support for ``GHC2024``, but the default
  language edition remained ``GHC2021``.)

  * Changes to the default language edition will be accompanied by appropriate
    mentions in the release notes and migration guide.

  * The initial GHCi prompt will be changed to display the active language
    edition.

  * GHC will not automatically emit a warning whenever a language edition has not
    been explicitly specified, because doing so would be overly noisy. However, if
    a language edition has not been explicitly specified, and compilation fails
    with one or more errors, GHC will emit an additional warning recommending that
    a language edition should be chosen, as the error may have resulted from an
    old module not specifying a language edition.

* Users are strongly encouraged to insulate themselves from changes to the
  default language edition by:

  * Using Cabal's ``default-language`` specifier to fix the language edition for a package, or
  * Using a ``LANGUAGE GHC20xx`` pragma in the source files themselves.

Cabal encourages packages to specify a ``default-language``, but does not
require it in all cases, and in its absence may pick its own default (currently
this is ``Haskell98`` or ``Haskell2010``, see `Cabal issue #9668
<https://github.com/haskell/cabal/issues/9668>`_). Thus changes to GHC's default
language edition are primarily of concern to users running ``ghc[i]`` directly,
rather than using Cabal.


For example, the GHCi prompt could look like this:

::

  $ ghci
  GHCi, version 9.14.1: https://www.haskell.org/ghc/  :? for help
  Using default language edition: GHC2024
  ghci>

::

  $ ghci -XGHC2021
  GHCi, version 9.14.1: https://www.haskell.org/ghc/  :? for help
  Using language edition: GHC2021
  ghci>

::

  $ cabal repl
  GHCi, version 9.14.1: https://www.haskell.org/ghc/  :? for help
  Using language edition: Haskell98
  ghci>

For example, the following module will give rise to an error message and a
warning as shown when the default language is ``GHC2024``:

::

  module MonoLocal where

  foo p = (bar True, bar ())
    where
      bar x = if p then x else x

::

  MonoLocal.hs:1:1: warning: [GHC-12345] [-Wmissing-language-edition]
      • No explicit language edition specified, defaulting to GHC2024.
      • Use a {-# LANGUAGE GHC2024 #-} pragma or -XGHC2024 option
        to set the language edition explicitly.
      • If you recently changed compiler version and are seeing new errors,
        you may want to fix an older language edition, as different GHC
        versions may use different defaults.

  MonoLocal.hs:3:24: error: [GHC-83865]
      • Couldn't match expected type ‘Bool’ with actual type ‘()’
      • In the first argument of ‘bar’, namely ‘()’
        In the expression: bar ()
        In the expression: (bar True, bar ())
    |
  3 | foo p = (bar True, bar ())


Costs and Drawbacks
-------------------

The implementation cost seems small.

The cost of a GHC20xx extension is that, upon reading a file with
``{-# LANGUAGE GHC20xx #-}``, the reader does not immediatelly know the set
of enabled extensions; this may hamper readability of code.

The costs of this process is that it binds volunteer time, and there is a
risk of unpleasant, heated debates, because everybody has opinions. The
process tries to err on the conservative side and rather add too few than too
many extensions.

Alternatives
------------

* We could fix a cadence already; one, two or three years have been proposed.

* We could be a tad less aggressive and *not* make it on by default in, say,
  ``ghci``. But it would defeat a bit of the purpose.

Implementation Plan
-------------------

The committee secretary will run the process as outlined here.
