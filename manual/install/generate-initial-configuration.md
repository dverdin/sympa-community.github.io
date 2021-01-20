---
title: 'Generate initial configuration'
prev: install-dependent-modules.md
up: ../install.md
next: setup-database.md
---

Generate initial configuration
==============================

General instruction
-------------------

Edit [``sympa.conf``](../layout.md#config) so that it will contain at least
two lines such as:
```
domain mail.example.org
listmaster your@e-mail.addr.ess,other@email.addr.ess
```

  * [``domain``](/gpldoc/man/sympa_config.5.html#domain)

    The "primary" mail domain name of the mailing list service.
    If your Sympa provides multiple domains and at anytime domain is not
    specified, this domain will be used.
    If your Sympa provides service for single domain, it is primary domain.

  * [``listmaster``](/gpldoc/man/sympa_config.5.html#listmaster)

    Real address(es) of listmasters.  If your Sympa provides multiple domains,
    it may have listmasters by each domain.  However, listmaster(s) defined in
    [``sympa.conf``](/gpldoc/man/sympa_config.5.html#config) have privileges to manage
    all domains, namely "super-listmaster".

    Multiple addresses have to be separated by comma
    (``,``).  Addresses of real people should be chosen: Especially, messages
    sent to those addresses must not be forwarded to any addresses managed by
    Sympa.

----
Note:

  * On Sympa prior to 6.2.34,
    [``wwsympa_url``](/gpldoc/man/sympa_config.5.html#wwsympa_url) parameter was also
    required.  If it is the case, you may specify appropriate value at this
    time.

----

Additionally, following parameter may be useful:

  * [``lang``](/gpldoc/man/sympa_config.5.html#lang)

    Default language used
    in user interface of Sympa (Note that this setting never affect languages
    of messages sent by users).  It should be one of possible values listed
    in [``supported_lang``](/gpldoc/man/sympa_config.5.html#supported_lang)
    parameter.  For example with Polish:
    ```
    lang pl
    ```
    The default value is ``en-US`` (American English).

To know about all available parameters in sympa.conf,
see [sympa.conf(5)](/gpldoc/man/sympa_config.5.html).

