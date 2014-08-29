Tools to manipulate Java thread dumps
======

## jtgrep

It's a shell script that prints threads from a thread dump matching a pattern. It also prints the rest of the context
from the thread dump, e.g. the header with the date.
 
It should work with thread dumps from the Oracle JVM and OpenJDK.

### Examples

Print all threads where the string "GC" appears:

    $ jtgrep GC thread-dump-19486-20140827-123856.log
    2014-08-27 12:38:57
    Full thread dump Java HotSpot(TM) 64-Bit Server VM (23.2-b09 mixed mode):
    
    "GC Daemon" daemon prio=10 tid=0x0000000019229800 nid=0x4e06 in Object.wait() [0x00002b9ea5dce000]
       java.lang.Thread.State: TIMED_WAITING (on object monitor)
            at java.lang.Object.wait(Native Method)
            - waiting on <0x00000007280274b8> (a sun.misc.GC$LatencyLock)
            at sun.misc.GC$Daemon.run(GC.java:117)
            - locked <0x00000007280274b8> (a sun.misc.GC$LatencyLock)
    
       Locked ownable synchronizers:
            - None
    
    "Surrogate Locker Thread (Concurrent GC)" daemon prio=10 tid=0x00000000176d0800 nid=0x4c2c waiting on condition [0x0000000000000000]
       java.lang.Thread.State: RUNNABLE
    
       Locked ownable synchronizers:
            - None
    
    "Gang worker#0 (Parallel GC Threads)" prio=10 tid=0x00000000174f7000 nid=0x4c20 runnable 
    
    "Gang worker#1 (Parallel GC Threads)" prio=10 tid=0x00000000174f9000 nid=0x4c21 runnable 
    
    "Gang worker#2 (Parallel GC Threads)" prio=10 tid=0x00000000174fb000 nid=0x4c22 runnable 
    
    "Gang worker#3 (Parallel GC Threads)" prio=10 tid=0x00000000174fc800 nid=0x4c23 runnable 
    
    "Gang worker#4 (Parallel GC Threads)" prio=10 tid=0x00000000174fe800 nid=0x4c24 runnable 
    
    "Gang worker#5 (Parallel GC Threads)" prio=10 tid=0x0000000017500800 nid=0x4c25 runnable 
    
    "Concurrent Mark-Sweep GC Thread" prio=10 tid=0x00000000175c3800 nid=0x4c28 runnable 
    JNI global references: 878

    $

It also works from standard input, if no files are named or if a single hyphen-minus (-) is given as file name:

    $ cat thread-dump-19486-20140827-123856.log | jtgrep nid=0x4e06
    2014-08-27 12:38:57
    Full thread dump Java HotSpot(TM) 64-Bit Server VM (23.2-b09 mixed mode):
    
    "GC Daemon" daemon prio=10 tid=0x0000000019229800 nid=0x4e06 in Object.wait() [0x00002b9ea5dce000]
       java.lang.Thread.State: TIMED_WAITING (on object monitor)
        at java.lang.Object.wait(Native Method)
        - waiting on <0x00000007280274b8> (a sun.misc.GC$LatencyLock)
        at sun.misc.GC$Daemon.run(GC.java:117)
        - locked <0x00000007280274b8> (a sun.misc.GC$LatencyLock)
    
       Locked ownable synchronizers:
        - None
    
    JNI global references: 878

Just like grep, it can invert the matching:

    $ jtgrep GC thread-dump-19486-20140827-123856.log | jtgrep -v Surrogate
    2014-08-27 12:38:57
    Full thread dump Java HotSpot(TM) 64-Bit Server VM (23.2-b09 mixed mode):
    
    "GC Daemon" daemon prio=10 tid=0x0000000019229800 nid=0x4e06 in Object.wait() [0x00002b9ea5dce000]
       java.lang.Thread.State: TIMED_WAITING (on object monitor)
        at java.lang.Object.wait(Native Method)
        - waiting on <0x00000007280274b8> (a sun.misc.GC$LatencyLock)
        at sun.misc.GC$Daemon.run(GC.java:117)
        - locked <0x00000007280274b8> (a sun.misc.GC$LatencyLock)
    
       Locked ownable synchronizers:
        - None
    
    "Gang worker#0 (Parallel GC Threads)" prio=10 tid=0x00000000174f7000 nid=0x4c20 runnable 
    
    "Gang worker#1 (Parallel GC Threads)" prio=10 tid=0x00000000174f9000 nid=0x4c21 runnable 
    
    "Gang worker#2 (Parallel GC Threads)" prio=10 tid=0x00000000174fb000 nid=0x4c22 runnable 
    
    "Gang worker#3 (Parallel GC Threads)" prio=10 tid=0x00000000174fc800 nid=0x4c23 runnable 
    
    "Gang worker#4 (Parallel GC Threads)" prio=10 tid=0x00000000174fe800 nid=0x4c24 runnable 
    
    "Gang worker#5 (Parallel GC Threads)" prio=10 tid=0x0000000017500800 nid=0x4c25 runnable 
    
    "Concurrent Mark-Sweep GC Thread" prio=10 tid=0x00000000175c3800 nid=0x4c28 runnable 
    JNI global references: 878

