# Patterns of Enterprise Application Architecture by Martin Fowler

## Conclusions

[comment]: <> (TODO)

## Main topics/keywords

[comment]: <> (TODO add maine keyword discussed in the book too be able to check if you understood enaugh about each)

....

- ... to add main topics/keywords from first chapters
- Data Source Architectural Patterns
    - Table Data Gateway
    - Row Data Gateway
    - Active Record
    - Data Mapper
    - Lazy Loading
    - Separated Interface Pattern
    - Layer Superype
- Object-Relational Behavioral Patterns
    - Unit of Work
    - Unit of Work caller registration
    - Unit of Work object registration
    - Unit of Work controller
    - Identity Map
    - Lazy Load
    - Lazy Load by Lazy Initialization
    - Lazy Load by Virtual Proxy
    - Lazy Load by Value Holder
    - Lazy Load by Ghost
- Object-Relational Structural Patterns
    - Identity Field
    - Foreign Key Mapping
    - Association Table Mapping
    - Dependent Mapping
    - Embedded Value
    - Serialized LOB
    - Single Table Inheritance
    - Single Table Inheritance
    - Class Table Inheritance
    - Concrete Table Inheritance
    - Inheritance Mappers
- Object-Relational Metadata Mapping Patterns
    - Metadata Mapping
    - Query Object
    - Repository
- Web Presentation Patterns
    - Model View Controller
    - Page Controller
    - Front Controller
    - Template View
    - Transform View
    - Two Step View
    - Application Controller
- Distribution Patterns
    - Remote Facade
    - Data Transfer Object
- ...

## Notes

Have to keep in mind that this book was written in 2002. Struts was a popular java framework at the
moment this book was written.

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

Model View Controller.

#### View Patterns

1) Template View
2) Transform View
3) Two Step View

Transform View and Template View are single stage. Two Step View is a varation that can be applied
to either

Template View allows you to write the presentation in the structure of the page and embed markers
into the page to indicate where dynamic content needs to go. Quite a few popular platforms are based
on this pattern, many of which are the server pages technologies (ASP, JSP, PHP) that allow you to
put a full programming language into the page. This clearly provides a lot of power and flexibility;
sadly, it also leads to very messy code that's difficult to maintain.

The Transform View uses a transform style of program. The usual example is XSLT. This can be very
effective if you're working with domain data that's in XML format or can easily be converted to it.
An input controller picks the appropriate XSLT stylesheet and applies it to XML gleaned from the
model.

#### Input Controller Patterns

There are two patterns for the input controller.

The most common is an input controller object for every page on your Website. In the simplest case
this Page Controller can be a server page itself, combining the roles of view and input controller.

Front Controller goes further into separating between the responsibility of handling the Http
request and deciding what to do with it by having only one object handling all requests. This single
handler interprets the URL to figure out what kind of request it is dealing with and creates a
separate object to process it.

### Concurrency

#### Concurrency Problems

#### Execution Contexts

#### Isolation and immutability

The problems of concurrency have been around for a while, and software people have come up with
various solutions. For enterprise applications two solutions are particularly important: isolation
and immutability.

#### Optimistic and Pessimistic Concurrency Control

What happens when we have mutable data that we can't isolate? In broad terms there are two forms of
concurrency control that we can use: optimistic and pessimistic.

A good way of thinking about this is that an optimistic lock is about conflict detection while a
pessimistic lock is about conflict prevention. As it turns out real source code control systems can
use either type, although these days most source code developers prefer to work with optimistic
locks. (There is a reasonable argument that says that optimistic locking isn't really locking, but
we find the terminology too convenient, and widespread, to ignore.)

#### Transactions

ACID. SQL Isolation levels.

#### Patterns for Offline Concurrency Control

Our first choice for handling offline concurrency problems is Optimistic Offline Lock, which
essentially uses optimistic concurrency control across the business transactions.

The limitation of Optimistic Offline Lock is that you only find out that a business transaction is
going to fail when you try to commit it, and in some circumstances the pain of that late discovery
is too much. Users may have put an hour's work into entering details about a lease, and if you get
lots of failures users lose faith in the system.

As an alternative we have Pessimistic Offline Lock, with which you find out early if you're in
trouble but lose out because it's harder to program and it reduces your liveness.

With either of these approaches you can save considerable complexity by not trying to manage locks
on every object. A Coarse-Grained Lock allows you to manage the concurrency of a group of objects
together. Another way you can make life easier for application developers is to use Implicit Lock ,
which saves them from having to manage locks directly. Not only does this save work, it also avoids
bugs when people forget—and these bugs are hard to find.

#### Application Server Concurrency

### Session State

#### The Value of Statelessness

#### Session State

#### Ways to Store Session State

Client Session State stores the data on the client. There are several ways to do this: encoding data
in a URL for a Web presentation, using cookies, serializing the data into some hidden field on a Web
form, and holding the data in objects on a rich client.

Server Session State may be as simple as holding the data in memory between requests. Usually,
however, there's a mechanism for storing the session state somewhere more durable as a serialized
object. The object can be stored on the application server's local file system, or it can be placed
in a shared data source. This could be a simple database table with a session ID as a key and a
serialized object as a value.

Session State is also server-side storage, but it involves breaking up the data into tables and
fields and storing it in the database much as you would store more lasting data.

### Distribution Strategies

#### The Allure of Distributed Objects

#### Remote and Local Interfaces

First Law of Distributed Object Design: Don't distribute your objects!

How, then, do you effectively use multiple processors? In most cases the way to go is clustering.
Put all the classes into a single process and then run multiple copies of that process on the
various nodes. That way each process uses local calls to get the job done and thus does things
faster. You can also use fine-grained interfaces for all the classes within the process and thus get
better maintainability with a simpler programming model.

#### Where You Have to Distribute

#### Working with Distribution Boundary

Using a Remote Facade helps minimize the difficulties that the coarse-grained interface introduces.
This way only the objects that really need a remote service get the coarse-grained method, and it's
obvious to the developers that they're paying that cost. Transparency has its virtues, but you don't
want to be transparent about a potential remote call.

