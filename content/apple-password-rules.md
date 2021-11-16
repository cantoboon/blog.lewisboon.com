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

```html
<input
    type="password"
    passwordrules="minlength: 20; required: lower; required: upper; required: digit; required: [-]; allowed: ascii-printable;"
/>
```

This Password Rule says a password:

- Has to have a min length of 20
- Has to have a lower, upper, digit, and the `-` (hyphen) character
- Can optionally include ascii-printable characters
    - By default, if you do not include `allowed`, you will be limited to only the types listed by `required`

I've created a [CodeSandbox demo](https://codesandbox.io/s/password-rules-demo-029h5) of the above. A quick test
shows that 1Password honours the min/max lengths. Hopefully the most popular Password Managers also honour it.

The [Password Rules Generator](https://developer.apple.com/password-rules/) helps you craft
the somewhat esoteric syntax. It also lets you download password examples for you to build into your tests to prove
that both your front and backends can accept the passwords.
