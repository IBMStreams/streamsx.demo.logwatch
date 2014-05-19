# LogWatch

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
to the root installation of Streams. LogWatch also requires Streams version 2.0 
or higher.

## Executing

For standalone:

    $ ./output/bin/standalone file="<input file>"

For distributed: 

    $ streamtool submitjob output/LogWatch.adl file="<input file>"
  
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

## Iterative developement:

The SPL files in the `etc` directory show iterative development of the LogWatch 
application. I have found this sequence of development useful in slowly introducing 
streaming concepts and SPL features while solving a real problem.

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

`LongTerm.txt`: Record of remote hosts that have made 300 attempts to
login in the past hour. Also includes the time of the latest attempt,
and the time between the first and lastest attempt.

`MediumTerm.txt`: Record of remote hosts that have made 50 attempts to login
in the past 10 minutes. Also includes the time of the latest attempt,
and the time between the first and lastest attempt.

`RealTime.txt`: Record of remote hosts that have made 5 attempts to login
in the past minute. Also includes the time of the latest attempt, and
the time between the first and lastest attempt.

## Contact
Scott Schneider, scott.a.s@us.ibm.com

