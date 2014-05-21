# LogWatchUDP

LogWatchUDP is an extension of the LogWatch sample application to demonstrate 
the user-defined parallelism (UDP) feature introduced in Streams 3.2.

# Differences from LogWatch

In this version of the application, we have invoked two operators 
(`ParsedLines` and `Failures`) with UDP to take advantage of data parallelism.
These operators are the most expensive operators in the application, which 
means they are *bottlenecks*. The throughput of SPL applications will be 
limited by the throughput of the bottlenecks in the application. Improving the 
performance of these bottlenecks will improve the overall throughput. 
Exploiting data parallelism in general, and using UDP specifically, is one way 
improve the performance of operators.

The logic in our application expects to receive tuples in the same order as they 
appear in the `/var/log/messages` system file. However, tuples can be processed 
in a UDP region in any order, and their results can leave the region in any 
order. To put the tuples back in order, we have implemented the `UDPMerge` 
composite operator. This operators takes a tuple attribute as a parameter, and 
then it uses that tuple attributes to perform an ordered merge on all tuples from 
all parallel channels. In this case, that tuple attribute is `seqno`. Before the 
parallel region, we make sure that the `seqno` attribute does provide a total 
ordering of tuples by giving successive tuples successive sequence numbers.

This technique works because no tuples are "lost" or "created" in the parallel
region. By "lost", we mean that no tuples are filtered out. (There are no 
invocations of a `Filter` operator in the parallel region, for example.) By 
"created", we mean that we never submit more than one tuple for each tuple we 
see. We commonly call this concept *selectivty*. The selectivity of the parallel 
regions in LogWatchUDP is 1. (Less than 1 would mean tuples were "lost", more than 
1 would mean tuples are "created".)

Finally, because UDP is primarily used to improve performance, we have 
instrumented the application so that it reports its throughput in the output 
file `ExecTime.txt`. By reporting application performance, we make it possible to measure 
the performance impact of UDP.

# Original Description

LogWatch is a sample SPL application in the security domain. LogWatch watches
the system messages file (usually found at `/var/log/messages`), flagging
security breaches. Breakins are detected using the following observation:

> If the same remote host attempts to login many times in a short period
> of time, then succeeds, it is likely a breakin.

In addition, the application also tracks long and medium term suspicious
behavior, based on the number of failed login attempts in a time frame from
the same remote host.

## Compiling

To build a standalone application:

    $ make standalone

To build a distributed application:

    $ make distributed

The makefile assumes that the environment variable `STREAMS_INSTALL` points 
to the root installation of Streams. LogWatchUDP also requires Streams version 
3.2 or higher.

## Executing

For standalone:

    $ ./output/bin/standalone file="<input file>"

For distributed: 

    $ streamtool submitjob output/LogWatchUDP.adl file="<input file>"
  
## Concepts and features covered

SPL concepts and features used in the application:

  * User-defined SPL functions.
  * Composite operator definition and multiple instantiations.
  * Composite operator parameters.
  * Count based, partitioned aggregation.
  * Count based join.
  * Submission time values.
  * Data parsing patterns using Custom operators.
  * Simple and ranged list accesses.
  * Simple substring searching.
  * Simple regular expression matching.
  * Simple timestamp manipulation
  * User-defined parallelism (UDP)

## Iterative developement:

The SPL files in the `etc` directory show iterative development of the 
LogWatchUDP application. We start with the original implementation of 
LogWatch, and then we apply UDP to the two operators. As a last step, we 
explicitly fuse operators into PEs and insert threaded ports.

## Input:

`messages.gz`: Actual messages log taken from a public-facing machine on
February 13, 2011. No breakins ocurred during this window.

`messages_broken.gz`: Doctored version of the previous in order to simulate
successful breakins.

Note that both of these files are compressed, and that Streams will 
decompress them on-the-fly.

## Output:

`Breakins.txt`: Successful breakins with time of breakin, originating host
and user name.

`ExecTime.txt`: Reports the performance of the run, with the time taken, total 
tuples processed, and throughput.

## Contact
Scott Schneider, scott.a.s@us.ibm.com

