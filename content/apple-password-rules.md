---
title: "Use Apple's Password Rules to help Password Managers"
date: 2021-11-16T17:17:35+01:00
tags: [web]
draft: false
---

I came across a blog post titled "[Why I hate Password Rules](https://www.schneier.com/blog/archives/2021/11/why-i-hate-password-rules.html)"
today. The author brings up a great pain point: password rules are frustrating.

Ironically, the solution is Apple's "[Password Rules](https://developer.apple.com/password-rules/)"!

Apple have created a `passwordrules` attribute for HTML and their own UIKit. It allows you to encode
your password requirements and covers a number of common patterns:

- Required and Allowed keys
- Min and max lengths
- Max consecutive characters

The Password Rules Generator linked above (and [here](https://developer.apple.com/password-rules/)) helps you craft
the somewhat esoteric syntax. It also lets you download password examples for you to build into your tests to prove
that both your front and backends can accept the passwords.

I've created a [CodeSandbox demo](https://codesandbox.io/s/password-rules-demo-029h5) of the feature. A quick test
shows that 1Password honours the min/max lengths. Hopefully the most popular Password Managers also honour it.