By keeping the coarse-grained interfaces as mere facades, however, you allow people to use the
fine-grained objects whenever they know they are running in the same process. This makes the whole
distribution policy much more explicit. Hand in hand with Remote Facade is Data Transfer Object. Not
only do you need coarse-grained methods, you also need to transfer coarse-grained objects. When you
ask for an address, you need to send that information in one block. You usually can't send the
domain object itself, because it's tied in a Web of fine-grained local inter-object references. So
you take all the data that the client needs and bundle it in a particular object for the
transfer—hence the term Data Transfer Object. (Many people in the enterprise Java community use the
term value object for this, but this causes a clash with other meanings of the term Value Object).
The Data Transfer Object appears on both sides of the wire, so it's important that it not reference
anything that isn't shared over the wire. This boils down to the fact that a Data Transfer Object
usually only references other Data Transfer Objects and fundamental objects such as strings.

Another route to distribution is to have a broker that migrates objects between processes. The idea
here is to use a Lazy Load scheme where, instead of lazy reading from a database, you move objects
across the wire. The hard part of this is ensuring that you don't end up with lots of remote calls.
I haven't seen anyone try this in an application, but some O/R mapping tools (e.g., TOPLink) have
this facility, and I've heard some good reports about it

### Putting It All Together

Technical practices to consider: continuous integration, test driven development and refactoring.

#### Starting with the Domain Layer

The start of the process is deciding which domain logic approach to go with. The three main
contenders are Transaction Script, Table Module, and Domain Model.

As indicated previously, the strongest force that drives you through this trio is the complexity of
the domain logic, something currently impossible to quantify, or even qualify, with any degree of
precision. But other factors also play in the decision, in particular, the difficulty of the
connection with a database.

#### Down to the Data Source Layer

##### Data Source for Transaction Script

The database patterns to choose from here are Row Data Gateway and Table Data Gateway. With a Row
Data Gateway each record is read into an object with a clear and explicit interface. With Table Data
Gateway you may have less code to write since you don't need all the accessor code to get at the
data, but you end up with a much more implicit interface that relies on accessing a record set
structure that's little more than a glorified map.

##### Data Source Table Module

The main reason to choose Table Module is the presence of a good Record Set framework. In this case
you'll want a database mapping pattern that works well with Record Sets, and that leads you
inexorably toward Table Data Gateway .These two patterns fit together as if it were a match made in
heaven.

##### Data Source for Domain Model

If your Domain Model is fairly simple, say a couple of dozen classes that are pretty close to the
database, then an Active Record makes sense. If you want to decouple things a bit, you can use
either Table Data Gateway or Row Data Gateway to do that. Whether you separate or not isn't a huge
deal either way.

As things get more complicated, you'll need to consider Data Mapper. This is the approach that
delivers on the promise of keeping your Domain Model as independent as possible of all the other
layers. But Data Mapper is also the most complicated one to implement. Unless you either have a
strong team or you can find some simplifications that make the mapping easier to do, I'd strongly
suggest getting a mapping tool.

Once you choose Data Mapper most of the patterns in the O/R mapping section come into play. In
particular I heartily recommend Unit of Work (184), which acts as a focal point for concurrency
control.

##### The Presentation Layer

Your first question is whether to provide a rich-client interface or an HTML browser interface. My
preference is to pick an HTML browser if you can get away with it and a rich client if that's not
possible. Rich clients will usually take more effort to program, but that's because they tend to be
more sophisticated, not so much because of the inherent complexities of the technology.

For HTML route, MVC would be recommended. You're left now with two decisions, one for the controller
and one for the view.

Given a freer choice, I'd recommend Page Controller if your site is more document oriented,
particularly if you have a mix of static and dynamic pages. More complex navigation and UI lead you
toward a Front Controller.

On the view front the choice between Template View and Transform View depends on whether your team
uses server pages or XSLT in programming. Template Views have the edge at the moment, although I
rather like the added testability of Transform View. If you have the need to display a common site
with multiple looks and feels, you should consider Two Step View.

How you communicate with the lower layers depends on what kind of layers they are and whether
they're always going to be in the same process. My preference is to have everything run in one
process if you can—that way you don't have to worry about slow inter-process calls. If you can't do
that, you should wrap your domain layer with Remote Facade and use Data Transfer Object to
communicate to the Web server.

#### Some Techonoly-Specific Advice

##### Java and J2EE

##### .NET

##### Stored procedures

They're often the fastest way to do things since they run in the same process as your database and
thus reduce the laggardly remote calls. However, most stored procedure environments don't give you
good structuring mechanisms for your stored procedures, and stored procedures will lock you into a
particular database vendor. (Take previous statement with a pinch of salt, things have evolved
somewhat from then, but the statement still holds some truth to it)

For the reasons of modularity and portability a lot of people avoid using stored procedures for
business logic. I tend to side with that view unless there's a strong performance gain to be had,
which, to be fair, there often is. In that case I take a method from the domain layer and happily
move it into a stored procedure. I

##### Web Services

#### Other layering Schemes

"Brown model" (name did not stick) has five layers:  presentation, controller/mediator, domain, data
mapping, and data source. Essentially it places additional mediating layers between the basic three
layers. The controller/mediator mediates between the presentation and domain layers, while the data
mapping layer mediates between the domain and data source layers. I find that the mediating layers
are useful some of the time but not all of the time, so I describe them in terms of patterns. The
Application Controller is the mediator between the presentation and domain, and the Data Mapper is
the mediator between the data source and the domain.

CoreJ2EE layering pattern. Here the layers are client, presentation, business, integration, and
resource. Simple correspondences exist for the business and integration layers. The resource layer
comprises external services that the integration layer connects to. The main difference is that they
split the presentation layer between the part that runs on the client (client) and the part that
runs on a server (presentation). This is often a useful split, but again it's not one that's needed
all the time.

The Microsoft DNA architect (Kirtland) defines three layers: presentation, business, and data
access, that correspond pretty directly to the three layers I use here. The biggest shift occurs in
the way that data is passed up from the data access layers. In Microsoft DNA all the layers operate
on record sets that result from SQL queries issued by the data access layer. This introduces an
apparent coupling in that both the business and the presentation layers know about the database.

