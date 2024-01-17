# Patterns of Enterprise Application Architecture by Martin Fowler

## Conclusions

[comment]: <> (TODO)

## Notes

### Common performance related terms

Response time is the amount of time it takes for the system to process a request from the outside.
This may be a UI action, such as pressing a button, or a server API call.

Responsiveness is about how quickly the system acknowledges a request as opposed to processing it.
This is important in many systems because users may become frustrated if a system has low
responsiveness, even if its response time is good. If your system waits during the whole request,
then your responsiveness and response time are the same. However, if you indicate that you've
received the request before you complete, then your responsiveness is better. Providing a progress
bar during a file copy improves the responsiveness of your user interface, even though it doesn't
improve response time.

Latency is the minimum time required to get any form of response, even if the work to be done is
nonexistent. It's usually the big issue in remote systems. If I ask a program to do nothing, but to
tell me when it's done doing nothing, then I should get an almost instantaneous response if the
program runs on my laptop. However, if the program runs on a remote computer, I may get a few
seconds just because of the time taken for the request and response to make their way across the
wire. As an application developer, I can usually do nothing to improve latency. Latency is also the
reason why you should minimize remote calls.

Throughput is how much stuff you can do in a given amount of time. If you're timing the copying of a
file, throughput might be measured in bytes per second. For enterprise applications a typical
measure is transactions per second (tps), but the problem is that this depends on the complexity of
your transaction. For your particular system you should pick a common set of transactions.

Load is a statement of how much stress a system is under, which might be measured in how many users
are currently connected to it. The load is usually a context for some other measurement, such as a
response time. Thus, you may say that the response time for some request is 0.5 seconds with 10
users and 2 seconds with 20 users.

Load sensitivity is an expression of how the response time varies with the load. Let's say that
system A has a response time of 0.5 seconds for 10 through 20 users and system B has a response time
of 0.2 seconds for 10 users that rises to 2 seconds for 20 users. In this case system A has a lower
load sensitivity than system B. We might also use the term degradation to say that system B degrades
more than system A.

Efficiency is performance divided by resources. A system that gets 30 tps on two CPUs is more
efficient than a system that gets 40 tps on four identical CPUs.

The capacity of a system is an indication of maximum effective throughput or load. This might be an
absolute maximum or a point at which the performance dips below an acceptable threshold.

Scalability is a measure of how adding resources (usually hardware) affects performance. A scalable
system is one that allows you to add hardware and get a commensurate performance improvement, such
as doubling how many servers you have to double your throughput. Vertical scalability, or scaling
up, means adding more power to a single server, such as more memory. Horizontal scalability, or
scaling out, means adding more servers.

### Layering