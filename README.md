# Provable Refactorings

A collection of refactoring recipes that are provably safe. Each recipe is language-specific and rests on the rules of that language. Within that language, each will either terminate with a clear failure or will complete in a way that guarantees no change in behavior.

Each recipe includes its methods of proof within each step of the recipe. The most common proof technique is static analysis (often performed by a compiler). No tests are assumed to exist on the code under refactoring.

In fact, we do not rely on tests in any way, even if they are present. This is because we are holding ourselves to a higher standard of correctness than testing can validate.

## Standard of Correctness: Bug-for-Bug Compatibility

Each recipe is guaranteed to be bug-for-bug compatible. This means that the code before and after the transformation is guaranteed to have exactly the same behavior, including unintended behavior. More plainly:

> Never accidentally introduce nor fix a bug, including one that you don't know exists. Maintain all behavior, including unknown or unspecified behavior.

This property allows these refactorings to be used in a wide variety of contexts in which other refactorings are insufficiently safe. That shifts the economics of software design, and allows design improvement of otherwise intractible code bases.

## Levels of Completion

Some of these recipes are very complete; others are works in progress. The recipes can have bugs: situations in which they don't meet the bug-for-bug compatibility guarantee. To help understand the risk associated with a given refactoring, all recipes are clearly marked as to their stage of development. New recipes always start at the beginning, advancing as evidence accumulates.

1. **Need:** We have identified the need for a recipe, but have not yet created one. We are in the process of gathering a clear and agreed description of the transformation's intention.
2. **Concepts:** We have identified one or more concepts. Each concept is a sequence of steps, each of which appears provable. We are in the process of verifying steps, finding constraints, and selecting between implementations that are valid.
3. **Draft:** We have an implementation that has worked in at least one context. It has identified and incorporated constraints and step-wise proof. The writing is intelligible for experienced legacy code menders. We are in the process of executing this in a variety of contexts and performing theoretical analysis to gain evidence of correctness.
4. **Staging:** We have an implementation that has worked in multiple contexts and has a theoretical basis. The writing is intelligible for general audience consumption. We are in the process of trying this out widespread, and are refining it. The main refinements are often in terms of performance (decreasing time and mental effort required to execute it) and clarity (decreasing mental effort required to learn it correctly and the number of errors made by those who learn it without mentorship).
5. **Production:** It's solid. It is in widespread use.

## Who Should Use What Levels

Recipes at lower levels of completion are a lot harder and more expensive to use. And they have bugs. For most people, using refactorings at the _Draft_ level and below simply won't make economic sense. Without a lot of practice, you will probably spend more time using the recipe and addressing its faults than you would by simply coding and fixing.

Here's where we believe the economics make sense, by audience:

* **General Public:** _Staging_ and _Production_. These recipes will work. The staging ones might require a couple of extra steps, but all have sufficiently low risk that the time saved by not chasing bugs more than pays for the time spent following the recipe.
* **Code by Refactoring Students:** _Draft_ and above. You are starting to develop deep mastery in legacy code mending. You are likely to see the defects in a draft recipe and understand it even in the presence of abrupt, abstract, or unclear language. And we could really use your help getting these drafts into staging.
* **Refactoring Authors:** All levels. You implement new features by refactoring the code safely until the feature is a one-liner. And then you are (correctly) more skeptical about that one-line change that changes behavior than about all those refactorings. You can visualize sequences of micro-transformations to make up a refactoring, and you know dozens of ways to establish that a step is safe. We really need your help getting things up to draft status!

## Contributing

Please send us PRs and bug reports!

In your PR, please include an update to add yourself to CONTRIBUTORS.txt if you aren't already there.

All contributions are licensed under the CC-BY-3.0. It is your responsibility to ensure you have the right to license your contribution under this license before sending us a PR.
