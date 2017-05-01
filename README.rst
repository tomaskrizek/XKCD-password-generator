xkcdpass
========

.. image:: https://badges.gitter.im/Join%20Chat.svg
   :alt: Join the chat at https://gitter.im/redacted/XKCD-password-generator
   :target: https://gitter.im/redacted/XKCD-password-generator?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge

A flexible and scriptable password generator which generates strong passphrases, inspired by `XKCD 936 <http://xkcd.com/936/>`_::

    $ xkcdpass
    > correct horse battery staple

.. image:: http://imgs.xkcd.com/comics/password_strength.png



Install
=======

``xkcdpass`` can be easily installed using pip::

    pip install xkcdpass

or manually::

    python setup.py install



Source
~~~~~~
The latest development version can be found on github: https://github.com/redacted/XKCD-password-generator

Contributions welcome and gratefully appreciated!



Requirements
============

Python 2 (version 2.7 or later), or Python 3 (version 3.2 or later).



Running ``xkcdpass``
====================

``xkcdpass`` can be called with no arguments::

    $ xkcdpass
    > pinball previous deprive militancy bereaved numeric

which returns a single password, using the default dictionary and default settings. Or you can mix whatever arguments you want::

    $ xkcdpass --count=5 --acrostic='chaos' --delimiter='|' --min=5 --max=6 --valid-chars='[a-z]'
    > collar|highly|asset|ovoid|sultan
    > caper|hangup|addle|oboist|scroll
    > couple|honcho|abbot|obtain|simple
    > cutler|hotly|aortae|outset|stool
    > cradle|helot|axial|ordure|shale

which returns

* ``--count=5``   5 passwords to choose from
* ``--acrostic='chaos'``   the first letters of which spell 'chaos'
* ``--delimiter='|'``   joined using '|'
* ``--min=5 --max=6``  with words between 5 and 6 characters long
* ``--valid-chars='[a-z]'``   using only lower-case letters (via regex).


A concise overview of the available ``xkcdpass`` options can be accessed via::

    xkcdpass --help

    Usage: xkcdpass [options]

    Options:
        -h, --help
                                    show this help message and exit
        -w WORDFILE, --wordfile=WORDFILE
                                    Specify that the file WORDFILE contains the list of
                                    valid words from which to generate passphrases.
                                    Provided wordfiles: eff-long (default), eff-short,
                                    eff-special, legacy
        --min=MIN_LENGTH
                                    Minimum length of words to make password
        --max=MAX_LENGTH
                                    Maximum length of words to make password
        -n NUMWORDS, --numwords=NUMWORDS
                                    Number of words to make password
        -i, --interactive
                                    Interactively select a password
        -v VALID_CHARS, --valid-chars=VALID_CHARS
                                    Valid chars, using regexp style (e.g. '[a-z]')
        -V, --verbose
                                    Report various metrics for given options, including word list entropy
        -a ACROSTIC, --acrostic=ACROSTIC
                                    Acrostic to constrain word choices
        -c COUNT, --count=COUNT
                                    number of passwords to generate
        -d DELIM, --delimiter=DELIM
                                    separator character between words


A large wordlist is provided for convenience, but the generator can be used with any word file of the correct format: a file containing one 'word' per line. The default word file can be found in ``xkcdpass/static/default.txt``.

The default word list is derived mechanically from `12Dicts <http://wordlist.aspell.net/12dicts/>`_ by Alan Beale. It is the understanding of the author of ``xkcdpass`` that purely mechanical transformation does not imbue copyright in the resulting work. The documentation for the 12Dicts project at
http://wordlist.aspell.net/12dicts/ contains the following dedication:

..

    The 12dicts lists were compiled by Alan Beale. I explicitly release them to the public domain, but request acknowledgment of their use.


The EFF has specifically designed a word list for passphrase generation, which can be downloaded from their `website <https://www.eff.org/deeplinks/2016/07/new-wordlists-random-passphrases>`_. As it was originally intended for use with Diceware ensure that the number of words in your passphrase is at least six when using it.

Using xkcdpass as an imported module
====================================

The built-in functionality of ``xkcdpass`` can be extended by importing the module into python scripts. An example of this usage is provided in `example_import.py <https://github.com/redacted/XKCD-password-generator/blob/master/examples/example_import.py>`_, which randomly capitalises the letters in a generated password. `example_json.py` demonstrates integration of xkcdpass into a Django project, generating password suggestions as JSON to be consumed by a Javascript front-end.

A simple use of import::

    from xkcdpass import xkcd_password as xp

    # create a wordlist from the default wordfile
    # use words between 5 and 8 letters long
    wordfile = xp.locate_wordfile()
    mywords = xp.generate_wordlist(wordfile=wordfile, min_length=5, max_length=8)

    # create a password with the acrostic "face"
    print(xp.generate_xkcdpassword(mywords, acrostic="face"))

When used as an imported module, `generate_wordlist()` takes the following args (defaults shown)::

    wordfile=None,
    min_length=5,
    max_length=9,
    valid_chars='.'

While `generate_xkcdpassword()` takes::

    wordlist,
    numwords=6,
    interactive=False,
    acrostic=False,
    delimiter=" "


Insecure random number generators
=================================
`xkcdpass` uses crytographically strong random number generators where possible (provided by `random.SystemRandom()` on most modern operating systems). From version 1.7.0 falling back to an insecure RNG must be explicitly enabled, either by using a new command line variable before running the script::

    xkcdpass --allow-weak-rng

or setting the appropriate environment variable::

    export XKCDPASS_ALLOW_WEAKRNG=1


Filtered word list
===================
While we recommend the standard word list for most purposes, we note that this list is uncensored and, as such, generated passwords could offend. For this reason, `a filtered word list can be found in the github repo <https://github.com/redacted/XKCD-password-generator/tree/master/contrib/office-safe.txt>`_ (filtered by Twig Nyugen and included here with permission).

An important caveat: due to the significant reduction in the size of the filtered word list when compared to the default, the strength of the corresponding passwords is also reduced. Users should expect approximately an *order of magnitude* reduction in the strength of a five word passphrase. This can be mitigated by increasing the length of generated passphrases.


Changelog
=========
- **1.9.5** Fix broken test
- **1.9.4** Improve office-safe wordlist contents
- **1.9.3** Link EFF wordlist information, fix typos, update copyright
- **1.9.2** Added Debian cracklib path
- **1.9.1** Fixed typo in example
- **1.9.0** Improvements to interactive mode
- **1.8.2** `generate_wordlist` behaviour didn't match docstring, fixed
- **1.8.1** Fix typo in validation function
- **1.8.0** Fix error in wordfile argument handling
- **1.7.0** require explicit permission to fall back to insecure PRNG
- **1.6.4** fix broken link in README


License
=======
This is free software: you may copy, modify, and/or distribute this work under the terms of the BSD 3-Clause license.
See the file ``LICENSE.BSD`` for details.


Wordfiles
---------

- "[eff-long](https://www.eff.org/files/2016/07/18/eff_large_wordlist.txt)"
  by [EFF](https://www.eff.org) is licensed under
  [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/us/)

- "[eff-short](https://www.eff.org/files/2016/09/08/eff_short_wordlist_1.txt)"
  by [EFF](https://www.eff.org) is licensed under
  [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/us/)

- "[eff-special](https://www.eff.org/files/2016/09/08/eff_short_wordlist_2_0.txt)"
  by [EFF](https://www.eff.org) is licensed under
  [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/us/)
