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

### Dependencies

It works best with GNU awk, as `mawk` doesn't have an option to ignore the case.

## Feedback welcome!

Add a comment, create an issue, ping me on [Twitter](https://twitter.com/fpavageau)...


------

Licensed under the Apache License, Version 2.0
