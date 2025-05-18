## Repository for testing Git EOL conversion using .gitattributes

### Checkout defaulting to LF

For any of the following configurations:

| core.autocrlf | core.eol |
|:-------------:|:--------:|
| false         | lf       |
| input         | *any*    |

The result of `git ls-files --eol testfiles` after checkout should be:

```
i/lf    w/crlf  attr/text=auto eol=crlf testfiles/auto.crlf
i/lf    w/lf    attr/text=auto eol=lf   testfiles/auto.lf
i/lf    w/lf    attr/text=auto          testfiles/auto.native
i/-text w/-text attr/text=auto          testfiles/binary.auto
i/-text w/-text attr/text=auto eol=crlf testfiles/binary.auto.crlf
i/-text w/-text attr/text=auto eol=lf   testfiles/binary.auto.lf
i/crlf  w/crlf  attr/-text              testfiles/binary.explicit.crlf
i/lf    w/lf    attr/-text              testfiles/binary.explicit.lf
i/lf    w/crlf  attr/text eol=crlf      testfiles/text.crlf
i/lf    w/lf    attr/text eol=lf        testfiles/text.lf
i/lf    w/lf    attr/text               testfiles/text.native
```
(i = index, w = working directory)

### Checkout defaulting to CRLF

For any of the following configurations:

| core.autocrlf | core.eol |
|:-------------:|:--------:|
| false         | crlf     |
| true          | *any*    |

The result of `git ls-files --eol testfiles` after checkout should be:

```
i/lf    w/crlf  attr/text=auto eol=crlf testfiles/auto.crlf
i/lf    w/lf    attr/text=auto eol=lf   testfiles/auto.lf
i/lf    w/crlf  attr/text=auto          testfiles/auto.native
i/-text w/-text attr/text=auto          testfiles/binary.auto
i/-text w/-text attr/text=auto eol=crlf testfiles/binary.auto.crlf
i/-text w/-text attr/text=auto eol=lf   testfiles/binary.auto.lf
i/crlf  w/crlf  attr/-text              testfiles/binary.explicit.crlf
i/lf    w/lf    attr/-text              testfiles/binary.explicit.lf
i/lf    w/crlf  attr/text eol=crlf      testfiles/text.crlf
i/lf    w/lf    attr/text eol=lf        testfiles/text.lf
i/lf    w/crlf  attr/text               testfiles/text.native
```
(i = index, w = working directory)

### Check-in

Any modified file in the working directory that is recognized as a
text file (e.g. `auto.*` and `text.*` in the above) that contains CRLF line endings
will be converted to LF in the index upon check-in (i.e. `git add`).
This can be verified using `git ls-files --eol` as in the above.

### Additional notes

  - *core.autocrlf = false* is equivalent to *core.autocrlf* being unset, i.e. the default
  - *core.autocrlf = true* overrides *core.eol*, as if *core.eol = crlf* was set
  - Both *core.autocrlf = true* and *core.autocrlf = input* have the
    effect of setting a default *text=auto* attribute on all files
    (which may be overridden on a file-by-file basis by attribute specifications
    in *.gitattributes* etc.)
  - *core.eol* is only relevant for checkout, never for check-in.
    Conversion from CRLF to LF always takes place for text files on
    check-in, regardless of *core.eol* or *core.autocrlf*.
  - *core.eol = crlf* is equivalent to *core.eol = native* on Windows
  - *core.eol = lf* is equivalent to *core.eol = native* on non-Windows systems (including Cygwin)
  - *core.eol = native* is equivalent to *core.eol* being unset, i.e. the default
  - In *.gitattributes*, the *eol* attribute defaults to the effective
    value of *core.eol*, i.e. possibly overridden by *core.autocrlf = true*
    (only relevant in context of a *text* attribute being set for a file)
  - The effective *eol* attribute determines whether conversion from
    LF to CRLF takes place for a text file upon checkout.
    (The reverse conversion, i.e. from CRLF to LF, never takes place
    on checkout.)


