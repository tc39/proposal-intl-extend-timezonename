# Extend TimeZoneName Option Proposal

<!--
 
  1.  Add a post-rewrite git hook to auto-rebuild the output on every commit:
      ```sh
      cp hooks/post-rewrite .git/hooks/post-rewrite
      chmod +x .git/hooks/post-rewrite
      ```
-->
## Stage 
Stage 0

## Champion
* Frank Tang @FrankYFTang

## Scope
  Extend the timeZoneName option in Intl.DateTimeFormat object to support more formatted options.
  
## Background
[DateTimeFormat: consider adding more timezone display options](https://github.com/tc39/ecma402/issues/119)
 
## 

Straw Proposal:
| timeZoneName | CLDR pattern | Examples | Note |
| --- | --- |--- | --- |
| short | z | -0800  | Already in ECMA402 |
| long | zzzz | -0800  | Already in ECMA402 |
| offset | Z | -0800  |   |
| longOffset  | ZZZZ | GMT-0800 |  |
| shortWall  | v | PT |  |
| longWall | vvvv | Pacific Time |  |
| location | VVV |	Los Angeles |  |
| longLocation | VVVV | Los Angeles TIme |  |
| locode | V | uslax |  |
| id | VV	| America/Los_Angeles |  |


 
## To be deleted ...
  3.  ["How to write a good explainer"][explainer] explains how to make a good first impression.

      > Each TC39 proposal should have a `README.md` file which explains the purpose
      > of the proposal and its shape at a high level.
      >
      > ...
      >
      > The rest of this page can be used as a template ...

      Your explainer can point readers to the `index.html` generated from `spec.emu`
      via markdown like

      ```markdown
      You can browse the [ecmarkup output](https://ACCOUNT.github.io/PROJECT/)
      or browse the [source](https://github.com/ACCOUNT/PROJECT/blob/HEAD/spec.emu).
      ```

      where *ACCOUNT* and *PROJECT* are the first two path elements in your project's Github URL.
      For example, for github.com/**tc39**/**template-for-proposals**, *ACCOUNT* is "tc39"
      and *PROJECT* is "template-for-proposals".


## Maintain your proposal repo

  1. Make your changes to `spec.emu` (ecmarkup uses HTML syntax, but is not HTML, so I strongly suggest not naming it ".html")
  1. Any commit that makes meaningful changes to the spec, should run `npm run build` and commit the resulting output.
  1. Whenever you update `ecmarkup`, run `npm run build` and commit any changes that come from that dependency.

  [explainer]: https://github.com/tc39/how-we-work/blob/HEAD/explainer.md
