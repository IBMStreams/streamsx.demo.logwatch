# streamsx.demo.logwatch

The purpose of the LogWatch family of applications is to teach concepts in SPL 
and Streams through a self-contained, small application with a well-defined 
problem statement. The application in the `language` sub-namespace is a language 
tutorial, and the application in the `performance` sub-namespace builds on that 
starting point, but with the goal of demonstrating strategies to improve 
performance.

## Problem Statement

The LogWatch family of applications are in the security domain. They watch
the system messages file on a Linux system, (usually found at `/var/log/messages`), flagging
security breaches. Breakins are detected using the following observation:

> If the same remote host attempts to login many times in a short period
> of time, then succeeds, it is likely a breakin.

We can also track long and medium term suspicious behavior, based on the number
of failed login attempts in a time frame from the same remote host. While applications 
in the LogWatch family be be implemented in different ways, they all solve the above problem.

## Iterative developement:

In all of the applications, SPL files in the `etc` directory show iterative development of that 
particular application. Since these applications are intended for teaching purposes, the final 
program is not the primary goal. Instead, the goal is to teach a development process. The sequence of 
SPL programs in the `etc` directory for each application demonstrate the iterative process used 
to accomplish the end program.

## Contact
Scott Schneider, scott.a.s@us.ibm.com

