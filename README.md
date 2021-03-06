Summary
-------

BeanTool is a console tool for quering your *[beanstalkd](http://kr.github.io/beanstalkd)* server. The name of the executable is *bt*, and provides the following subcommands. You may list the subcommands and their descriptions by running __"bt -h"__.


Installation
------------

### Install from *pip*

```bash
$ sudo pip install beantool
```

### Install manually

1. git clone https://github.com/dsoprea/BeanTool beantool
2. cd beantool
3. sudo python setup.py install


Subcommands
-----------

The subcommands are listed below along with an example. However, there are many options that are not displayed (hostname, tube name, priority, etc..). Each subcommand has its own command-line help.

### server_stats

Show server-level statistics

```bash
$ bt server_stats
```

```
Server stats:

{
  "current-connections": 1,
  "max-job-size": 65535,
  "cmd-release": 11,
  "cmd-reserve": 25,
  "pid": 53138,
  "cmd-bury": 1,
  "current-producers": 0,
  "total-jobs": 14,
  "current-jobs-ready": 0,
  "cmd-peek-buried": 1,
  "current-tubes": 1,
  "id": "e480716f4fc498f8",
  "current-jobs-delayed": 0,
  "uptime": 11274,
  "cmd-watch": 21,
  "hostname": "dustinx",
  "job-timeouts": 0,
  "cmd-stats": 2,
  "rusage-stime": 0.016621,
  "version": 1.9,
  "current-jobs-reserved": 0,
  "current-jobs-buried": 0,
  "cmd-reserve-with-timeout": 11,
  "cmd-put": 14,
  "cmd-pause-tube": 0,
  "cmd-list-tubes-watched": 0,
  "cmd-list-tubes": 4,
  "current-workers": 0,
  "cmd-list-tube-used": 0,
  "cmd-ignore": 0,
  "binlog-records-migrated": 0,
  "current-waiting": 0,
  "cmd-peek": 42,
  "cmd-peek-ready": 8,
  "cmd-peek-delayed": 1,
  "cmd-touch": 0,
  "binlog-oldest-index": 0,
  "binlog-current-index": 0,
  "cmd-use": 26,
  "total-connections": 125,
  "cmd-delete": 15,
  "binlog-max-size": 10485760,
  "cmd-stats-job": 18,
  "rusage-utime": 0.006289,
  "cmd-stats-tube": 11,
  "binlog-records-written": 0,
  "cmd-kick": 0,
  "current-jobs-urgent": 0
}
```

### tube_list

List tubes

```bash
$ bt tube_list
```

```
Current tubes:

[
  "default"
]
```

### tube_stats

Show tube-level statistics

```bash
$ bt tube_stats default
```

```
Tube stats:

{
  "current-jobs-delayed": 0,
  "pause": 0,
  "name": "default",
  "cmd-pause-tube": 0,
  "current-jobs-buried": 0,
  "cmd-delete": 1,
  "pause-time-left": 0,
  "current-waiting": 0,
  "current-jobs-ready": 0,
  "total-jobs": 1,
  "current-watching": 1,
  "current-jobs-reserved": 0,
  "current-using": 1,
  "current-jobs-urgent": 0
}
```

### tube_kick

Kick (requeue) buried job(s)

```bash
$ bt tube_kick
```

```
Kicking buried jobs.

{
  "kicked": 0
}
```

### job_put

Push a job

```bash
$ bt job_put 'job data'
```

```
Job created:

{
  "job_id": 15
}
```

### job_peek

Peek on a specific job

```bash
$ bt job_peek 15
```

```
<JOB (15)>

job data
```

### job_peek_ready

Peek for 'ready' jobs

```bash
$ bt job_peek_ready
```

```
<JOB (15)>

job data
```

### job_peek_delayed

Peek for 'delayed' jobs

```bash
$ bt job_peek_delayed
```

### job_peek_buried

Peek for 'buried' jobs

```bash
$ bt job_peek_buried
```

### job_stats

Get job stats

```bash
$ bt job_stats 15
```

```
Job stats:

{
  "buries": 0,
  "time-left": 0,
  "releases": 0,
  "tube": "default",
  "timeouts": 0,
  "ttr": 120,
  "age": 566,
  "pri": 2147483648,
  "delay": 0,
  "state": "ready",
  "reserves": 0,
  "file": 0,
  "kicks": 0,
  "id": 15
}
```

### job_delete

Delete job

```bash
$ bt job_delete 15
```

```
Deleted.
```

Job Terminal
-------------

There is one more subcommand not covered above: *job_reserve*

Because a socket disconnect will cause *beanstalkd* to requeue a job reserved by a client, calling *job_reserve* will send the user into the "*job terminal*". In this mode, *beantool* will reserve one job, and wait for you to enter a number of commands:

```bash
$ bt job_reserve 
```

```
Requesting job.

Job Terminal
============

Job with ID (10) has been reserved. You may manipulate it, here.

# Commands:
#
#      stats                      Display job information
#     delete                      Delete job
#    release [priority] [delay]   Release job back to queue
#       bury [priority]           Bury job
#      touch                      Re-lease job
#       data                      Show job data
#       help                      Display help
#       quit                      Quit

-> 
```

Performing a state-change on the job or explicitly asking to quit will cause the session to close.


### stats

Display job metadata.

```
-> stats
```

```
{'age': 593,
 'buries': 0,
 'delay': 0,
 'file': 0,
 'id': 10,
 'kicks': 0,
 'pri': 2147483648,
 'releases': 0,
 'reserves': 3,
 'state': 'reserved',
 'time-left': 77,
 'timeouts': 1,
 'ttr': 120,
 'tube': 'default'}
 ```


### delete

Delete/finish the job.

```
-> delete
```

```
Job deleted.
Job terminal stopped.
```


### release

Requeue the job.

```
-> release
```

```
Job released.
Job terminal stopped.
```


### bury

Exclude the job from being processed in the future.

```
-> bury
```

```
Job buried.
Job terminal stopped.
```


### touch

Re-lease the job.

```
-> touch
```

```
Job touched.
```


### data

Display the job data.

```
-> data
```

```
['string 1', 'string 2']
```


Notes
=====

1. Human readable text and phrasing is printed to STDERR. Data is printed to STDOUT  (does not apply to *Job Terminal*).
2. Everything written to STDOUT is encoded as JSON, except for job-bodies (does not apply to *Job Terminal*).
