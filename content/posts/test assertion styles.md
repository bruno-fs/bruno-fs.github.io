---
date: "2023-03-24T16:38-03:00"
tags:
- pytest
- unnittest
title: test assertion styles
---

# pytest v unittest assertion styles {#pytest-v-unittest-assertion-styles}

`pytest` can be used as a test runner in a test suite that was originally designed
for standard library `unittest` (usually) with no code changes. In these scenarios,
though, it might be really interesting replacing the `unittest` style asserts with
"plain" asserts (a.k.a. `pytest` style).

But why, you ask. Well...

> Talk is cheap. Show me the code.

Instead of explaining it, let's see the difference together by comparing similar tests
written with `pytest` style for asserts vs. `unittest` style asserts. Let's use simple
datatypes for that:
- long strings
- dictionaries
- lists
- sets

The following test file will be used for these comparisons.

``` python
# file-name = test_dummy.py
from unittest import TestCase

MESSAGE1 = (
    "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque venenatis nulla"
    " ut arcu gravida, a lacinia dui ullamcorper. Aenean pulvinar placerat consequat."
    " Nullam bibendum mauris a velit vehicula porta. Aenean pretium euismod fringilla."
    " Aenean faucibus orci magna, sed dictum nisl placerat vitae. Cras sit amet mattis"
    " dolor. Nulla molestie ut tellus in luctus. Vivamus sagittis lacus non elementum "
    "sagittis. Donec ac malesuada velit. Aliquam lacinia eget nibh sit amet faucibus."
    " Cras id auctor ligula."
)

MESSAGE2 = (
    "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque venenatis nulla"
    " ut arcu gravida, a lacinia dui ullamcorper. Aenean pulvinar placerat consequat."
    " Nullam bibendum mauris a velit vehicula porta. Aenean pretium euismod fringilla."
    " Aenean faucibus orci magna, sed dictum nisl placerat vitae. Cras sit amet mattis"
    " dolor. Nulla molecule ut tellus in luctus. Vivamus sagittis lacus non elementum "
    "sagittis. Donec ac malesuada velit. Aliquam lacinia eget nibh sit amet faucibus."
    " Cras id auctor ligula."
)

# from https://json.org/example.html
DICT1 = {
    "glossary": {
        "title": "example glossary",
        "GlossDiv": {
            "title": "S",
            "GlossList": {
                "GlossEntry": {
                    "ID": "SGML",
                    "SortAs": "SGML",
                    "GlossTerm": "Standard Generalized Markup Language",
                    "Acronym": "SGML",
                    "Abbrev": "ISO 8879:1986",
                    "GlossDef": {
                        "para": "A meta-markup language, used to create markup languages such as DocBook.",
                        "GlossSeeAlso": ["GML", "XML"],
                    },
                    "GlossSee": "markup",
                }
            },
        },
    }
}
DICT2 = {
    "glossary": {
        "title": "example glossary",
        "GlossDiv": {
            "title": "S",
            "GlossList": {
                "GlossEntry": {
                    "ID": "SGML",
                    "SortAs": "SGML",
                    "GlossTerm": "Standard Generalized Markup Language",
                    "Acronym": "SGML",
                    "Abbrev": "ISO 8889:1986",
                    "GlossDef": {
                        "para": "A meta-markup language, used to create markup languages such as DocBook.",
                        "GlossSeeAlso": ["GML", "XML"],
                    },
                    "GlossSee": "markup",
                }
            },
        },
    }
}


class TestDummy(TestCase):
    def test_simple_dict_unittest_style(self):
        self.assertDictEqual(dict(zip("abc", range(3))), dict(zip("abc", [0, 2, 2])))

    def test_simple_dict_pytest_style(self):
        assert dict(zip("abc", range(3))) == dict(zip("abc", [0, 2, 2]))

    def test_list_unittest_style(self):
        self.assertListEqual(list("ABCD"), list("AFCBE"))

    def test_list_pytest_style(self):
        assert list("ABCD") == list("AFCBE")

    def test_set_unittest_style(self):
        self.assertSetEqual(set("ABCD"), set("AFCBE"))

    def test_set_pytest_style(self):
        assert set("ABCD") == set("AFCBE")

    def test_long_text_unittest_style(self):
        self.assertEqual(MESSAGE1, MESSAGE2)

    def test_long_text_pytest_style(self):
        assert MESSAGE1 == MESSAGE2

    def test_nested_dict_unitttest_style(self):
        self.assertDictEqual(DICT1, DICT2)

    def test_nested_dict_pytest_style(self):
        assert DICT1 == DICT2
```

## long strings {#long-strings}

This is the output for "long_text" tests
![pytest-v-unittest-long-text](../test%20assertion%20styles/23842e581975fb53d9286b152250993cf7a18e71.png)

Whoa, look at that beautiful diff on the report for `TestDummy.test_long_text_pytest_style` ! It's pointing exactly where the difference is, ignoring the rest of the file.
The `unittest` style doesn't help in this aspect. It mentions it can show the whole thing setting `maxDiff` to `None`, but that doesn't help. Just makes it more verbose.

## dict

This is the output for "simple_dict" tests

![pytest-v-unittest-simple-dict](../test%20assertion%20styles/5127241a8bd1e1e540f485aa464e5b8313b84a54.png)

Both seem useful to me in this scenario.

Let's see a test using the nested dict.

![pytest-v-unittest-nested-dict](../test%20assertion%20styles/e5394d71629bcd30f882f9803b1d5cbabbb38fee.png)

Again, no clear winner, right? Hold on a sec, let's try again with the `-vv` for increased verbosity.

![pytest-v-unittest-nested-dict-vv](../test%20assertion%20styles/7900c985ddcf3950fe926ae31cc26aef83744a99.png)

Whoa! Look at that! `pytest` style is the clear winner here. It points where
exactly difference is within the dict. I should mention this only works well for differences in `values`. Differences in `keys` won't be so clear, but `pytest` will try to help.

As for `unittest`, setting `maxDiff` don't help too much. It only makes the output more verbose.

## set and list {#set-and-list}

Those are pretty simillar and I've no strong opinions on which one is better. It's a tie IMO.

![pytest-v-unittest-set](../test%20assertion%20styles/5bc98e50dc063e0d6457748d075bc76935279dc6.png)

![pytest-v-unittest-list](../test%20assertion%20styles/6632d3f97ba613cf02e32118cef9abd9d549ed18.png)
