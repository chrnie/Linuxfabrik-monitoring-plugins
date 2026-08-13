# Check redis-version


## Overview

Checks the installed Redis version against the endoflife.date API and alerts if the version is end-of-life or if newer releases are available.

**Important Notes:**

* Redis distributions that ship a prefixed server binary are supported via `--path`. For Icinga DB Redis, use `--path=/usr/bin/icingadb-redis-server`.

**Data Collection:**

* Runs `redis-server --version` locally to determine the installed version, using the binary given by `--path`
* Queries the endoflife.date API (<https://endoflife.date/api/redis.json>) and caches the result in a local SQLite database
* Must run on the Redis server itself


## Fact Sheet

| Fact | Value |
|----|---|
| Check Plugin Download                 | <https://github.com/Linuxfabrik/monitoring-plugins/tree/main/check-plugins/redis-version> |
| Nagios/Icinga Check Name              | `check_redis_version` |
| Check Interval Recommendation         | Every day |
| Can be called without parameters      | Yes |
| Runs on                               | Cross-platform |
| Compiled for Windows                  | No |
| Uses SQLite DBs                       | `$TEMP/linuxfabrik-lib-version.db` |


## Help

```text
usage: redis-version [-h] [-V] [--always-ok] [--check-major] [--check-minor]
                     [--check-patch] [--insecure] [--no-perfdata] [--no-proxy]
                     [--offset-eol OFFSET_EOL] [--path PATH]
                     [--timeout TIMEOUT]
                     [--unreachable-severity {ok,warn,crit,unknown}]

Checks the installed Redis version against the endoflife.date API and alerts
if the version is end-of-life or if newer major, minor, or patch releases are
available. By default, alerts 30 days before the official EOL date. The offset
is configurable.

options:
  -h, --help            show this help message and exit
  -V, --version         show program's version number and exit
  --always-ok           Always returns OK.
  --check-major         Alert when a new major release is available, even if
                        the current version is not yet EOL. Example: running
                        v26 (not yet EOL) and v27 is available.
  --check-minor         Alert when a new major.minor release is available,
                        even if the current version is not yet EOL. Example:
                        running v26.2 (not yet EOL) and v26.3 is available.
  --check-patch         Alert when a new major.minor.patch release is
                        available, even if the current version is not yet EOL.
                        Example: running v26.2.7 (not yet EOL) and v26.2.8 is
                        available.
  --insecure            This option explicitly allows insecure SSL
                        connections.
  --no-perfdata         Suppress the performance data section from the output.
                        The status message and the exit code are unaffected,
                        so alerting keeps working while trending data is
                        dropped.
  --no-proxy            Do not use a proxy.
  --offset-eol OFFSET_EOL
                        Alert n days before ("-30") or after an EOL date ("30"
                        or "+30"). Default: -30 days
  --path PATH           Local path to your redis-server binary, or its bare
                        name if it is in $PATH. Set this for Redis
                        distributions shipping a prefixed binary, for example
                        "/usr/bin/icingadb-redis-server". Default: redis-
                        server
  --timeout TIMEOUT     Network timeout in seconds. Default: 8 (seconds)
  --unreachable-severity {ok,warn,crit,unknown}
                        State to report when the online source is unreachable.
                        What is used instead - bundled offline data, a cached
                        copy, or nothing at all - is named in the output, and
                        a clean result then only covers what that fallback
                        could confirm. Default: ok

Documentation:
https://linuxfabrik.github.io/monitoring-plugins/check-plugins/redis-version/
```


## Usage Examples

```bash
./redis-version --offset-eol=-30
```

Output:

```text
Redis v7.0.13 (EOL unknown, minor 7.2.1 available)
```

Checking the Redis instance that ships with Icinga DB, which installs its binaries with an `icingadb-` prefix:

```bash
./redis-version --path=/usr/bin/icingadb-redis-server
```


## States

* WARN if the installed version is EOL.
* Optional: WARN when a new major version is available.
* Optional: WARN when a new minor version is available.
* Optional: WARN when a new patch version is available.
* `--always-ok` suppresses all alerts and always returns OK.


## Perfdata / Metrics

| Name | Type | Description |
|----|----|----|
| redis-version | Number | Installed Redis version as float. "3.3.22" becomes "3.322". |


## Credits, License

* Authors: [Linuxfabrik GmbH, Zurich](https://www.linuxfabrik.ch)
* License: The Unlicense, see [LICENSE file](https://unlicense.org/).
