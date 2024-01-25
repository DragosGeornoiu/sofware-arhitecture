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
objects, and this transformation complicates your domain objects.

A better route is to isolate the Domain Model from the database completely, by making your
indirection layer entirely responsible for the mapping between domain objects and database tables.
This Data Mapper handles all of the loading and storing between the database and the Domain Model
and allows both to vary independently. It's the most complicated of the database mapping
architectures, but its benefit is complete isolation of the two layers.

I don't recommend using a Gateway as the primary persistence mechanism for a Domain Model. If the
domain logic is simple and you have a close correspondence between classes and tables, Active Record
is the simple way to go. If you have something more complicated, Data Mapper is what you need.

OO databases exist, but they never gained popularity and never where backed up by big vendors as the
relational dabases are. Also, commercial O/R mapping tools exist.

#### The Behavioral Problem

When working with objects, it might seem as a trivial problem the fact that we need to save and load
them from the databse. but what happens if you read some ojects and want ot modify them or modify
other objects based on data from the objects you read, what happens if somone else changed the state
of the objects you read in the database. This is where the concept of Unit Of Work appears.

As you load objects, you have to be wary about loading the same one twice. If you do that, you'll
have two in-memory objects that correspond to a single database row. Update them both, and
everything gets very confusing. To deal with this you need to keep a record of every row you read in
an Identity Map . Each time you read in some data, you check the Identity Map first to make sure
that you don't already have it. If the data is already loaded, you can return a second reference to
it. That way any updates will be properly coordinated. As a benefit you may also be able to avoid a
database call since the Identity Map also doubles as a cache for the database. Don't forget,
however, that the primary purpose of an Identity Map is to maintain correct identities, not to boost
performance.

With many objects connected together any read of any object can pull an enormous object graph out of
the database. To avoid such inefficiencies you need to reduce what you bring back yet still keep the
door open to pull back more data if you need it later on. Lazy Load relies on having a placeholder
for a reference to an object.

#### Reading the data

When reading in data I like to think of the methods as finders that wrap SQL select statements with
a method-structured interface. Thus, you might have methods such as find(id) or findForCustomer(
customer).

When reading in data I like to think of the methods as finders that wrap SQL select statements with
a method-structured interface. Thus, you might have methods such as find(id) or findForCustomer(
customer). Clearly these methods can get pretty unwieldy if you have 23 different clauses in your
select statements, but these are, thankfully, rare.

Try to pull back multiple rows at once. In particular, never do repeated queries on the same table
to get multiple rows. It's almost always better to pull back too much data than too little (although
you have to be wary of locking too many rows with pessimistic concurrency control).

#### Structural Mapping Patterns

##### Mapping Relationships

The way a structure is mapped in a OO language is not translated always in the same way when mapped
to a table structure. For example an object having a collection of other objects will hold a
reference to this object in the OO language. In the table structure the referenced objects will hold
the reference (foreign key) to the initial object.

##### Inheritance

Three options on handling inheritance: Single Table Inheritance, Concrete Table Inheritance and
Class Table Inheritance.

#### Building the Mapping

When you map to a relational database, there are essentially three situations that you encounter:

1) You choose the schema yourself.
2) You have to map to an existing schema, which can't be changed.
3) You have to map to an existing schema, but changes to it are negotiable.

#### Using Metadata

Metadata Mapping is based on boiling down the mapping into a metadata file that details how columns
in the database map to fields in objects.

#### Database Connections

In most scenarios you will use a connection pool and probably it is abstracted enaugh so that even
thaugh you use "open" and "close" methods on a connection, it is actually only returned to the
connection pool.

Since connections are so tied to transactions, a good way to manage them is to tie them to a
transaction. Open a connection when you begin a transaction, and close it when you commit or roll
back.

#### Some Miscellaneous Points

Using "select  *" should generally be avoided.

It's always worth making the effort to use static SQL that can be precompiled, rather than dynamic
SQL that has to be compiled each time. Most platforms give you a mechanism for precompiling SQL. A
good rule of thumb is to avoid using string concatenation to put together SQL queries.

Many environments give you the ability to batch multiple SQL queries into a single database call. I
haven't done that for these examples, but it's certainly a tactic you should use in production code.
How you do it varies with the platform.

### Web Presentation

...