Marinescu Layering provides five layers. The presentation is split into two layers, reflecting the
separation of an Application Controller. The domain is also split, with a Service Layer built on a
Domain Model, reflecting the common idea of splitting a domain layer into two parts. This is a
common approach, reinforced by the limitations of EJB as a Domain Model.

### Domain Logic Patterns

#### Transaction Script

Organizes business logic by procedures where each procedure handles a single request from the
presentation.

With Transaction Script the domain logic is primarily organized by the transactions that you carry
out with the system. If your need is to book a hotel room, the logic to check room availability,
calculate rates, and update the database is found inside the Book Hotel Room procedure.

You can organize your Transaction Scripts into classes in two ways. The most common is to have
several Transaction Scripts in a single class, where each class defines a subject area of related
Transaction Scripts. This is straightforward and the best bet for most cases. The other way is to
have each Transaction Script in its own class, using the Command pattern [Gang of Four]. In this
case you define a supertype for your commands that specifies some execute method in which
Transaction Script logic fits. The advantage of this is that it allows you to manipulate instances
of scripts as objects at runtime, although I've rarely seen a need to do this with the kinds of
systems that use Transaction Scripts to organize domain logic.

When to Use It? The glory of Transaction Script is its simplicity. Organizing logic this way is
natural for applications with only a small amount of logic, and it involves very little overhead
either in performance or in understanding. As the business logic gets more complicated, however, it
gets progressively harder to keep it in a well-designed state. One particular problem to watch for
is its duplication between transactions. Since the whole point is to handle one transaction, any
common code tends to be duplicated.

However much of an object bigot you become, don't rule out Transaction Script. There are a lot of
simple problems out there, and a simple solution will get you up and running much faster.

#### Domain Model

Putting a Domain Model in an application involves inserting a whole layer of objects that model the
business area you're working in. You'll find objects that mimic the data in the business and objects
that capture the rules the business uses. Mostly the data and process are combined to cluster the
processes close to the data they work with.

There are two styles of Domain Model in the field. A simple Domain Model looks very much like the
database design with mostly one domain object for each database table. A rich Domain Model can look
different from the database design, with inheritance, strategies, and other [Gang of Four] patterns,
and complex webs of small interconnected objects. A rich Domain Model is better for more complex
logic, but is harder to map to the database. A simple Domain Model can use Active Record (160),
whereas a rich Domain Domain Model requires Data Mapper.

A common concern with domain logic is bloated domain objects. As you build a screen to manipulate
orders you'll notice that some of the order behavior is only needed only for it. If you put these
responsibilities on the order, the risk is that the Order class will become too big because it's
full of responsibilities that are only used in a single use case.

The problem with separating usage-specific behavior is that it can lead to duplication. Behavior
that's separated from the order is harder to find, so people tend to not see it and duplicate it
instead. Duplication can quickly lead to more complexity and inconsistency, but I've found that
bloating occurs much less frequently than predicted. If it does occur, it's relatively easy to see
and not difficult to fix. My advice is not to separate usage-specific behavior. Put it all in the
object that's the natural fit. Fix the bloating when, and if, it becomes a problem

If you have complicated and everchanging business rules involving validation, calculations, and
derivations, chances are that you'll want an object model to handle them. On the other hand, if you
have simple not-null checks and a couple of sums to calculate, a Transaction Script is a better bet.

If you're using Domain Model, my first choice for database interaction is Data Mapper. This will
help keep your Domain Model independent from the database and is the best approach to handle cases
where the Domain Model and database schema diverge.

When you use Domain Model you may want to consider Service Layer (133) to give your Domain Model a
more distinct API.

#### Table Module

A single instance that handles the business logic for all rows in a database table or view.

The primary distinction with Domain Model is that, if you have many orders, a Domain Model will have
one order object per order while a Table Module will have one object to handle all orders.

Table Module is very much based on table-oriented data, so obviously using it makes sense when
you're accessing tabular data using Record Set. It also puts that data structure very much in the
center of the code, so you also want the way you access the data structure to be fairly
straightforward.

Table Module doesn't give you the full power of objects in organizing complex logic. You can't have
direct instance-to-instance relationships, and polymorphism doesn't work well. So, for handling
complicated domain logic, a Domain Model is a better choice

If the objects in a Domain Model and the database tables are relatively similar, it may be better to
use a Domain Model that uses Active Record. Table Module works better than a combination of Domain
Model and Active Record when other parts of the application are based on a common table-oriented
data structure. That's why you don't see Table Module very much in the Java environment, although
that may change as row sets become more widely used.

#### Service Layer

Defines an application's boundary with a layer of services that establishes a set of available
operations and coordinates the application's response in each operation.

The two basic implementation variations are the domain facade approach and the operation script
approach

Identifying the operations needed on a Service Layer boundary is pretty straightforward. They're
determined by the needs of Service Layer clients, the most significant (and first) of which is
typically a user interface. Since a user interface is designed to support the use cases that actors
want to perform with an application, the starting point for identifying Service Layer operations is
the use case model and the user interface design for the application.

Disappointing as it is, many of the use cases in an enterprise application are fairly boring "
CRUD" (create, read, update, delete) use cases on domain objectscreate one of these, read a
collection of those, update this other thing. My experience is that there's almost always a
one-to-one correspondence between CRUD use cases and Service Layer operations.

When to Use It? The benefit of Service Layer is that it defines a common set of application
operations available to many kinds of clients and it coordinates an application's response in each
operation. The response may involve application logic that needs to be transacted atomically across
multiple transactional resources. Thus, in an application with more than one kind of client of its
business logic, and complex responses in its use cases involving multiple transactional resources,
it makes a lot of sense to include a Service Layer with container-managed transactions, even in an
undistributed architecture.

### Data Source Architectural Patterns

#### Table Data Gateway

An object that acts as a Gateway to a database table. One instance handles all the rows in the
table. A Table Data Gateway has a simple interface, usually consisting of several find methods to
get data from the database and update, insert, and delete methods

