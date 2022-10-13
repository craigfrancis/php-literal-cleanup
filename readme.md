# Literal Cleanup

A **placeholder** repository for now.

Some tools to help adoption of literal strings.

Literal strings are those defined by the developer (in their source code).

Because they cannot contain user data, they cannot cause an Injection Vulnerability.

While concatenation is allowed (to help adoption), all other forms of modifications are not (it's hard to prove what modifications are safe).

---

## Replace `sprintf()`

The `sprintf()` function is not simple.

It supports a few different format specifiers (more could be added in the future).

The formatting can change the values in unexpected ways, like value coercion (string to int), and how `sprintf('%.3f', 1.23)` will change its output based on the locale (either `.` or `,` for the decimal place).

I intend to provide a way to automatically modify existing code to replace `sprintf()` to use simple string concatenation, e.g.

```diff
- sprintf('%s.id = %s.vendorId', self::VENDOR_ALIAS, $entityAlias),

+ self::VENDOR_ALIAS . '.id = ' . $entityAlias . '.vendorId',
```

Notes from [Juliette](https://github.com/jrfnl):

> Luckily it is largely a solved problem in [PHPCS](https://github.com/squizlabs/PHP_CodeSniffer).
>
> You need to take all sorts of different ways the code can be written into account (single line, multi-line, with comments in the params, named parameter etc).
>
> Use something like the [WPCS](https://github.com/WordPress/WordPress-Coding-Standards) `AbstractFunctionParameterSniff` as a basis. You could have a look at some of the sniffs which extend that sniff to see some example code.
>
> Decide whether the fixer should kick in if there are comments and if so, what to do with those comment.
>
> Think carefully how to fix `sprintf()`s which are:
> - a straight assignment
> - themselves a parameter of another function call
> - already being concatenated
> - etc
>
> What if the replacement param is not a string?
>
> Also find the same in `printf()`, and if so, what should the fix look like?
