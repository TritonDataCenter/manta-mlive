<!--
    This Source Code Form is subject to the terms of the Mozilla Public
    License, v. 2.0. If a copy of the MPL was not distributed with this
    file, You can obtain one at http://mozilla.org/MPL/2.0/.
-->

<!--
    Copyright (c) 2014, Joyent, Inc.
-->

# manta-mlive

This repository is part of the Joyent Manta project.  For contribution
guidelines, issues, and general documentation, visit the main
[Manta](http://github.com/joyent/manta) project page.

mlive is a small tool for monitoring Manta liveness, used mainly when doing
fault testing of a Manta instance.  The idea is to start mlive while Manta's
fine, then start the fault testing (restarting components, introducing
partitions, etc.).  mlive makes a small number of both read and write requests
and reports when it detects what looks like a partial or complete outage.

mlive is not a primary mechanism for monitoring Manta.  Manta provides alarms
for being notified of service issues and dashboards for monitoring service
health.

## Usage

    $ mlive --help
    usage: mlive [-f | --force] [-p | --path PATH] REPORT_TIME
    
    Runs a small, continuous read and write load against a Manta 
    service in order to assess liveness.  Prints out when outages 
    appear to start and end on a per-second interval.

Generally, you'd run mlive with one argument that specifies how many seconds
between reports when nothing has changed.  Here's example output:

    $ mlive 10
    will stomp over directory at "/dap/stor/mlive"
    will test for liveness: reads, writes
    assuming 3 metadata shards
    using base paths: /dap/stor/mlive
    time between requests: 100 ms
    maximum outstanding requests: 100
    environment:
        MANTA_USER = dap
        MANTA_URL = https://172.26.5.11
    creating test directory tree ... done.
    2014-12-16T17:35:27.059Z: reads okay, writes okay (9/9 ok since start)
    2014-12-16T17:35:37.067Z: all okay (98/98 ok since 2014-12-16T17:35:27.059Z)
    2014-12-16T17:35:47.074Z: all okay (98/98 ok since 2014-12-16T17:35:37.067Z)
    2014-12-16T17:35:57.080Z: all okay (100/100 ok since 2014-12-16T17:35:47.074Z)
    2014-12-16T17:36:07.088Z: all okay (98/98 ok since 2014-12-16T17:35:57.080Z)
    2014-12-16T17:36:17.098Z: all okay (98/98 ok since 2014-12-16T17:36:07.088Z)
    ...
    2014-12-16T17:44:42.501Z: all okay (98/98 ok since 2014-12-16T17:44:32.494Z)
    2014-12-16T17:44:52.507Z: all okay (98/98 ok since 2014-12-16T17:44:42.501Z)
    2014-12-16T17:45:00.515Z: all partial_outage (78/80 ok since 2014-12-16T17:44:52.507Z)
    2014-12-16T17:45:01.516Z: read partial_outage, write outage (4/9 ok since 2014-12-16T17:45:00.515Z)
    2014-12-16T17:45:02.516Z: all outage (0/10 ok since 2014-12-16T17:45:01.516Z)
    2014-12-16T17:45:03.518Z: read partial_outage, write outage (2/10 ok since 2014-12-16T17:45:02.516Z)
    2014-12-16T17:45:08.521Z: read okay, write outage (15/50 ok since 2014-12-16T17:45:03.518Z)
    2014-12-16T17:45:09.523Z: read partial_outage, write outage (2/10 ok since 2014-12-16T17:45:08.521Z)
    2014-12-16T17:45:17.531Z: read okay, write outage (20/78 ok since 2014-12-16T17:45:09.523Z)
    2014-12-16T17:45:19.532Z: read okay, write stuck (6/8 ok since 2014-12-16T17:45:17.531Z)
    2014-12-16T17:45:22.535Z: read partial_outage, write outage (5/10 ok since 2014-12-16T17:45:19.532Z)
    2014-12-16T17:45:29.546Z: read okay, write partial_outage (44/100 ok since 2014-12-16T17:45:22.535Z)
    2014-12-16T17:45:31.547Z: all okay (20/21 ok since 2014-12-16T17:45:29.546Z)
    2014-12-16T17:45:41.558Z: all okay (99/99 ok since 2014-12-16T17:45:31.547Z)