The trickiest thing about a Table Data Gateway is how it returns information from a query. Even a
simple find-by-ID query will return multiple data items.

When to Use It? As with Row Data Gateway the decision regarding Table Data Gateway is first whether
to use a Gateway (466) approach at all and then which one. I find that Table Data Gateway is
probably the simplest database interface pattern to use, as it maps so nicely onto a database table
or record type. It also makes a natural point to encapsulate the precise access logic of the data
source. I use it least with Domain Model because I find that Data Mapper (165) gives a better
isolation between the Domain Model and the database.

Table Data Gateway works particularly well with Table Module, where it produces a record set data
structure for the Table Module to work on. Indeed, I can't really imagine any other database-mapping
approach for Table Module.

#### Row Data Gateway

An object that acts as a Gateway to a single record in a data source. There is one instance per row.

With a Row Data Gateway you're faced with the questions of where to put the find operations that
generate this pattern. It often makes sense to have separate finder objects so that each table in a
relational database will have one finder class and one gateway class for the results

It's often hard to tell the difference between a Row Data Gateway and an Active Record. The crux of
the matter is whether there's any domain logic present; if there is, you have an Active Record. A
Row Data Gateway should contain only database access logic and no domain logic.

When to Use It? The choice of Row Data Gateway often takes two steps: first whether to use a gateway
at all and second whether to use Row Data Gateway or Table Data Gateway Use Row Data Gateway most
often when I'm using a Transaction Script. In this case it nicely factors out the database access
code and allows it to be reused easily by different Transaction Scripts. I don't use a Row Data
Gateway when I'm using a Domain Model. If the mapping is simple, Active Record does the same job
without an additional layer of code. If the mapping is complex, Data Mapper works better, as it's
better at decoupling the data structure from the domain objects because the domain objects don't
need to know the layout of the database.

If you use Transaction Script with Row Data Gateway, you may notice that you have business logic
that's repeated across multiple scripts; logic that would make sense in the Row Data Gateway. Moving
that logic will gradually turn your Row Data Gateway into an Active Record, which is often good as
it reduces duplication in the business logic.

#### Active Record

An object that wraps a row in a database table or view, encapsulates the database access, and adds
domain logic on that data.

The essence of an Active Record is a Domain Model in which the classes match very closely the record
structure of an underlying database. Each Active Record is responsible for saving and loading to the
database and also for any domain logic that acts on the data.

The Active Record class typically has methods that do the following:

- Construct an instance of the Active Record from a SQL result set row
- Construct a new instance for later insertion into the table
- Static finder methods to wrap commonly used SQL queries and return Active Record objects
- Update the database and insert into it the data in the Active Record
- Get and set the fields
- Implement some pieces of business logic

When to Use It? Active Record is a good choice for domain logic that isn't too complex, such as
creates, reads, updates, and deletes. Derivations and validations based on a single record work well
in this structure.

In an initial design for a Domain Model the main choice is between Active Record and Data Mapper.
Active Record has the primary advantage of simplicity. It's easy to build Active Records, and they
are easy to understand. Their primary problem is that they work well only if the Active Record
objects correspond directly to the database tables: an isomorphic schema. If your business logic is
complex, you'll soon want to use your object's direct relationships, collections, inheritance, and
so forth. These don't map easily onto Active Record, and adding them piecemeal gets very messy.
That's what will lead you to use Data Mapper instead. Another argument against Active Record is the
fact that it couples the object design to the database design. This makes it more difficult to
refactor either design as a project goes forward.

#### Data Mapper

A layer of Mappers that moves data between objects and a database while keeping them independent of
each other and the mapper itself. With Data Mapper the in-memory objects needn't know even that
there's a database present, they need no SQL interface code.

A query request from the client will usually lead to a graph of objects being loaded, with the
mapper designer deciding exactly how much to pull back in one go.Since objects are very
interconnected, you usually have to stop pulling dsata back at some point, otherwise you're likely
to pull back the entire database with a request. Mapping layers have techniques to teal with this
while minimizing the impact on the in-memory objects, using Lazy Load.

When to use it? The primary occasion for using Data Mapper is when you want the database schema and
the object model to evolve independently. The most common case for this is with a Domain Model.

The price, of course, is the extra layer that you don't get with Active Record(160), so the test for
using these patterns is the complexity of the business logic. If you have fairly simple business
logic, you probably won't need a Domain Model or a Data Mapper. Keep i mind however that even though
Data Mapper without Domain Model might not make sense, Domain Model without Data Mapper might, as
for example using an Active Record with a Domain Model if the business logic is quite simple.

To allow domain objects to invoke finder behavior I can use Separated Interface Pattern to separate
the finder interfaces from the mappers. I can put these finder interfaces in a separate package
that's visible to the domain layer, or, as in this case, I can put them in the domain layer itself.

### Object-Relational Behavioral Patterns

#### Unit of Work

Maintains a list of objects affected by a business transaction and coordinates the writing out of
changes and the resolution of concurrency problems.

With caller registration the user of an object has to remember to register the object with the Unit
of Work for changes. Any objects that aren't registered won't be written out on commit. Although
this allows forgetfulness to cause trouble, it does give flexibility in allowing people to make
in-memory changes that they don't want written out. Still, I would argue that it's going to cause
far more confusion than would be worthwhile. It's better to make an explicit copy for that purpose.

With object registration the onus is removed from the caller. The usual trick here is to place
registration methods in object methods. Loading an object from the database registers the object as
clean, the setting methods register the object as dirty. For this scheme to work the Unit of Work
needs either to be passed to the object or to be in a well-known place. Passing the Unit of Work
around is tedious but usually no problem to have it present in some kind of session object. Even
object registration leaves something to remember; that is, the developer of the object has to
remember to add a registration call in the right places. The consistency becomes habitual, but is
still an awkward bug when missed.

Unit of work controller is another technique,used by the TOPLink product. The Unit of Work handles
all reads from the database and registers clean objects whenever they're read. Rather than marking
objects as dirty the Unit of Work takes a copy at read time and then compares the object at commit
time. Although this adds overhead to the commit process, it allows a selective update of only those
fields that were actually changed; it also avoids registration calls in the domain objects.

