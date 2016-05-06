#Best practices

Best practices center around different scopes:
- Performance
- Readablility
- Code Qualility

## Performance

The following best practices are ordered by importance.

### Try to solve a problem in SQL first

The by far most important best practice is to master SQL as good as possible. Most performance problems derive from poor performing SQL, not PL/SQl.

Solving problems in SQL does not mean to write complicated SQL rather than simple PL/SQL stored procedures, but to elaborate on SQL and utilize it's power and versatility. In most of the cases, if not any, it's easier, shorter and faster to have the database collect the data you require than to do it yourself.

Another point to remember is: Tell the database as precisely and completly as you can which data you need. It's by far better to write a Top-N-Analysis with an analytic function or the new Row Limiting Clause in version 12c than to simply order your data and only fetch the first N rows and discard the cursor. The more the database knows about the data you want, the more the database will do for you.

Put the other way round, it reads: The less you tell the database about the data you require, the more you're on your own in regard to performance tuning. If you dig into it, you won't believe how smart the optimizer is in supporting you, should you give it a chance.

### Really know your SQL

This is close to the first recommendation but this is to stress it's importance.

Stay informed about the newest development in SQL such as Multi Table Insert, Analytic Functions, Row Pattern Matching or the other possibilities. To write elegant and good performing SQL is the basis for all PL/SQL enhancements that follow. Digging into SQL is not a one time process but requires you to stay informed about latest improvements of new database versions, reading best practices from other SQL developers and getting to know nice SQL tricks that simplify your life.

### Control environment swtches

This is a very important recommendation. Since database version 9, SQL and PL/SQL run in different environments. If you write a loop in PL/SQL and insert data into a table within that loop, the database must switch between the SQL and the PL/SQL environment. This is a tremendous cost factor you should try to avoid. There are basically two technologies to avoid unnecessary environment switches:

- In PL/SQL you want to use bulk operations (FORALL, BULK COLLECT) to reduce environment switches
- In SQL you want to include PL/SQL function calls in scalar subqueries, if possible [` ... where ename = (select v('P10_ENAME') from dual)`] to reduce the number of environment switches
- If at all possible, you want to avoid row triggers completely

### Use bind variables

If you create a SQL statement outside the database or dynamically inside PL/SQL, you want to assure that you don't pipe together elements of the query that could be passed in by using bind variables as well. If you have to use dynamic SQL, you can't pass column or table names as bind variables, but you can use bind variables for search criteria and the like. 

Make sure you do. Reason is that any select or DML statement is stored in the Library Cache after it has been parsed to avoid unnecessary parses (which may last as long as the execution of the statement). In order to recognize a statement, a hash code is computed over the statetement text. If the text of the statement changes, it creates a different hash code and won't be recognized again. This not only leads to unnecessary parsing but wipes other reusable statements off the Library Cache, harming performance of other statements as well.

### Let the database help you

PL/SQL has emerged from a simple macro language to a sophisticated, data oriented programming language. Version 11g brought an all new optimizer with many options you should utilize in your code. Let the database help you means: let the compiler help you optimize your code. This can be achieved by switching the PLSQL_OPTIMIZE_LEVEL flag to it's maximum value (which is 3 in Version 12c). 

Doing so, the optimizer inlines helper methods into the code, removing unnecessary method calls, converts For Record in Cursor loops to bulkö-optimized loops, restructures your code to remove unnecessary variable assignments and many more. The best is that you don'r need to be aware of all the optimizations, it "just happens"!

### Tell the database about your code

There are numerous possibilities to give the compiler extra information about the code you write. A function might be deterministic. If so, tell it. In prior versions, the deterministic clause had no big effect, but in the meantime it has. Therefore your code will benefit from these improvements without you having to do anything. The same will hold true for the new pragma UDF in version 12c, telling the database that you wrote a function that will be called from SQL most of the times. Telling the database about your code opens a possibility for the database to help you get maximum performance.

### Caching

There are new possibilities for caching select statements with the Result Cache or PL/SQL results with the Function Cache. Although being an Enterprise Edition option for now, if you have access to it, use it. Other "Caching options" may include Function Based Indexes over deterministic functions or scalar subqueries, allowing SQL to only compute a function once per select statement.

### Native Compilation

Since version 9 it was possible to have the database compile your PL/SQL code in native C-code. It has been possible, but it wasn't easy. Starting with version 11, it's easy as well. The only thing you need to do is set session parameter PLSQL_OPTIMIZE_LEVEL to 2 at least (which should have happened after the last best practice anyway) and PLSQL_CODE_TYPE to NATIVE. Plus, there is a script to have the database recompile all internal packages natively. Ask your favourite DBA to help you on that.

### Review your Code

There are some instruments that help you to create efficient and good performing code. Look into the Hierarchical Profiler to gather an understanding of what is going on in your code, review execution plans by tracing them with SQLTrace, review code quality by collecting metadata about your code with PL/Scope. Finally, switch compiler warning on and review what they have to tell you. Remove unneeded variables, unreachable code etc.

Don't wait for performance problems to arise but code pro-actively with these best practices in mind. Ask yourself, if the code you write is short, precise and convincing. If it is not, it's almost certain sub optimal. If your procedures grow in length and complexity, re-think whether you have the chance to solve the problem from a different point of view. Sometimes it may turn out that you should adjust your data model, sometimes you may be able to prepare portions of the data with SQL. I recall having thrown away 600 lines of XML-DOM code in PL/SQL by replacing it with a five line UPDATEXML-statement. Look out for these opportunities

### Take time to refactor

If code grows older and older, chances are that there are improvements possible which could not have been taken into account in the days the code was created. In version 12 you may have heared already about the possibility of accessing sequences with Identity Columns or enhanced Default value possibilities without being forced into row triggers. If you can rely that your software runs on 12c, refactor unneeded row triggers away!

The same holds true for enhancements in SQL. Here are some possible candidates: LISTAGG in Version 11.2, Multi Table Insert, RETURNING clause, LOG ERRORS claue, analytical functions, SQL/XML, XQuery support etc. Being informed about all these possibilities is only half the duty, the other half is to refactor this into the existing code. In PL/SQL, you want to have a look into features like object orientation, CASE statements and otehrs.
