---
title: findRESubmatch
description: Returns a slice of strings holding the text of the leftmost match of the regular expression and the matches, if any, of its subexpressions
categories: [functions]
menu:
  docs:
    parent: functions
keywords: [regex]
signature:
  - "findRESubmatch PATTERN INPUT [LIMIT]"
  - "strings.FindRESubmatch PATTERN INPUT [LIMIT]"
relatedfuncs: [findRE, replaceRE]
---

By default, `findRESubmatch` finds all matches. You can limit the number of matches with an optional LIMIT parameter. A return value of nil indicates no match.

When specifying the regular expression, use a raw [string literal] (backticks) instead of an interpreted string literal (double quotes) to simplify the syntax. With an interpreted string literal you must escape backslashes.

[string literal]: https://go.dev/ref/spec#String_literals

This function uses the [RE2] regular expression library. See the [RE2 syntax documentation] for details. Note that the RE2 `\C` escape sequence is not supported.

[RE2]: https://github.com/google/re2/
[RE2 syntax documentation]: https://github.com/google/re2/wiki/Syntax/

{{% note %}}
The RE2 syntax is a subset of that accepted by [PCRE], roughly speaking, and with various [caveats].

[caveats]: https://swtch.com/~rsc/regexp/regexp3.html#caveats
[PCRE]: https://www.pcre.org/
{{% /note %}}

## Demonstrative examples

```go-html-template
{{ findRESubmatch `a(x*)b` "-ab-" }} → [["ab" ""]]
{{ findRESubmatch `a(x*)b` "-axxb-" }} → [["axxb" "xx"]]
{{ findRESubmatch `a(x*)b` "-ab-axb-" }} → [["ab" ""] ["axb" "x"]]
{{ findRESubmatch `a(x*)b` "-axxb-ab-" }} → [["axxb" "xx"] ["ab" ""]]
{{ findRESubmatch `a(x*)b` "-axxb-ab-" 1 }} → [["axxb" "xx"]]
```

## Practical example

This markdown:

```text
- [Example](https://example.org)
- [Hugo](https://gohugo.io)
```

Produces this HTML:

```html
<ul>
  <li><a href="https://example.org">Example</a></li>
  <li><a href="https://gohugo.io">Hugo</a></li>
</ul>
```

To match the anchor elements, capturing the link destination and text:

```go-html-template
{{ $regex := `<a\s*href="(.+?)">(.+?)</a>` }}
{{ $matches := findRESubmatch $regex .Content }}
```

Viewed as JSON, the data structure of `$matches` in the code above is:

```json
[
  [
    "<a href=\"https://example.org\"></a>Example</a>",
    "https://example.org",
    "Example"
  ],
  [
    "<a href=\"https://gohugo.io\">Hugo</a>",
    "https://gohugo.io",
    "Hugo"
  ]
]
```

To render the `href` attributes:

```go-html-template
{{ range $matches }}
  {{ index . 1 }}
{{ end }}
```

Result:

```text
https://example.org
https://gohugo.io
```

{{% note %}}
You can write and test your regular expression using [regex101.com](https://regex101.com/). Be sure to select the Go flavor before you begin.
{{% /note %}}