Unit of Work works with any transactional resource, not just databases, so with message queues and
transaction monitors.

When to use it? The fundamental problem that Unit of Work deals with is keeping track of the various
objects you've manipulated so that you know which ones you need to consider synchronizing your
in-memory data with the database. If you're able to do all your work within a system transaction,
the only objects you need to worry about are those you alter. Although Unit of Work is generally the
best way of doing this, there are alternatives.

#### Identity Map

Ensures that each object gets loaded only once by keeping every loaded object in a map. Looks up
objects using the map when referring to them.

There are a number of implementation choices to worry about. Also, since Identity Maps interact with
concurrency management you should consider Optimistic Offline Lock.

How many? The decision varies between one map per class and one map for the whole session. A single
map for the session works only if you have database-unique keys.

If you have multiple maps, the obvious route is one map per class or per table, which works well if
your database schema and object models are the same. If they look different, it's usually easier to
base the maps on your objects rather than on your tables, as the objects shouldn't really know about
the intricacies of the mapping

Inheritance also has to be considered. It's usually easier to keep one table per inheritance three
because otherwise you have to look in multiple maps in a polymorphic case, however this means that
the keys should be made unique per each inheritance tree.

Where to put them? If you're using Unit of Work that's by far the best place for the Identity Maps
since the Unit of Work is the main place for keeping track of data coming in or out of the database.
If you don't have a Unit of Work, the best bet is a Registry.

#### Lazy Load

An object that doesn't contain all the data you need but knows how to get it. For loading data from
a database into memory it's handy to design things so that as you load an object of interest you
also load the objects that are related to it. This makes loading easier on the developer using the
object, who otherwise has to load all the objects explicitly.

There are four main ways you can implement Lazy Load: lazy initialization, virtual proxy, value
holder and ghost.

Lazy Load is a good candidate for aspect-oriented programming.

In theory, you might want a range of different degrees of laziness, but in practice you really need
only two: a complete load and enough of a load for identification purposes in a list. Adding more
usually adds more complexity than is worthwhile.

When to use it? Deciding when to use Lazy Load is all about deciding how much you want to pull back
from the database as you load an object and how many database calls that will require. It's usually
pointless to use Lazy Load on a field that's stored in the same row as the rest of the objects,
because most of the time it doesn't cost anymore to bring back extra data in a call, even if the
data field is quite large.

### Object-Relational Structural Patterns

#### Identity Field

Saves a database ID field in an object to maintain identity between an in-memory object and a
database row.

Prefer meaningless keys (some id) over meaningful keys (SSN/VIN) because you can guarantee
uniqueness, in case of human error when writing it you cannot for meaningful keys.

Prefer simple keys over compound keys because you can use the same code for all key manipulation.

A table-unique key is usually fine, but a databawse-unique key is often easier to do and allows the
usage of a single Identity Map.

In case of inheritance, if you are using Concrete Table Inheritance of Class Table Inheritance, you
should go for keys that are unique to the hierarchy rahter than unique to each table.

When to Use It? Use Identity Field when there's a mapping between objects in memory and rows in a
database. This is usually when you use Domain Model or Row Data Gateway. You don't need this mapping
if you're using Transaction Script, Table Module, or Table Data Gateway

#### Foreign Key Mapping

Maps an association between objects to a foreign key reference between tables.

When to use it? A Foreign Key can be used for almost all associations between classes. The most
common case where it isn't possible is with many-to-many associations. If you have a collection
field with no back pointer, you should consider whether the many side should be a Dependent Mapping.
if so, it can simplify your handling of the collection. If the related object is a Value Object then
you should use Embedded Value.

#### Association Table Mapping

Saves an association as a table with foreign keys to the tables that are linked by the association.

When to Use It? The canonical case for Association Table Mapping is a many-to-many association,
since there are really no any alternatives for that situation.

#### Dependent Mapping

Has one class perform the database mapping for a child class.

How It Works? The basic ide behind Dependent Mappin is that one class (the dependent) relies upon
some other class (the owner) for its database persistence. Each dependent can have only one owner
and must have one owner

For Active Record and Row Data Gateway, the dependent class won't contain any database mapping code;
its mapping code sits in the owner. With Data Mapper there's no mapper for the dependent, the
mapping code sits in the mapper for the owner. In a Table Data Gateway there will typically be no
dependent class at all, all the handling of the dependent is done in the owner.

In most cases every time you load an owner, you load the dependents too. If the dependents are
expensive to load and infrequently used, you can use a Lazy Load to avoid loading the dependents
until you need them.

An important property of a dependent is that it doesn't have an Identity Field and therefore isn't
stored in a Identity Map. It therefore cannot be loaded by a find method that looks up an ID.
Indeed, there's no finder for a dependent since all finds are done with the owner.

It's usually easier for the primary key on the database to be a composite key that includes the
owner's primary key. No other table should have a foreign key into the dependent's table, unless
that object has the same owner. As a result, no in-memory object other than the owner or its
dependents should have a reference to a dependent. Strictly speaking, you can relax that rule
providing that the reference isn't persisted to the database, but having a nonpersistent reference
is itself a good source of confusion.

If you want to update the collection of dependents you can safely delete all rows that link to the
owner and then reinsert all the dependants. This saves you from having to do an analysis of objects
added or removed from the owner's collection.

Using Dependent Mapping complicates tracking whether the owner has changed. Any changes to the
dependent need to mark the owner as changed so that the owner will write the changes to the
database.

When to Use It? You use Dependent Mapping when you have an object that's only referred to by one
other object, which usually occurs when one object has a collection of dependents. For Dependent
Mapping to work there are a number of preconditions: a dependent must have exactly one owner; there
must be no references from any object other than the owner of the dependent;

Dependent Mapping is not that recommended in the context of Unit of Work. Keeping track of thins is
not that trivial in the context of delete and reinsert strategy for Unit of Work.

#### Embedded Value

Maps an object into several fields of another object's table.

