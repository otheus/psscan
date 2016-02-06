# psscan
Continuously logs state of all interesting processes to a file

## Usage : `psscan [options] | logger -p user.info `

From `/etc/inittab` or `crond`, launch this program. It will continuously take a snapshot of the process table at the user-defined interval. 

## Options

A configuration file is (by default) read from `/etc/psscan.conf`. It is actually a perl file; configuration is of the form

```perl
 $VARS = 'string' ;

 $VARn = 12 ;
```

* The location of the configuration file may be specified on the command-line with `-c`. 
* The output directory is specified with `-l` or in the config file as `$LOG_DIR`. 
* The sample interval may be specified with `-i` or in the config file via `$INTERVAL`.
* Kernel threads are filtered by default; disabling of the filtering is with `-K` or `$SHOW_KTHREADS = 1`.
* Interactive processes are filtered by default and can be output with `-I` or `$SHOW_INTERACTIVE = 1`.
* The particular output fields are specified as an argument to `ps`'s `-o` option, via `-o` or `$PS_OUTPUT_FMT`.

## Motivation

The major concern is how system processes grow in memory usage and overall cpu consumption. Without such data collection, determining why a 
particular process is problematic can be difficult to determine. What this allows us to do is go back and find out not only _when_ a process
hung or crashed, but possibly _why_ (memory usage, ulimit barrier, etc).

A simple while-loop around `ps` would accomplish the same thing; indeed, that is how this project started. The first improvement is the 
elimination from output of kernel-threads. The next improvement is the elimination from output of interactive processes (ie, bound to tty) -- 
On many systems, interactive processes are not interesting. Further improvements include : prevention from multiple instances writing to the same file, graceful restart on HUP, auto-rotation of the 
output file (on a daily basis). The auto-restart feature can be used to prevent the program itself from being starved or ensnared by Linux's OOM Killer.

## Future

* A tool to analyze the file and determine trends and anomalies, searching by process name or group.
* Sample the process table and output only changesets according to particular thresholds.



