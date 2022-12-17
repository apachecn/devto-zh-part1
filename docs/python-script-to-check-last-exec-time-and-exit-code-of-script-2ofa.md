# Python 脚本检查脚本的最后执行时间和退出代码

> 原文：<https://dev.to/rndmh3ro/python-script-to-check-last-exec-time-and-exit-code-of-script-2ofa>

我需要一个脚本来检查:

*   一个 [cronjob](http://en.wikipedia.org/wiki/Cron) 是否在其确定的日期运行(这意味着它定期运行)
*   如果 cronjob 运行成功

它还应该可以通过 [Nagios](http://www.nagios.org) 进行监控。这意味着，当 cronjob 出现问题时，它应该提供有意义的输出，例如“CRITICAL: cronjob 没有运行！”

当然，有人已经有了相同的需求，并为其构建了一个 Perl 脚本，这反过来可以用作 NRPE 插件。

为此，必须编辑要监控的 cronjob，以便将每个输出及其退出代码记录到日志文件中。然后，该脚本执行上述检查。你可以在这里找到更详细的解释[。](http://blog.endpoint.com/2012/04/monitoring-cronjob-exit-codes-with.html)

Perl 脚本缺少两点:

1.  两个参数(上次执行时间和退出代码)都是必需的
2.  一个 Python 实现！

为了解决这些问题，我自己写了一个小脚本，即[在](https://github.com/rndmh3ro/check_exit-code) [Github](http://github.com) 和这里(因为它很小):
上托管

```
#!/usr/bin/env python

"""
This script can check whether a file was last modified before a time X. If it was modified after that,
an error is raised. It can also check, whether the file contains
 a specific string (e.g. "exit 0"). If it's not there, an error is raised.

 Example:
 check_exit_code.py --exitcode -t 20 /path/to/logfile
"""

import os
import argparse
import time
import re

# Parse arguments
parser = argparse.ArgumentParser(
    description= __doc__ ,
    formatter_class=argparse.ArgumentDefaultsHelpFormatter)

parser.add_argument("logfile", help="Specify the logfile to monitor.")
parser.add_argument("-t", "--time", help="Time in minutes which a log file can be unmodified before raising CRITICAL"
                                     " alert.", type=int)
parser.add_argument("--exitcode", help="If specified, check if \"exit 0\" exists in logfile.", action="store_true")

args = parser.parse_args()

if not (args.time or args.exitcode):
    print "At least one argument (--time, --exitcode) is required."
    exit(2)

#variables
log = args.logfile

# Check if file exists and is readable by user
try:
    f = open(log)
except IOError:
    print "Cannot open the file. Check if it exists and you have the right permissions to open it."
    exit(1)

# check exit code
if args.exitcode:
    logfile = f.read()
    if not re.search("exit 0", logfile, flags=re.I):
        print "CRITICAL: Exit code not found in File %s" % log
        exit(2)

# Check last file modification time
if args.time:
    # File Modification time in seconds since epoch
    file_mod_time = os.stat(log).st_mtime

    # Time in seconds since epoch for time, in which logfile can be unmodified.
    t = time.time()
    should_time = t - (args.time * 3600)

    # Time in minutes since last modification of file
    last_time = (t - file_mod_time) / 60

    if last_time > args.time:
        print "CRITICAL: {} last modified {:.2f} minutes. Threshold set to {:.2f} minutes".format(log, last_time,
                                                                                              args.time)
        exit(2)

# If nothing went wrong, print OK and exit.
print "OK. Exitcode found or last modification time not exceeded."
exit(0) 
```