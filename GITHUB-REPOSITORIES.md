# GitHub Repositories

Repos are checked top-to-bottom. Set enabled to false to keep a repo in the list without indexing it.
Set pages to true to use the fast GitHub Pages read-path instead of recursive Octokit calls.
Set STREAM to the workspace that should receive the repository's `files.json` tree.
Set fallback to `github-tree` when a repo does not publish `files.json`; this uses GitHub's recursive tree API instead.

| name                          | stream     | repo                                  | branch | root | enabled | priority | pages | empty | LFS   | fallback   |
| ----------------------------- | ---------- | ------------------------------------- | ------ | ---- | ------- | -------- | ----- | ----- | ----- | ---------- |
| NCERT-SCIENCE                 | SCIENCE    | fsr-science/NCERT-Science             | main   |      | true    | 1        | true  | false | false |            |
| NCERT-COMMERCE                | COMMERCE   | fsr-commerce/NCERT-Commerce           | main   |      | true    | 2        | true  | true  | false |            |
| NCERT-HUMANITIES              | HUMANITIES | fsr-humanities/NCERT-Humanities       | main   |      | true    | 3        | true  | false | false |            |
| NoteBooks-Community           | COMMUNITY  | fsr-official/NoteBooks-Community      | main   |      | true    | 4        | false | false | false |            |
| NoteBooks-Issues              | ISSUES     | fsr-official/NoteBooks-Issues         | main   |      | true    | 5        | false | false | false |            |
| NoteBooks-Documents           | DEVELOPERS | fsr-official/NoteBooks-Documents      | main   |      | true    | 6        | false | false | false | github-tree |
| CENGAGE-Physics               | SCIENCE    | fsr-science/CENGAGE-Physics           | main   |      | true    | 7        | false | false | false |            |
| CENGAGE-Maths                 | SCIENCE    | fsr-science/CENGAGE-Maths             | main   |      | true    | 8        | false | false | false |            |
| CENGAGE-Chemistry             | SCIENCE    | fsr-science/CENGAGE-Chemistry         | main   |      | true    | 9        | false | false | true  |            |
| COMPUER_SCIENCE-PREETI-AURORA | SCIENCE    | fsr-science/CS-PREETI-AURORA          | main   |      | true    | 10       | false | false | false |            |
| MATHEMATICS-RD-SHARMA          | SCIENCE    | fsr-science/MATHS-RD-SHARMA           | main   |      | true    | 11       | false | false | false |            |
| PHYSICS-HC-VERMA-I            | SCIENCE    | fsr-science/PHYSICS-HC-VERMA-I        | main   |      | true    | 12       | false | false | false |            |
| PHYSICS-HC-VERMA-II           | SCIENCE    | fsr-science/PHYSICS-HC-VERMA-II       | main   |      | true    | 13       | false | false | false |            |
