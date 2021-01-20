---
title: 'Archives'
up: ../customize.md#customizing-sympa-services
---

Archives
========

Sympa maintains text archive and web archive for mailing lists.
The web archives are fully integrated in the Sympa web interface, though the
engine that does the HTML tranformation is an third-party tool.

Features
--------

### Access Control

This is probably the most interesting part of the Sympa web archives : you can
restrict access to a list web archives to a certain population (members of the
current list, members of another list, clients in particular IP address ranges,
users defined in an SQL/LDAP base). This fine tuning is defined via the
[`archive.web_access`](/gpldoc/man/sympa_config.5.html#archive) list parameter. This
parameter refers to the corresponding
[authorization scenario](../costomize/basics-scenarios.md).

Access via mail command (`INDEX` and `GET`) may also be controlled with
[`archive.mail_access`](/gpldoc/man/sympa_config.5.html#archive) list parameter.

### Search engine

Sympa provides a search engine to search archive of each list. User can either
use the standard search field or the advanced search page.

### Messages removal

Sympa provides a "message removal" feature, accessible through a button from
individual message web pages. This feature is restricted to either message
authors (once authenticated) or list owners. Removal requests are then
processed by the [`archivd.pl`](/gpldoc/man/archived.8.html) process; therefore it
may take a few seconds before it is removed.

### Resending message

From individual message web pages, you can ask that the message is sent back
to you, via SMTP.

### Export of web archives

List owners have access to an archive management page from the web interface. From this page, they can download a ZIP of their list web archives. The ZIP is organized by month, each month containing original emails.

MHonArc tool
------------

Sympa does not perform the MIME to HTML transformation by itself, neither does
it build the messages index and threads. All this is performed by the
[MHonArc](https://www.mhonarc.org) program. MhonArc is a software used beside
many other mailing list softwares. The may Sympa uses MHonArc concentrates on
integration.

You can customize the behavior of `mhonarc` via the
[`mhonarc-ressources.tt2`](/gpldoc/man/mhonarc-ressources.tt2.5.html) resource file.
The HTML view of archives is being delivered by the `wwsympa.fcgi` process so
the MHonArc output uses Sympa TT2 template format. When modifying
`mhonarc-ressources.tt2` you may change the way MHonArc prepare archives or
you may change the way wwsympa.fcgi show them.

For example, you may want to customize the archiving in order to reduce disk
usage.

To change MHonArc behavior, we need to override its settings, configured in
``default/mhonarc-ressources.tt2``.

First, we need to copy that file to override it to keep our changes through
Sympa upgrades.

```
cp default/mhonarc-ressources.tt2 etc/
```

Then edit ``etc/mhonarc-ressources.tt2`` to add
[``MIMEIncs``](https://www.mhonarc.org/MHonArc/doc/resources/mimeincs.html)
and/or
[``MIMEExcs``](https://www.mhonarc.org/MHonArc/doc/resources/mimeexcs.html)
sections.

### ``MIMEIncs``

MHonArc archives all parts it can find in the mail. ``MIMEIncs`` allows to
specify the [media types](https://en.wikipedia.org/wiki/Media_type) of the
parts that you want to archive, excluding anything else.

For example, to only archive text parts of a mail, add this to
``etc/mhonarc-ressources.tt2``:

```
<MIMEIncs>
text/plain
text/html
</MIMEIncs>
```

With this snippet, images (for example) attached to a mail will not be
archived, but the plain text and HTML version of the body of the mail will be
archived (along with any attachment matching those media types)

You can use a more generic media type:

```
<MIMEIncs>
text
</MIMEIncs>
```

---
Note:

  * MHonArc will archive parts of the mail matching a media type defined
    in ``MIMEIncs`` *and* not matching a media type defined in ``MIMEExcs``.
    That allows you to have a generic media type in ``MIMEIncs`` and block
    archiving some more specific media types.

### ``MIMEExcs``

``MIMEExcs`` prevents MHonArc to archive mail parts matching one of the defined
media type, even if it matches a media type defined in ``MIMEIncs``.

Example:
```
<MIMEExcs>
image/jpg
image/gif
text/xml
</MIMEExcs>
```

See [MHonArc manual](https://www.mhonarc.org/MHonArc/doc/mhonarc.html) for
more informations on customization.

Archives structure
------------------

The web archives are gathered in a common directory
[`$ARCDIR`](../layout.md#arcdir) (location may be customized by the
[`arc_path`](/gpldoc/man/sympa_config.5.html#arc_path) sympa.conf parameter).
This directory contains a subdirecty for each archived mailing list.
The list archive directory is structured a subdirectory for each month.
Eath month directory contains both the HTML pages for messages and indexes
and a subdirectory containing the orginal messsages in a text format.

Here is a sample list archive hierarchy :

``` code
$ARCDIR/your-list@mail.example.org/:
  1997-07
  ...
  2018-11
    arctxt
      1
      2
      ...
    mail1.html
    msg00000.html
    msg00001.html
    msg00002.html
    ...
    thrd1.html
```

Configuration parameters
------------------------

### `sympa.conf` / `robot.conf` parameters

  * `arc_path`
  * `archive_default_index`
  * `custom_archiver`
  * `default_archive_quota`
  * `ignore_x_no_archive_header_feature`
  * `mhonarc`
  * `process_archive`
  * `web_archive_spam_protection`

### list parameters

  * `archive`
  * `archive_crypted_msg`
  * `process_archive`
  * `web_archive_spam_protection`

See also "[Archives](/gpldoc/man/sympa_config.5.html#archives)" section of manpage.

`archived.pl` daemon
--------------------

A dedicated daemon, [`archived.pl`](/gpldoc/man/archived.8.html), is performing all
operations on list archives (adding a message to archive, removing a message,
rebuilding HTML files). This process is running as the `sympa` user; messages
to archive and removal/rebuild commands are fetched from the archive spool
located at [``$SPOOLDIR``](../layout.md#spooldir)``/outgoing`` directory
(location may be customized by
[queueoutgoing](/gpldoc/man/sympa_config.5.html#queueoutgoing) site parameter).

When it archives the first message ever for a given list, `archived.pl` will
try to create its archive directory. If it can't, it copies the message into a
`bad/` subdirectory of the archive spool.

Rebuilding web archive
----------------------

Sympa provides a way to rebuild all HTML files for a given web archive. This is useful whenever you changed the `mhonarc-resources.tt2` file or if Sympa changed its template format or the charset used for web pages.

The rebuild feature is accessible to listmasters only from the `Sympa admin` - `Archives` chapter from the web interface (See also
"[Listmaster portal](../admin/web-interface.md#listmaster-portal)").
The rebuild is then run by the `archived.pl` process.

Importing archives of earlier version
-------------------------------------

Since version 5.2b, Sympa maintains a single archive of mailing lists, but it previously maintained both a mail archive (stored in the `list_data/mylist/archives/` directories) and a web archive (for historical reasons). You may need to import existing mail archives in a list mail archive, using the provided `arc2webarc.pl` script.
See ~~[``arc2webarc(1)``](/gpldoc/man/arc2webarc.pl.1.html)~~.

<!--
If you are moving from another mailing list software to Sympa, you are also facing messages archive import problems. Check the [Contrib section](http://www.sympa.org/wiki/contribs/index "http://www.sympa.org/wiki/contribs/index") for useful migration tools.
-->