When to Use It? The simplest cases for Embedded Value are the clear, simple Value Objects like money
and date range. Since Value Objects (486) don't have identity, you can create and destroy them
easily without worrying about such things as Identity Maps (195) to keep them all in sync. Indeed,
all Value Objects (486) should be persisted as Embedded Value, since you would never want a table
for them there.

#### Serialized LOB

Saves a graph object by serializing them into a large screen object (LOB) which it stores in a
database field

The advantages of the BLOB are that it's simple to program (if your platform supports it) and that
it uses the minimum of space. The disadvantages are that your database must support a binary data
type for it and that you can't reconstruct the graph without the object, so the field is utterly
impenetrable to casual viewing. The most serious problem, however, is versioning. If you change the
department class, you may not be able to read all its previous serializations; since data can live
in the database for a long time, this is no small thing.

The alternative is a CLOB. In this case you serialize the department graph into a text string that
carries all the information you need. The text string can be read easily by a human viewing the row,
which helps in casual browsing of the database. However the text aproach will usually need more
space, and you may need to create your own parser for the textual format you use. It's also likely
to be slower than a binary serialization. Many of the disadvantages of CLOBs can be overcome with
XML.

When to Use It? Serialized LOB isn't considered as often as it might be. XML makes it much more
attractive since it yields a easy-to-implement textual approach. Its main disadvantage is that you
can't query the structure using SQL. SQL extensions appear to get at XML data within a field, but
that's still not the same (or portable).

An obvious danger here is that someone may try to edit the XML by hand in the database and mess up
the XML, making it unreadable by the load routine. More sophisticated to at would support adding a
DTD or XML schema to a field as validation will obviously help with that.

#### Single Table Inheritance

Represents an inheritance hierarchy of classes as a single table that has columns for all the fields
of the various classes.

When loading an object into memory you need to know which class to instantiate, for this you have a
field in the table that indicates which class should be used.

#### Class Table Inheritance

Represents an inheritance hierarchy of classes with one table for each class.

In order to map them in the tables, you either keep the same primary key in the superclass and the
concrete class (key 101 in footballers table and the row of key 101 in the players table correspond
to the same domain object) or you let each table have its own primarty keys and use foreign keys
into the superclass table to tie the rows together.

#### Concrete Table Inheritance

Represents an inheritance hierarchy of classes with one table per concrete class in the hierarchy.

You need to pay attention to the keys with this pattern. Punningly, the key thing is to ensure that
keys are unique not just to a table but to all the tables from a hierarchy. A classic example of
where you need this is if you have a collection of players and you're using Identity Field (216)
with table-wide keys. If keys can be duplicated between the tables that map the concrete classes,
you'll get multiple rows for a particular key value. us, you thus need a key allocation system that
keeps track of key usage across tables; also, you can't rely on the database's primary key
uniqueness mechanism.

#### Inheritance Mappers

A structure to organize database mappers that handle inheritance hierarchies.

When you map from an object-oriented inheritance hierarchy in memory to a relational database you
have to minimize the amount of code needed to save and load the data to the database. You also want
to provide both abstract and concrete mapping behavior that allows you to save or load a superclass
or a subclass.

When to Use It? This general scheme makes sense for any inheritance based database mapping. The
alternatives involve such things as duplicating superclass mapping code among the concrete mappers
and folding the player's interface into the abstract player mapper class. The former is a heinous
crime, and the latter is possible but leads to a player mapper class that's messy and confusing. On
the whole, then, its hard to think of a good alternative to this pattern.

### Object-Relational Metadata Mapping

#### Metadata Mapping

Holds details of object-relational mapping in metadata.

A Metadata Mappiong allows developers to define the mappiongs in a simple tabular form, which can
then be processed by generic code to carry out the details of reading, inserting and updating the
data.

How It Works? The biggest decision in using Metadata Mapping is how the information in the metadata
manifests itself in terms of running code. There are two main routes to take: code generation and
reflective programming.

I usually counsel against reflection, partly because it's slow but mainly because it often causes
code that's hard to debug. Even so, reflection is actually appropriate for database mapping. Since
you're reading in the names of fields and methods from a file, you're taking full advantage of
reflection's flexibility.

When to use it? Metadata Mapping can greatly reduce the amount of work needed to handle database
mapping. However, some setup work is required to prepare the Metadata Mapping framework. Also, while
it's often easy to handle most cases with Metadata Mapping, you can find exceptions that really
tangle the metadata. It's no surprise that the commercial object-relational mapping tools use
Metadata Mapping—when selling a product producing a sophisticated Metadata Mapping is always worth
the effort.

Metadata Mapping can interfere with refactoring, particularly if you're using automated tools. If
you change the name of a private field, it can break an application unexpectedly. Even automated
refactoring tools won't be able to find the field name hidden in a XML data file of a map.

#### Query Object

An object that represents a database query.

How it works? A Query Object is an application of the Interpreter pattern geared to represent a SQL
query. Its primary roles are to allow a client to form queries of various kinds and to turn thos
object structures into the appropriate SQL string.

A common feature of Query Object is that it can represent queries in the language of the in-memory
objects rather than the database schema. That means that, instead of using table and column names,
you can use object and field names. While this isn't important if your objects and database have the
same structure, it can be very useful if you get variations between the two. In order to perform
this change of view, the Query Object needs to know how the database structure maps to the object
structure, a capability that really needs Metadata Mapping.

When to Use It? Query Objects are a pretty sophisticated pattern to put together, so most projects
don't use them if they have a handbuilt data source layer. You only really need them when you're
using Domain Model and Data Mapper; you also really need Metadata Mapping to make serious use of
them. Even then Query Objects aren't always necessary, as many developers are comfortable with SQL.
You can hide many of the details of the database schema behind specific finder methods.

The advantages of Query Object come with more sophisticated needs: keeping database schemas
encapsulated, supporting multiple databases, supporting multiple schemas, and optimizing to avoid
multiple queries.

#### Repository

Mediates between the domain and data mapping layers using a collection-like interface for accessing
domain objects.