It can also ignore the case:

    $ jtgrep GC thread-dump-19486-20140827-123856.log | jtgrep -vi surrogate
    2014-08-27 12:38:57
    Full thread dump Java HotSpot(TM) 64-Bit Server VM (23.2-b09 mixed mode):
    
    "GC Daemon" daemon prio=10 tid=0x0000000019229800 nid=0x4e06 in Object.wait() [0x00002b9ea5dce000]
       java.lang.Thread.State: TIMED_WAITING (on object monitor)
        at java.lang.Object.wait(Native Method)
        - waiting on <0x00000007280274b8> (a sun.misc.GC$LatencyLock)
        at sun.misc.GC$Daemon.run(GC.java:117)
        - locked <0x00000007280274b8> (a sun.misc.GC$LatencyLock)
    
       Locked ownable synchronizers:
        - None
    
    "Gang worker#0 (Parallel GC Threads)" prio=10 tid=0x00000000174f7000 nid=0x4c20 runnable 
    
    "Gang worker#1 (Parallel GC Threads)" prio=10 tid=0x00000000174f9000 nid=0x4c21 runnable 
    
    "Gang worker#2 (Parallel GC Threads)" prio=10 tid=0x00000000174fb000 nid=0x4c22 runnable 
    
    "Gang worker#3 (Parallel GC Threads)" prio=10 tid=0x00000000174fc800 nid=0x4c23 runnable 
    
    "Gang worker#4 (Parallel GC Threads)" prio=10 tid=0x00000000174fe800 nid=0x4c24 runnable 
    
    "Gang worker#5 (Parallel GC Threads)" prio=10 tid=0x0000000017500800 nid=0x4c25 runnable 
    
    "Concurrent Mark-Sweep GC Thread" prio=10 tid=0x00000000175c3800 nid=0x4c28 runnable 
    JNI global references: 878

Finally, it can also count threads instead of printing them, in which case the non-thread output is also suppressed:

    $ jtgrep -c RUN thread-dump-7660-20140829-104003.log
    11
    $ jtgrep -c RUN thread-dump-7660-20140829-1040*.log
    thread-dump-7660-20140829-104003.log:11
    thread-dump-7660-20140829-104013.log:13
    thread-dump-7660-20140829-104024.log:18
    thread-dump-7660-20140829-104034.log:18
    thread-dump-7660-20140829-104045.log:18
    thread-dump-7660-20140829-104055.log:14
    $ cat thread-dump-7660-20140829-1040*.log | jtgrep -c RUN
    92

### Usage

Running the command without any parameter will show its options:

    $ jtgrep 
    Usage: jtgrep [-i] [-v] PATTERN [FILE...]
        -c Replace normal output by a count of matching threads for each input file
        -h Suppress the prefixing of file names on output when counting
        -H Print the file name for each match when counting
        -i Ignore case distinctions in the pattern
        -v Invert the sense of matching, to select non-matching threads

### Dependencies

It works best with GNU awk, as `mawk` doesn't have an option to ignore the case.

## jtid

It's a shell script that prints thread names with their decimal ID, usable in the OS context. This information can for
example be used to read from `/proc/[PID]/task/[TID]/stat`.

### Examples

    $ jtgrep GC thread-dump-7660-20140829-104003.log | jtid
    "Surrogate Locker Thread (Concurrent GC)" 7674
    "Gang worker#0 (Parallel GC Threads)" 7662
    "Gang worker#1 (Parallel GC Threads)" 7663
    "Gang worker#2 (Parallel GC Threads)" 7664
    "Gang worker#3 (Parallel GC Threads)" 7665
    "Gang worker#4 (Parallel GC Threads)" 7666
    "Gang worker#5 (Parallel GC Threads)" 7667
    "Concurrent Mark-Sweep GC Thread" 7670

The PID of the JVM being 7660, we could read `/proc/7660/task/7670/stat` to get the ID of the last processor used by
the CMS thread (as the 39th field).

## Feedback welcome!

Add a comment, create an issue, ping me on [Twitter](https://twitter.com/fpavageau)...


------

Licensed under the Apache License, Version 2.0
