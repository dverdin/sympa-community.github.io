---
title: 'Configure mail server'
priv: configure-system-log.md
up: ../install.md
next: configure-http-server.md
---

Configure mail server
=====================

Requirements
------------

  * Mail transfer agent (MTA):
    See also "[Requirements](../requirements.md#mail-transfer-agent-mta)".

  * A mail domain name for the mailing list service.
    See also "[Requirements](../requirements.md#network-requirements)".

    Through the instructions in this chapter, ``mail.example.org`` will be
    used for example.

Instruction by MTAs
-------------------

  - ~~[exim](configure-mail-server-exim.md)~~ (Work in progress)
  - [OpenSMTPD](configure-mail-server-opensmtpd.md)
  - [Postfix](configure-mail-server-postfix.md)
  - [Sendmail](configure-mail-server-sendmail.md)

Tests
-----

  1. Start mail user agent (MUA) on your PC or PDA.

     ----
     Note:

       * If you are not allowed to use MUA, you may use telnet client instead:
         See also "[Telnet example](#telnet-example)" below.

     ----

  2. Send any message to ``sympa-request@mail.example.org``.
     And confirm that your message will be delivered to ``postmaster``.

  3. Send a message with a subject "help" to ``sympa@mail.example.org``.
     And confirm that the message will be stored into
     [incoming spool directory](/gpldoc/man/sympa_config.5.html#queue) (by default
     it is ``msg`` subdirectory in [``$SPOOLDIR``](../layout.md#spooldir)
     directory).

     ----
     Note:

       * If the services have already started (or once they start), this
         message will be removed immediately, and a help messsage will be sent
         back to you.

     ----

  4. Send any message to ``bounce+hogehoge@mail.example.org``.
     And confirm that the message will be stored into
     [bounce spool directory](/gpldoc/man/sympa_config.5.html#queuebounce) (by default
     it is ``bounce`` subdirectory in [``$SPOOLDIR``](../layout.md#spooldir)
     directory).

     ----
     Note:

       * If the services have already started (or once they start), this
         message will be immediately moved into ``bad`` subdirectory of the
         spool.

     ----

If something went unexpected, check mail system log and configuration of MTA.

### Telnet Example

  1. Open your favorite telnet tools (telnet or putty for example).

  2. Open a session to your postfix server and use this command (replace
     example by your addresses).
     ```
     HELO example.fr
     250 yourserver
     MAIL FROM:<user@example.fr>
     250 2.1.0 Ok
     RCPT TO:<sympa-request@mail.example.org>
     250 2.1.5 Ok
     RCPT TO:<sympa@mail.example.org>
     250 2.1.5 Ok
     RCPT TO:<bounce+hogehoge@mail.example.org>
     250 2.1.5 Ok
     DATA
     354 End data with <CR><LF>.<CR><LF>
     Message-ID: <Something>
     From: <user@example.fr>
     Subject: Hello

     Hello World
     .
     250 2.0.0 Ok: queued as XXXXXXXXXXXXXX
     QUIT
     221 2.0.0 Bye
     ```
This should be enough to see if everything is ok on the MTA side.

