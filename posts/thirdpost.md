---
title: Conventional comments in code reviews
description: This is a post on clarifying intentions in code review comments by using conventional comments
date: 2021-08-08
tags:
  - Git
  - Code-reviews
  - Conventional-comments
layout: layouts/post.njk
---

## Conventional Comments

Conventional comments are used by prefixing comments with a label: `praise`, `nitpick`, `suggestion`, `issue`, `question`, `thought`, `chore`. The whole point of these labels are to more clearly convey the intention of the comment, reduce confusion caused by misinterpretations, and to set the tone.

Here is an example from the [Conventional Comments](https://conventionalcomments.org/) site:

A comment like this is not really helpful to the author:

```
This is not worded correctly.
```

Whereas, with a prefixed label, the intention behind the comment is more clear and the author will have a better idea of what action is neccessary, if any:

```
suggestion: This is not worded correctly
```

or:

```
nitpick (non-blocking): This is not worded correctly.
```

Often code reviews are done between tasks, when developer's have found some time outside of their current work, or at the end of the day. Sometimes this block of time isn't much to work with, so we can be culpable to writing comments that are too terse or too open to interpretation. Choosing one of these labels when adding comments can help us as reviewers think more about what we're trying to say e.g is it a nitpick, a question or a suggestion?

I think one of the biggest benefits of using this approach is that when changes are requested to a PR, it's clear to the author what it is that the reviewer thinks needs changing, as the comments can be prefixed with `issue`. The author can then address the issues at hand, or discuss them further with the reviewer. As for the rest of the comments regarding nitpicks and suggestions, the author can decide if they warrant changes. It also means that the reviewer can leave a bunch of nitpicks or suggestions and still approve the PR, allowing the author to make the decision if anything needs changing, without blocking them from merging.

It may seem like using these prefixes can be seen as a waste of time or unnecessary, however, I believe people do see code reviews as conversations, and do take comments personally, even when trying to avoid this in the name of professionalism. Using conventional comments can be useful in taking the ego out of code reviews and lead to a more efficient and streamlined process.
