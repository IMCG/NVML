---
layout: manual
Content-Style: 'text/css'
title: rpmemd(1)
header: NVM Library
date: version 1.0.0
...

[NAME](#name)<br />
[SYNOPSIS](#synopsis)<br />
[DESCRIPTION](#description)<br />
[OPTIONS](#options)<br />
[CONFIGURATION FILES](#configuration-files)<br />
[EXAMPLES](#examples)<br />
[DEFAULT CONFIGURATION](#default-configuration)<br />
[PERSISTENCY METHODS](#persistency-methods)<br />
[SEE ALSO](#see-also)<br />


### NAME ###

**rpmemd** -- librpmem target node process

### SYNOPSIS ###

```
rpmemd [--help] [--version] [<args>]
```

### DESCRIPTION ###

The **rpmemd** process is executed on target node by **librpmem** library over
**ssh**(1) and facilitates access to persistent memory over RDMA. The **rpmemd**
should not be run manually under normal conditions.

### OPTIONS ###

Command line options overwrite the default **rpmemd** configuration, the global
configuration file and the user configuration file.

`-V, --version`

: Displays **rpmemd** version and exits.

`-h, --help`

: Prints synopsis and list of parameters and exits.

`-c, --config <path>`

: Custom configuration file location. If the custom configuration file is
provided others are omitted. See [CONFIGURATION FILES](#configuration-files)
section for details.

All options described in [CONFIGURATION FILES](#configuration-files) section are
common for both the configuration file and the command line. An equivalent of a
following line in the config file: `option = value` is `--option value` in the
command line.

`--persist-apm`, `--persist-general` and `--use-syslog` command line options
should not be followed by a value. Presence of each of them in the command line
turns on appropriate option. See [CONFIGURATION FILES](#configuration-files)
section for details.

### CONFIGURATION FILES ###

The **rpmemd** searches for the configuration files with following priorities:

+ The global configuration file located in /etc/rpmemd/rpmemd.conf.
+ The user configuration file located in the user home directory
($HOME/.rpmemd.conf).

The **rpmemd** can also read configuration from the custom configuration file
provided using `--config` command line option. See [OPTIONS](#options)
section for details.

The default configuration is described in the
[DEFAULT CONFIGURATION](#default-configuration) section.

The configuration file is a plain text file. Each line of the configuration file
can store only one configuration option defined as a `key=value` pair. Empty
lines and lines starting with `#` are omitted.

The allowed options are:

+ `log-file = <path>` - log file location
+ `poolset-dir = <path>` - poolset files directory
+ `persist-apm = {yes|no}` - enable **The Appliance Persistency Method**. This
option must be set only if the target platform has non-allocating writes IO
enabled. See [PERSISTENCY METHODS](#persistency-methods) section for details.
+ `persist-general = {yes|no}` - enable **The General Purpose Server Persistency
Method**. See [PERSISTENCY METHODS](#persistency-methods) section for
details.
+ `use-syslog = {yes|no}` - use syslog(3) for logging messages instead of log
file
+ `log-level = <level>` - set log level value. Accepted `<level>` values are:

    + `err` - error conditions
    + `warn` - warning conditions
    + `notice` - normal, but significant, condition
    + `info` - informational message
    + `debug` - debug-level message

The $HOME substring in the `poolset-dir` path is replaced with the current user
home directory.

### EXAMPLES ###

Example of the configuration file:

```
# This is an example of configuration file
log-file = $HOME/.logs/rpmemd.log
poolset-dir = $HOME/poolsets/
persist-apm = yes
persist-general = no
use-syslog = no # Use log file instead of syslog
log-level = info
```

### DEFAULT CONFIGURATION ###

The **rpmemd** default configuration is equivalent of the following
configuration file:

```
log-file = /var/log/rpmemd.log
poolset-dir = $HOME
persist-apm = no
persist-general = yes
use-syslog = yes
log-level = err
```

### PERSISTENCY METHODS ###

The **librpmem** supports two methods for making data written to remote
persistent memory durable. The difference between the use of the two mechanisms
is based on whether **librpmem** will make use of non-allocating writes on the
remote node.

+ **The General Purpose Server Persistency Method** does not have any
requirements for the platform on which the target daemon runs and can be enabled
by administrator using the `persist-general` option. This method utilize
**libpmem(3)** persistency mechanisms on remote node and requires additional
communication between initiator and remote node using the in-band connection.

+ **The Appliance Persistency Method** requires non-allocating writes enabled on
the platform and can be enabled by administrator using `persist-apm` option.
This method requires to issue an RDMA READ operation after the RDMA WRITE
operations performed on requested chunk of memory.

The Non-allocating write requests is the Intel Integrated IO Controller mode
where all incoming PCIe Writes will utilize non-allocating buffers for the write
requests. Non-allocating writes are guaranteed to bypass all of the CPU caches
and force the write requests to flow directly to the Integrated Memory
Controller without delay.

### SEE ALSO ###

**librpmem(3)**, **libpmem(3)**.
