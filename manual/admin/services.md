---
title: 'Managing services'
prev: ../upgrade.md
up: ../admin.md
next: cli.md
---

Managing services
=================

Services overview
-----------------

### Sympa services

Several daemons collaboratively provide mailing list service.

  * [``archived.pl``](/gpldoc/man/archived.8.html)

    Archiving daemon.

  * [``bounced.pl``](/gpldoc/man/bounced.8.html)

    Bounce processing daemon.

  * [``bulk.pl``](/gpldoc/man/bulk.8.html)

    Daemon for submitting messages to mail transfer agent (MTA).
    It may fork several workers as necessity.

  * [``sympa_msg.pl``](/gpldoc/man/sympa_msg.8.html)

    Daemon to handle incoming messages.
    It may fork several workers as necessity.

  * [``task_manager.pl``](/gpldoc/man/task_manager.8.html)

    Daemon to process periodical tasks.

And optionally:

  * [``sympa_automatic.pl``](/gpldoc/man/sympa_automatic.8.html)

    Automatic list creation daemon.
    It may be invoked by ``sympa_msg.pl`` as necessity.

### Mail transfer agent (MTA)

Mail transfer agent (MTA) injects incoming messages into incoming spool of
Sympa, and takes outgoing messages from Sympa up to distribute them beyond
Internet.

### HTTP server and WWSympa

HTTP server provides users web interface on behalf of WWSympa
([``wwsympa.fcgi``](/gpldoc/man/wwsympa.8.html)), the FastCGI server.

WWSympa may either be invoked by HTTP server or launch as an independent
daemon.

### Database server

Database server provides backend database for services above.

Signals
-------

Daemons of Sympa services accept following signals:

* ``TERM``

  Daemon finishes process in progress and then terminates safely.
  ``INT`` signal is also recognized.

* ``HUP``

  For ``sympa_msg.pl`` and ``sympa_automatic.pl`` only.
  Daemon reloads configuration then continues processing.

Anyway, recommended procedure to reload, stop or start the services is
described in succeeding sections.

Checking status of services
---------------------------

### Checking status of Sympa services

  * Systemd:
    ```bash
    # systemctl status 'sympa*'
    ```

  * initscripts or FreeBSD ports:
    ```bash
    # service sympa status
    ```

Reloading services
------------------

### Reloading Sympa services

  * Systemd:
    ```bash
    # systemctl restart sympa
    # systemctl status sympa
    ```

  * initscripts or FreeBSD ports:
    ```bash
    # service sympa restart
    # service sympa status
    ```

### Reloading WWSympa

  * Automatic reloading

    If [``wwsympa.fcgi``](/gpldoc/man/wwsympa.8.html) script was updated, it detects
    change of itself and exits.  Then WWSympa will be automatically reloaded.

    Exception is the case running separate FastCGI service with initscripts:
    If script exits,
    it have to be restared manually by executing such as:
    ```
    # service wwsympa start
    ```

  * Forced reloading

    To force reloading, restart HTTP server.

    Exception is the case running separate FastCGI service (either with
    Systemd or initscripts):
    Only that service (``wwsympa``) may be reloaded by
    ``service wwsympa restart`` or ``systemctl restart wwsympa``.

Stopping and starting services
------------------------------

### Stopping services

  1. Ensure that HTTP server is stopping.

     If you are running separate FastCGI service, only that service
     (``wwsympa``) may be stopped
     in many cases: HTTP server itself may not be stopped.

  2. Ensure that mail server is stopping.

  3. Stop Sympa services.

       * Systemd:
         ```bash
         # systemctl stop sympa
         # systemctl status sympa
         ```

       * initscripts:
         ```bash
         # service sympa stop
         # service sympa status
         ```

  4. Database service may be stopped when all services above are stopping.

### Starting services

  1. Ensure that database service is running.

  2. Start Sympa services.

       * Systemd:
         ```bash
         # systemctl start sympa
         # systemctl status sympa
         ```

       * initscripts or FreeBSD ports:
         ```bash
         # service sympa start
         # service sympa status
         ```

  3. Start mail server.

  4. Start HTTP server, if necessary.

     If you are running separate FastCGI service, that service
     (``wwsympa``) must be started
     along with HTTP server itself.