How It Works? Repository is a sophisticated pattern that makes use of a fair number of the other
patterns described in this book. In fact, it looks like a small piece of an object-oriented database
and in that way it's similar to Query Object, which development teams may be more likely to
encounter in an object-relational mapping tool than to build themselves. However, if a team has
taken the leap and built Query Object, it isn't a huge step to add a Repository capability. When
used in conjunction with Query Object, Repository adds a large measure of usability to the
object-relational mapping layer without a lot of effort.

For example, to find person objects by name we first create a criteria object, setting each
individual criterion like so: criteria.equals(Person.LAST_NAME, "Fowler"), and criteria.like(
Person.FIRST_NAME, "M"). Then we invoke repository.matching(criteria) to return a list of domain
objects representing people with the last name Fowler and a first name starting with M. Various
convenience methods similar to matching (criteria) can be defined on an abstract repository; for
example, when only one match is expected soleMatch(criteria) might return the found object rather
than a collection. Other common methods include byObjectId(id), which can be trivially implemented
using soleMatch.

Compare this with the direct use of Query Object, in which client code may construct a criteria
object (a simple example of the specification pattern), add() that directly to the Query Object, and
execute the query. With a Repository, client code constructs the criteria and then passes them to
the Repository, asking it to select those of its objects that match. From the client code's
perspective, there's no notion of query "execution"; rather there's the selection of appropriate
objects through the "satisfaction" of the query's specification. This may seem an academic
distinction, but it illustrates the declarative flavor of object interaction with Repository, which
is a large part of its conceptual power.

Under the covers, Repository combines Metadata Mapping with a Query Object to automatically generate
SQL code from the criteria

When to Use It? In a large system with many domain object types and many possible queries,
Repository reduces the amount of code needed to deal with all the querying that goes on. Repository
promotes the Specification pattern (in the form of the criteria object in the examples here), which
encapsulate s the query to be performed in a pure object-oriented way. Therefore, all the code for
setting up a query object in specific cases can be removed. Clients need never think in SQL and can
write code purely in terms of objects.

However, situations with multiple data sources are where we really see Repository coming into its
own.

### Web Presentation Patterns

#### Model View Controller

Splits user interface interaction into three distinct roles.

Model View Controller (MVC) is one of the most quoted (and most misquoted) patterns around. It
started as a framework developed by Trygve Reenskaug for the Smalltalk platform in the late 1970s.
Since then it has played an influential role in most UI frameworks and in the thinking about UI
design.

How It Works? MVC considers three roles. The model is an object that represents some information
about the domain. The view represents the display of the model in the UI. The controller takes user
input, manipulates the model, and causes the view to update appropriately. In this way UI is a
combination of the view and the controller.

In MVC there are two principal separations: separating the presentation from the model and
separating the controller from the view. Of these the separation of presentation from model is one
of the most fundamental heuristics of good software design.

When to Use It? The value of MVC lies in its two separations. Of these the separation of
presentation and model is one of the most important design principles in software, and the only time
you shouldn't follow it is in very simple systems where the model has no real behavior in it anyway.
As soon as you get some nonvisual logic you should apply the separation. Unfortunately, a lot of UI
frameworks make it difficult, and those that don't are often taught without a separation. The
separation of view and controller is less important, so I'd only recommend doing it when it is
really helpful.

#### Page Controller

An object that handles a request for a specific page or action on a Website.

How It Works? The basic idea behind a Page Controller is to have one module on the Web server act as
the controller for each page on the Website. In practice, it doesn't work out to exactly one module
per page, since you may hit a link sometimes and get a different page depending on dynamic
information. More strictly, the controllers tie in to e ch action, which may be clicking a link or a
button.

The basic responsibilities of a Page Controller are:

- Decode the URL and extract any form data to figure out all the data for the action.
- Create and invoke any model objects to process the data. All relevant data from the HTML request
  should be passed to the model so that the model objects don't need any connection to the HTML
  request.
- Determine which view should display the result page and forward the model information to it.

The Page Controller needn't be a single class but can invoke helper objects. This is particularly
useful if several handlers have to do similar tasks. A helper class can then be a good spot to put
any code that would otherwise be duplicated.

When to Use It? Page Controller works particularly well in a site where most of the controller logic
is pretty simple. In this case most URLs can be handled with a server page and the more complicated
cases with helpers. When your controller logic is simple, Front Controller adds a lot of overhead.
It's not uncommon to have a site where some requests are dealt with by Page Controllers and others
are dealt with by Front Controllers, particularly when a team is refactoring from one to another.
Actually, the two patterns mix without too much trouble.

#### Front Controller

A controller that handles all requests for a Website.

How it works? A Front Controller handles all calls for a Website, and is usually structured in two
parts: a Web handles and a command hierarchy. The Web handler is the object that actually receives
post or get requests from the Web server. It pulls just enaugh information from the URL and the
request to decide what kind of action to initiate and then delegates to a command to carry out the
action.

The Web handler itself is usually a fairly simple program that does nothing other than decide which
command to run.

The Web handler can decide which command to run either statically or dynamically. The static version
involves parsing the URL and using conditional logic; the dynamic version usually involves taking a
standard piece of the URL and using dynamic instantiation to create a command class. The static case
has the advantage of explicit logic, compile time error checking on the dispatch, and lots of
flexibility in the look of your URLs. The dynamic case allows you to add new commands without
changing the Web handler. With dynamic invocation you can put the name of the command class into the
URL or you can use a properties fie that binds URLs to command class names.

A particularly useful pattern to use in conjunction with Front Controller is Intercepting Filter,
This is essentially a decorator that wraps the handler of the front controller allowing you to build
a filter chain (or pipeline of filters) to handle issues such as authentication, logging, and locale
identification. Using filters allows you to dynamically set up the filters to use at configuration
time.

When to Use It? The Front Controller is a more complicated design than its obvious counterpart, Page
Controller. It therefore needs a few advantages to be worth the effort. Only one Front Controller
has to be configured into the Web server; the Web handler does the rest of the dispatching. There's
just one controller, so you can easily enhance its behavior at runtime with decorators You can have
decorators for authentication, character encoding, internationalization, and so forth, and add them
using a configuration file or even while the server is running

#### Template View

Renders information into HTML by embedding markers in an HTML page.

