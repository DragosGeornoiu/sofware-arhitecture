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

The Three Principal Layers: presentation, domain, and data source.

| Layer      | Responsabilities |
| ----------- | ----------- |
| Presentation      | Provision of services display of information (e.g., in Windowes or HTML, handling of user request (mouse-clicks, keyboard hits), HTTP requests, command-line invocations, batch API)       |
| Domain   | Logic thjat is the real point of the system        |
| Data Source   | Communication with databases, messaging systems, transaction, managers, other packages        |

### Organizing Domain Logic

#### Transaction Script

The simplest approach to storing domain logic is the Transaction Script. A Transaction Script is
essentially a procedure that takes the input from the presentation, processes it with validations
and calculations, stores data in the database, and invokes any operations from other systems. It
then replies with more data to the presentation, perhaps doing more calculation to help organize and
format the reply. The fundamental organization is of a single procedure for each action that a user
might want to do. Hence, we can think of this pattern as being a script for an action, or business
transaction. It doesn't have to be a single inline procedure of code. Pieces get separated into
subroutines, and these subroutines can be shared between different Transaction Scripts. However, the
driving force is still that of a procedure for each action, so a retailing system might have
Transaction Scripts for checkout, for adding something to the shopping cart, for displaying delivery
status, and so on.

#### Domain Model

Of course, complex logic is where objects come in, and the object-oriented way to handle this
problem is with a Domain Model. With a Domain Model we build a model of our domain which, at least
on a first approximation, is organized primarily around the nouns in the domain. Thus, a leasing
system would have classes for lease, asset, and so forth. The logic for handling validations and
calculations would be placed into this domain model, so shipment object might contain the logic to
calculate the shipping charge for a delivery. There might still be routines for calculating a bill,
but such a procedure would quickly delegate to a Domain Model method.

#### Table Module

A Table Module is in many ways a middle ground between a Transaction Script and a Domain Model.
Organizing the domain logic around tables rather than straight procedures provides more structure
and makes it easier to find and remove duplication. However, you can't use a number of the
techniques that a Domain Model uses for finer grained structure of the logic, such as inheritance,
strategies, and other OO patterns.

The biggest advantage of a Table Module is how it fits into the rest of the architecture. Many GUI
environments are built to work on the results of a SQL query organized in a Record Set. Since a
Table Module also works on a Record Set, you can easily run a query, manipulate the results in the
Table Module, and pass the manipulated data to the GUI for display. You can also use the Table
Module on the way back for further validations and calculations. A number of platforms, particularly
Microsoft's COM and .NET, use this style of development.

#### Service Layer

A common approach in handling domain logic is to split the domain layer in two. A Service Layer is
placed over an underlying Domain Model or Table Module. Usually you only get this with a Domain
Model or Table Module since a domain layer that uses only Transaction Script isn't complex enough to
warrant a separate layer. The presentation logic interacts with the domain purely through the
Service Layer, which acts as an API for the application.

As well as providing a clear API, the Service Layer is also a good spot to place such things as
transaction control and security. This gives you a simple model of taking each method in the Service
Layer and describing its transactional and security characteristics.

My preference is thus to have the thinnest Service Layer you can, if you even need one. My usual
approach is to assume that I don't need one and only add it if it seems that the application needs
it. However, I know many good designers who always use a Service Layer with a fair bit of logic, so
feel free to ignore me on this one. Randy Stafford has had a lot of success with a rich Service
Layer, which is why I asked him to write the Service Layer pattern for this book.

### Mapping to Relational Databases

#### Architectural Patterns

It's wise to separate SQL access from the domain logic and place it in separate classes. A good way
of organizing these classes is to base them on the table structure of the database so that you have
one class per database table. These classes then form a Gateway to the table. The rest of the
application needs to know nothing about SQL, and all the SQL that accesses the database is easy to
find. Developers who specialize in the database have a clear place to go.

There are two main ways in which you can use a Gateway.

The most obvious is to have an instance of it for each row that's returned by a query. This Row Data
Gateway is an approach that naturally fits an object-oriented way of thinking about the data.

Many environments provide a Record Set — that is, a generic data structure of tables and rows that
mimics the tabular nature of a database. Because a Record Set is a generic data structure,
environments can use it in many parts of an application. It's quite common for GUI tools to have
controls that work with a Record Set. If you use a Record Set, you only need a single class for each
table in the database. This Table Data Gateway provides methods to query the database that return a
Record Set.

When using the Gateway you can end up with the Domain Model coupled to the schema of the database.
As a result there's some transformation from the fields of the Gateway to the fields of the domain
objects, and this transformation complicates your domain objects. A better route is to isolate the
Domain Model from the database completely, by making your indirection layer entirely responsible for
the mapping between domain objects and database tables. This Data Mapper handles all of the loading
and storing between the database and the Domain Model and allows both to vary independently. It's
the most complicated of the database mapping architectures, but its benefit is complete isolation of
the two layers.


....