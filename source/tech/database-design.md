## About database-design

Database design is the process of specifying the logical and/or physical parts of a database. The goal of database design is to make a representation of some "universe of discourse" - the types of facts, business rules and other requirements that the database is intended to model.

The result of [database design](http://en.wikipedia.org/wiki/Database_design) is a plan for the construction of a database as a model of the Universe of Discourse (the "business domain" or subject area about which information will be recorded in the database).

Most databases that capture and manage semi-permanent data operate under the control of a database management system (DBMS). Prominent DBMS products are Microsoft's SQL Server, Oracle DBMS, and IBM's DB2\. There are dozens of others. Many of the questions and answers you’ll find under this tag relate to one of these DBMS products, but some design issues are DBMS independent.

The amount of preparation and education you’ll need before building your first successful database varies widely depending on many factors. Among other factors, it depends on how ambitious your database project is and on what prior experience you bring to bear on the project. Very experienced programmers sometimes underestimate the amount of material there is to learn about database design.

Sometimes programmers learn well by trial and error, or by postponing formal learning until their second or third project. Other times, database design neophytes make design decisions that lead into pitfalls that are very difficult to reverse.

There are many ways to measure the quality of a database design. Programmers building their first database are often primarily concerned with performance. There’s no question that performance is important. A bad design can easily result in database operations that take ten to a hundred times as much time as they should.

But don’t let performance issues blind you to other aspects of good design. In particular, future proofing of a database is enormously important. Failure to do this can result in a database that traps its users at the first level and prevents their data from evolving as their needs evolve.

Another aspect involves separating out the hidden features of a database (sometimes called physical design) from the public features visible across the application interface (sometimes called logical design). A neat separation of these features can result a database that can be tweaked and tuned quite a bit with no changes to application code. A poor separation of these features can result in a database that makes a nightmare out of application development or database administration.

Another consideration is whether the proposed database will be embedded within a single application, or whether it will be an information hub that serves the needs of multiple applications. Some design decisions will be made very differently in these two cases.

Yet another consideration is whether the application is going to perform all data management functions on behalf of its clients, or whether custodial responsibility for the database and its data is going to be vested in one or more DBAs (database administrators).

* * *

**What kinds of questions will appear in the database-design tag?**

You'll see a lot of questions about table design, data normalization, index design, query optimization, constraint declarations, and keys. A lot of questions, and many of the responses will address issues of speed or performance. There will be a lot of questions about key selection.

Most of the questions are about relational databases, including the SQL databases that are commonly called relational. A few questions are about "truly relational" databases or about "non-relational" or "post-relational" databases. A few are about semistructured or unstructured data.

A lot of questions tagged "database design" will also be tagged "data modeling". There is a huge overlap between the two subjects.

You'll see a lot of questions on the subject of table composition and decomposition. Closely related to table decomposition is the concept of data normalization. Indeed, many responders treat table decomposition and data normalization as though they are synonymous terms. They aren't quite synonymous. Nearly all improvements in data normalization result in table decomposition, but there are plenty of ways of decomposing tables that have nothing to do with normalization.

Data normalization is a brand new topic to many neophyte database designers. It's worth learning the rudiments of data normalization, even if the database you are building is small and simple. It's also sometimes worthwhile to disregard the rules of data normalization, but you really have to know what you are doing.

You'll also see a lot of questions on the subject of index design. Closely related to index design is query optimization. Many questions about either index design or query design have to do with how much effort the programmer should expend in getting the very best result out of the optimizer.

Three things are worth keeping in mind. First, optimization is often a matter of tradeoffs. Sometimes organizing things for rapid query will slow down data updates. Sometimes speed really matters in some database operations, but not others.

Second, you really need to pay attention to those things that slow operations down from seconds to minutes, or from minutes to hours, before you worry about 10% improvements.

Third, database delays vary enormously as the volume of data increases and as the number of concurrent users increases. Simple tests with one user and sample data can really mislead you about speed in a production environment.

* * *

What are common database development mistakes?

**1\. Not using appropriate indices**

This is a relatively easy one, but it still it happens all the time. Foreign keys should have indexes on them. If you're using a field in a `WHERE` you should (probably) have an index on it. Such indexes should often cover multiple columns based on the queries you need to execute.

**2\. Not enforcing referential integrity**

Your database may vary here, but if your database supports referential integrity - meaning that all foreign keys are guaranteed to point to an entity that exists - you should be using it.

It's quite common to see this failure on MySQL databases.

More here:

*   _[How important are constraints like NOT NULL and FOREIGN KEY if I'll always control my database input with PHP?](http://stackoverflow.com/questions/382309)_
*   _[Are foreign keys really necessary in a database design?](http://stackoverflow.com/questions/18717)_

**3\. Missing or inappropriately chosen keys**

Keys are a fundamental part of database design and data integrity. Poorly chosen keys, failure to implement keys or misunderstandings about keys are very common problems and topics of discussion. The relative merits and use of surrogate and natural keys is just one aspect. Surrogate and natural keys can both be useful tools when applied correctly but they have distinct features and functions and one key cannot necessarily be regarded as a substitute for or alternative to another. Some relevant questions and answers are:

*   _[How do you like your primary keys?](http://stackoverflow.com/questions/404040)_
*   _[What's the best practice for primary keys in tables?](http://stackoverflow.com/questions/337503)_
*   _[Use item specific prefixes and autonumber for primary keys?](http://stackoverflow.com/questions/506164)_
*   _[Surrogate vs. natural/business keys](http://stackoverflow.com/questions/63090)_
*   _[Should I have a dedicated primary key field?](http://stackoverflow.com/questions/166750)_

**4\. Writing queries that require `DISTINCT` to work**

You often see this in ORM-generated queries. Look at the log output from [Hibernate](http://en.wikipedia.org/wiki/Hibernate_%28Java%29) and you'll see all the queries begin with:

    SELECT DISTINCT ...

This is a bit of a shortcut to ensuring you don't return duplicate rows and thus get duplicate objects. You'll sometimes see people doing this as well. If you see it too much it's a real red flag. Not that `DISTINCT` is bad or doesn't have valid applications. It does (on both counts), but it's not a surrogate or a stopgap for writing correct queries.

From [Why I Hate DISTINCT](http://weblogs.sqlteam.com/markc/archive/2008/11/11/60752.aspx):

> Where things start to go sour in my opinion is when a developer is building substantial query, joining tables together, and all of a sudden he realizes that it **looks** like he is getting duplicate (or even more) rows and his immediate response... his "solution" to this "problem" is to throw on the DISTINCT keyword and **POOF** all his troubles go away.

**5\. Favoring aggregation over joins**

Another common mistake is to not realize how much more expensive aggregation (that is, the `GROUP BY` clause) can be compared to joins.

For example:

From [SQL statement - “join” vs “group by and having”](http://stackoverflow.com/questions/477006):

> First query:
> 
>     SELECT userid
>     FROM userrole
>     WHERE roleid IN (1, 2, 3)
>     GROUP by userid
>     HAVING COUNT(1) = 3
>     
> 
> Query time: 0.312 s
> 
> Second query:
> 
>     SELECT t1.userid
>     FROM userrole t1
>     JOIN userrole t2 ON t1.userid = t2.userid AND t2.roleid = 2
>     JOIN userrole t3 ON t2.userid = t3.userid AND t3.roleid = 3
>     AND t1.roleid = 1
>     
> 
> Query time: 0.016 s
> 
> That's right. The join version I proposed is **twenty times faster than the aggregate version.**

**6\. Not simplifying complex queries through views**

Not all database vendors support views but for those that do, they can greatly simplify queries if used judiciously. As an example, consider a [generic Party model](http://www.tdan.com/view-articles/5014/) for CRM. This is an extremely powerful and flexible modeling technique, but it can lead to many joins. In this model there were:

*   **Party**: people and organizations;
*   **Party Role**: things those parties did, for example Employee and Employer;
*   **Party Role Relationship**: how those roles related to each other.

Example:

*   Ted is a Person, being a subtype of Party;
*   Ted has many roles, one of which is Employee;
*   Intel is an organization, being a subtype of a Party;
*   Intel has many roles, one of which is Employer;
*   Intel employs Ted, meaning there is a relationship between their respective roles.

So there are five tables joined to link Ted to his employer. We assume all employees are Persons (not organizations) and provide this helper view:

    CREATE VIEW vw_employee AS
    SELECT p.title, p.given_names, p.surname, p.date_of_birth, p2.party_name employer_name
    FROM person p
    JOIN party py ON py.id = p.id
    JOIN party_role child ON p.id = child.party_id
    JOIN party_role_relationship prr ON child.id = prr.child_id AND prr.type = 'EMPLOYMENT'
    JOIN party_role parent ON parent.id = prr.parent_id = parent.id
    JOIN party p2 ON parent.party_id = p2.id

And suddenly we have a very simple view of the data you want, but on a highly flexible data model.

**7\. Not sanitizing input**

This is a huge one. If you don't know what you're doing it's really easy to create sites vulnerable to attack. Nothing sums it up better than the [story of little Bobby Tables](http://xkcd.com/327/).

Data provided by the user by way of URLs, form data **and cookies** should always be treated as hostile and be sanitized. Make sure you're getting what you expect.

**8\. Not using prepared statements**

Prepared statements are when you compile a query minus the data used in inserts, updates and `WHERE` clauses and then supply that later. For example:

    SELECT * FROM users WHERE username = 'bob'

vs

    SELECT * FROM users WHERE username = ?

or

    SELECT * FROM users WHERE username = :username

depending on your platform.

Basically, each time any modern database encounters a new query it has to compile it. If it encounters a query it's seen before, you're giving the database the opportunity to cache the compiled query and the execution plan. By doing the query a lot you're giving the database the opportunity to figure that out and optimize accordingly (for example, by pinning the compiled query in memory).

Using prepared statements will also give you meaningful statistics about how often certain queries are used.

Prepared statements will also better protect you against [SQL injection](http://en.wikipedia.org/wiki/SQL_injection) attacks.

**9\. Not normalizing enough**

[Database normalization](http://en.wikipedia.org/wiki/Database_normalization) is the process of optimizing database design, or how you organize your data into tables.

As an example, consider code where someone implodes an array and inserts it into a single field in a database. Normalizing this would be to treat each element of the array as a separate row in a child table (that is, a one-to-many relationship).

This came up in _[Best method for storing a list of user IDs](http://stackoverflow.com/questions/620645)_:

> I've seen in other systems that the list is stored in a serialized PHP array.

But lack of normalization comes in many forms.

More:

*   _[Normalization: How far is far enough?](http://articles.techrepublic.com.com/5100-10878_11-6140413.html)_
*   _[SQL by Design: Why You Need Database Normalization](http://www.sqlmag.com/Article/ArticleID/4887/sql_server_4887.html)_

**10\. Normalizing too much**

This may seem like a contradiction to the previous point but normalization, like many things, is a tool. It is a means to an end and not an end in and of itself. Many developers forget this and start treating a "means" as an "end". [Unit testing](http://en.wikipedia.org/wiki/Unit_testing) is a prime example of this.

Careful and considered de-normalization can have huge performance benefits, but you have to be really careful when doing this.

More:

*   _[Why too much Database Normalization can be a Bad Thing](http://www.selikoff.net/blog/2008/11/19/why-too-much-database-normalization-can-be-a-bad-thing/)_
*   _[How far to take normalization in database design?](http://stackoverflow.com/questions/496508)_
*   _[When Not to Normalize your SQL Database](http://www.25hoursaday.com/weblog/CommentView.aspx?guid=cc0e740c-a828-4b9d-b244-4ee96e2fad4b)_
*   _[Maybe Normalizing Isn't Normal](http://www.codinghorror.com/blog/archives/001152.html)_
*   _[The Mother of All Database Normalization Debates on Coding Horror](http://highscalability.com/mother-all-database-normalization-debates-coding-horror)_

The trouble with following advice to "denormalize" is that it doesn't tell you what to do. It's like trying to get to [Los Angeles](https://en.wikipedia.org/wiki/Los_Angeles) by driving away from [Chicago](http://en.wikipedia.org/wiki/Chicago). You could end up almost anywhere. A better plan is to find another design discipline that will function as an alternative to normalization, with different design goals. One such alternative is [star schema](http://en.wikipedia.org/wiki/Star_schema) design. Star schema design is widely used in [data warehousing](https://en.wikipedia.org/wiki/Data_warehouse) and reporting databases, where speed and ease of querying outweighs simplicity of update. There is another alternative, called [snowflake design](http://en.wikipedia.org/wiki/Snowflake_schema) which looks sort of like a compromise between star schema and normalized design.

**11\. Using exclusive arcs**

An exclusive arc is a common mistake where a table is created with two or more foreign keys where one and only one of them can be non-null. For one thing, it becomes that much harder to maintain data integrity. After all, even with referential integrity, nothing is preventing two or more of these foreign keys from being set (complex check constraints notwithstanding).

**12\. Not doing performance analysis on queries at all**

Pragmatism reigns supreme, particularly in the database world. If you're sticking to principles to the point that they've become a dogma then you've quite probably made mistakes. Take the example of the aggregate queries from above. The aggregate version might look "nice", but its performance is woeful. A performance comparison should've ended the debate (but it didn't), but more to the point: spouting such ill-informed views in the first place is ignorant, even dangerous.

**13\. Over-reliance on UNION ALL and particularly UNION constructs**

A UNION in SQL terms merely concatenates congruent data sets, meaning they have the same type and number of columns. The difference between them is that UNION ALL is a simple concatenation and should be preferred wherever possible whereas a UNION will implicitly do a DISTINCT to remove duplicate tuples.

UNIONs, like DISTINCT, have their place. There are valid applications. But if you find yourself doing a lot of them, particularly in sub-queries, then you're probably doing something wrong. That might be a case of poor query construction or a poorly designed data model forcing you to do such things.

UNIONs, particularly when used in joins or dependent sub-queries, can cripple a database. Try to avoid them whenever possible.

**14\. Using OR conditions in queries**

This might seem harmless. After all, ANDs are OK. OR should be OK too right? Wrong. Basically, an AND condition **restricts** the data set, whereas an OR condition **grows** it, but not in a way that lends itself to optimization. Particularly when the different OR conditions might intersect thus forcing the optimizer to effectively to a DISTINCT operation on the result.

Bad:

    ... WHERE a = 2 OR a = 5 OR a = 11

Better:

    ... WHERE a IN (2, 5, 11)

Now your SQL optimizer may effectively turn the first query into the second. But it might not. Just don't do it.

**15\. Not designing their data model to lend itself to high-performing solutions**

This is a hard point to quantify. It is typically observed by its effect. If you find yourself writing complicated queries for relatively simple tasks or that queries for finding out relatively straightforward information are not efficient, then you probably have a poor data model.

In some ways this point summarizes all the earlier ones, but it's more of a cautionary tale that doing things like query optimization is often done first when it should be done second. First and foremost you should ensure you have a good data model before trying to optimize the performance. As [Donald Knuth](http://en.wikipedia.org/wiki/Donald_Knuth) said:

> Premature optimization is the root of all evil

**16\. Incorrect use of Database Transactions**

All data changes for a specific process should be atomic. That is, if the operation succeeds, it does so fully. If it fails, the data is left unchanged. There should be no possibility of 'half-done' changes.

Ideally, the simplest way to achieve this is that the entire system design should strive to support all data changes through single INSERT/UPDATE/DELETE statements. In this case, no special transaction handling is needed, as your database engine should do so automatically.

However, if any processes do require multiple statements be performed as a unit to keep the data in a consistent state, then appropriate transaction control is necessary.

*   Begin a transaction before the first statement.
*   Commit the transaction after the last statement.
*   On any error, rollback the transaction. And very newbie! Don't forget to skip/abort all statements that follow after the error.

Also pay careful attention to the subtleties of how your database connectivity layer, and database engine interact in this regard.

**17\. Not understanding the 'set-based' paradigm**

The SQL language follows a specific paradigm suited to specific kinds of problems. Various vendor-specific extensions notwithstanding, the language struggles to deal with problems that are trivial in languages like Java, C#, [Delphi](http://en.wikipedia.org/wiki/Embarcadero_Delphi), etc.

This lack of understanding manifests itself in a few ways.

*   Inappropriately imposing too much procedural or imperative logic on the database.
*   Inappropriate or excessive use of [cursors](http://en.wikipedia.org/wiki/Cursor_%28databases%29). Especially when a single query would suffice.
*   Incorrectly assuming that triggers fire once per row affected in multi-row updates.

Determine clear division of responsibility, and strive to use the appropriate tool to solve each problem.