How It Works? The basic idea of Template View is to embed markers into a static HTML page when it's
written. When the page is used to service a request, the markers are replaced by the results of some
computation, such as a database query. This way the page can be laid out in the usual manner, often
with WYSIWYG editors, often by people who aren't programmers. The markers then communicate with real
programs to put in the results.

Putting a lot of scriptlets into the page makes it too easy to mingle the different layers of an
enterprise application. When domain logic starts turning up on server pages it becomes far too
difficult to structure it well and far too easy to duplicate it across different server pages. All
in all, the worst code I've seen in the last few years has been server page code.

Helper Object. The key to avoiding scriptlets is to provide a regular object as a helper to each
page. This helper has all the real programming logic. The page only has calls into it, which
simplifies the page and makes it a m Template View.

The issue comes when you want to have conditional display, you end up still having logic in the
template. They should be avoided, as they are a code smell, but that is not always possible. In
either case it's important that the condition be done based on a single Boolean property of the
helper. Putting any more complex expression into the page is actually putting the logic into the
page itself.

Another issue comes from Iterations. Iterating over a collection presents similar issues. If you
want a table where each line corresponds to a line item on an order, you need a construct that
allows easy display of information for each line. Here it's hard to avoid a general iteration over a
collection tag, but it usually works simply enough to fit in quite well.

When Template View is taking on responsabilities beyond the view it's importand to ensure that these
responsabilities are handled by the helper, not by the page.

When to Use It? For implementing the view in Model View Controller the main choice is between
Template View and Transform View. The strength of Template View is that it allows you to compose the
content of the page by looking at the page structure. This seems to be easier for most people to do
and to learn. In particular it nicely supports the idea of a graphic designer laying out a page with
a programmer working on the helper.

You need good discipline to keep the page simple and display oriented, putting logic in the helper.
The second weakness is that Template View is harder to test than Transform View. Most
implementations of Template View.

When using a JSP as a view only, it's always be invoked from a controller rather than directly from
the servlet container. Thus, it's important to pass to the JSP any information it will need to
figure out what to display. A good way to do this is to have the controller create a helper object
and pass it to the JSP using the HTTP request.

```java
<jsp:useBean id="helper"type="actionController.ArtistHelper"scope="request"/>
```

#### Transform View

A view that processes domain data element by element and transforms it into HTML.

How It Works? The basic notion of Transform View is writing a program that looks at domain-oriented
data and converts it to HTML. The program walks the structure of the domain data and, as it
recognizes each form of domain data, it writes out the particular piece of HTML for it. If you think
about this in an imperative way, you might have a method called renderCustomer that takes a customer
object and renders it into HTML. If the customer contains a lot of orders, this method loops over
the orders calling renderOrder.

The key difference between Transform View and Template View is the way in which the view is
organized. A Template View is organized around the output. A Transform View is organized around
separate transforms for each kind of input element. The transform is controlled by something like a
simple loop that looks at each input element, finds the appropriate transform for that element, and
then calls the transform on it. A typical Transform View's rules can be arranged in any order
without affecting the resulting output.

When to Use It? The choice between a Transform View and a Template View mostly comes down to which
environment the team working on the view software prefers. The presence of tools is a key factor
here. There are more and more HTML editors that you can use to write Template Views. Tools for XSLT
are, at least so far, muc less sophisticated. Also, XSLT can be an awkward language to master
because of its functional programming style coupled with its awkward XML syntax.

#### Two Step View

Turns domain data into HTML in two steps: first by forming some kind of logical page, then rendering
the logical page into HTML

How It Works? The key to this pattern is in making the transformation to HTML a two-stage process.
The first stage assembles the information in a logical screen structure that is suggestive of the
display elements yet contains no HTML. The second stage takes that presentation-oriented structure
and renders it into HTML. This intermediate form is a kind of logical screen. Its elements might
include things like fields, headers, footers, tables, choices, and the like.

There are several ways to build a Two Step View. Perhaps the easiest is with two-step XSLT.
Single-step XSLT follows the approach in Transform View, in which each page has a single XSLT style
sheet that transforms the domain-oriented XML into HTML. In the two-step approach there are two XSLT
style sheets. The first-stage style sheet transforms the domain-oriented XML into
presentation-oriented XML, the second stage style sheet renders that XML into HTML.

When to Use It? Two Step View's key value comes from the separation of first and second stages,
allowing you to make global changes more easily. It helps to think of two situations:
multiappearance Web applications and single-appearance Web applications. Multiappearance apps are
the rarer breed but a growing one. In them the same basic functionality is provided through multiple
organizations and each organization has its own distinct look.

With a single-stage view (either Template View or Transform Vie, you build one view module per Web
page. With a Two Step View you have two stages: one first-stage module per page and one second-stage
module for the entire application.

#### Application Controller

A centralized point for handling screen navigation and the flow of an application.

Some applications contain a significant amount of logic about the screens to use at different
points, which may involve invoking certain screens at certain times in an application. This is the
wizard style of interaction, where the user is led through a series of screens in a certain order.
In other cases we may see screens that are only brought in under certain conditions, or choices
between different screens that depend on earlier input.

How It Works? An Application Controller has two main responsibilities: deciding which domain logic
to run and deciding the view with which display the response. To do this it typically holds two
structured collections of class references, one for domain commands to execute against in the domain
layer and one of views.

For both the domain commands and the view, the application controller needs a way to store something
it can invoke. A Command, from the Command Pattern, is a good choice, since it allows it to easily
get hold of and run a block of code. Languages that can manipulate functions can hold references to
them. Another option is to hold a string that can be used to invoke a method by reflection. The
domain commands can be command objects that are part of the Application Controller layer, or they
can be references to a Transaction Script or domain object methods in the domain layer.

When to Use It? If the flow and navigation of your application are simple enough so that anyone can
visit any screen in pretty much any order, there's little value in a Application Controller. The
strength of an Application Controller comes from definite rules about the order in which pages
should be visited and different views depending on the state of objects. A good signal to use an
Application Controller is if you find yourself having to make similar changes in lots of different
places when your application's flow changes.

### Distribution Patterns

#### Remote Facade

....


