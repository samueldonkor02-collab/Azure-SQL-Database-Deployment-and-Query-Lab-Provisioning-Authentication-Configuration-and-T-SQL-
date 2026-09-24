# Azure-SQL-Database-Deployment-and-Query-Lab-Provisioning-Authentication-Configuration-and-T-SQL-
Hands on lab provisioning an Azure SQL Database (PaaS), configuring Microsoft Entra only authentication, and querying the AdventureWorksLT sample schema with T SQL in the Azure portal.

# Azure SQL Database Deployment and Query Lab (Provisioning, Authentication Configuration and T SQL Querying with AdventureWorksLT Sample Data)

`Microsoft Azure` · `Azure SQL Database` · `Query Editor` · `T SQL` · `Microsoft Entra ID` · `AdventureWorksLT Sample Schema`

## Overview
This lab was hands on practice with provisioning a cloud hosted relational database in Azure and then working with it directly in the browser. I created a new SQL Database Server and database from scratch, walked through the authentication options available when setting one up, confirmed the deployment succeeded, and then used the built in Query editor to explore the sample schema and run queries against it.

The goal was less about writing complicated SQL and more about getting comfortable with the full path from "nothing exists yet" to "I have a working database I can query," including the decisions Azure asks you to make along the way, like which authentication model to use.

## Objective
Get comfortable creating a SQL Database Server and database in the Azure portal, understand the tradeoffs between the available authentication methods, confirm a deployment actually completed, and practice reading and querying an unfamiliar schema using T SQL.

## Environment
- **Cloud platform:** Microsoft Azure Portal
- **Subscription:** Azure subscription 1 (subscription ID `d8cff6ab 3ad6 4c4a 86ef 140fe24bb1cf`)
- **Resource group:** `data analysis`
- **Server:** `mysqlserver24.database.windows.net`, region West US
- **Database:** `mydatabase24`
- **Pricing tier:** Free, General Purpose, Serverless, Gen5, 2 vCores
- **Sample data:** AdventureWorksLT style schema, using the `SalesLT` schema alongside the default `dbo` schema

## What I Did

### Creating the SQL Database Server
1. On the Create SQL Database Server page, entered the server name `mysqlserver24` and set the location to West US.
2. Reviewed the authentication method options before continuing:
   - Use Microsoft Entra only authentication
   - Use both SQL and Microsoft Entra authentication
   - Use SQL authentication
3. Selected **Microsoft Entra only authentication**, since relying on Entra identities instead of a standalone SQL login is generally the more secure default when it is available.

### Confirming the Deployment
1. After submitting the server and database configuration, checked the deployment overview page to confirm the status read "Your deployment is complete" rather than assuming it worked.
2. Noted the deployment name (`Microsoft.SQLDatabase.newDatabaseNewServer`), the resource group (`data analysis`), and the correlation ID, since those are useful if I ever need to trace the deployment back through Azure's activity log.

### Reviewing the Database Overview
1. Opened the new database's Overview page and confirmed the status was Online.
2. Noted the pricing tier was Free, General Purpose, Serverless, Gen5, 2 vCores, with 99,910 free vCore seconds remaining for the month, and that overage billing was disabled. That last detail mattered to me since it meant I could not accidentally rack up a bill just from running queries in this lab.
3. Also noted the earliest restore point shown on the database, which is a reminder that Azure SQL Database keeps automatic backups even for a database this small.

### Working in the Query Editor
1. Opened the Query editor (preview) and read the warning that any query text in the editor is lost once the session ends, unless it is saved as a view or copied out somewhere else. That is a detail that is easy to skip past but matters if you are in the middle of building something you want to keep.
2. Expanded the Explorer pane and looked through the schema before writing anything. Under `dbo` and `SalesLT` I found tables including `Customer`, `CustomerAddress`, `Address`, `Product`, `ProductCategory`, `ProductDescription`, and several `ProductModel` related tables, along with folders for Views, Stored Procedures, and Functions under `SalesLT`.
3. Ran a first query against the sample data:
```sql
SELECT *
FROM SalesLT.Customer
```
4. This returned 847 rows across 15 columns, including fields like `CustomerID`, `NameStyle`, `Title`, `FirstName`, `MiddleName`, `LastName`, `CompanyName`, and `SalesPerson`. I also opened the `Address` table separately to compare its structure to `Customer` before deciding whether a join would be useful.
5. Started building a more targeted query by adding a `WHERE` clause to filter the `Customer` results instead of always pulling every row back with `SELECT *`. I did not finish narrowing the condition in this session, but the intent was to practice moving from "just look at everything" to "ask a specific question of the data."

## What's in This Repo
```
azure-sql-database-lab/
├── README.md                              # This file
└── screenshots/
    ├── 01-create-sql-database-server.png  # Server name, location, and authentication method options
    ├── 02-deployment-complete.png         # Deployment overview confirming success
    ├── 03-database-overview.png           # Pricing tier, status, and restore point details
    ├── 04-query-editor-opened.png         # Query editor with the session warning visible
    ├── 05-select-star-customer.png        # SELECT * FROM SalesLT.Customer with 847 rows returned
    └── 06-where-clause-in-progress.png    # Schema explorer expanded, WHERE clause being built
```

## Skills I Picked Up
- **Reading authentication options before clicking through them.** Azure SQL Database asks up front whether to use Microsoft Entra only, both Entra and SQL, or SQL only authentication, and that choice has real security implications rather than being a formality to skip past.
- **Verifying a deployment instead of assuming it worked.** Checking the deployment overview page for "complete" status, and noting the resource group and correlation ID, is a habit worth building even for a small lab database.
- **Reading a pricing tier carefully.** Understanding that the Free, General Purpose, Serverless tier came with a specific number of free vCore seconds and disabled overage billing helped me avoid worrying about cost while experimenting.
- **Exploring a schema before querying it.** Looking through the `dbo` and `SalesLT` schemas in the Explorer pane, including the Views, Stored Procedures, and Functions folders, gave me a sense of what was available before I wrote a single line of SQL.
- **Practicing the move from broad to specific.** Running `SELECT *` first to see the full shape of the data, then starting to add a `WHERE` clause, mirrors how I would actually want to work with an unfamiliar table in a real environment.

## How This Applies in the Real World
Provisioning managed cloud databases is a routine part of both administration and security work, and the decisions made at creation time, like which authentication model to allow, often outlive the person who set them up. A database created with SQL only authentication and a weak password is a very different risk profile from one locked down to Microsoft Entra identities, even though both might look identical once they are running.

Getting comfortable in the Query editor also matters beyond this lab, since being able to quickly explore an unfamiliar schema, run a broad query to understand the shape of the data, and then narrow it down with a `WHERE` clause is exactly the kind of workflow that comes up when investigating a data exposure or reviewing what a compromised account could actually see.

## Where I'm Coming From
I'm making the jump into cybersecurity from a background in **healthcare**. Labs like this one are partly about SQL and Azure specifically, and partly about building the broader habit of not trusting that something worked just because I clicked the button. Confirming the deployment, checking the pricing tier, and reading the session warning in the Query editor are all small things, but they add up to the kind of careful, methodical checking I'm trying to make automatic.

## What I Want to Learn Next
- Finishing filtered queries with `WHERE` clauses and moving on to joins across tables like `Customer`, `CustomerAddress`, and `Address`
- Saving useful queries as views so they persist beyond a single Query editor session
- Configuring server level and database level firewall rules instead of leaving that unexplored
- Connecting to the database from a client like Azure Data Studio or SQL Server Management Studio instead of only using the browser based editor
- Testing what Microsoft Entra only authentication actually looks like from the client side, including how a login attempt with a SQL login is rejected

## Limitations & What I'd Do Differently in Production
- **Only basic `SELECT *` queries were completed.** The `WHERE` clause was started but not finished, and no joins were run in this session.
- **Query text was not saved.** Since the editor warns that queries are lost when the session ends, anything not copied out or saved as a view would need to be rewritten from scratch next time.
- **Firewall and networking settings were not reviewed.** A production deployment would need a clear picture of what can reach the server, not just how logins are authenticated.
- **Single sample database, no real workload.** This was exploratory, not a stand in for how a production database with real data and real users would need to be secured and monitored.

## References
- [Azure SQL Database Documentation](https://learn.microsoft.com/en-us/azure/azure-sql/database/)
- [Authentication Methods for Azure SQL Database](https://learn.microsoft.com/en-us/azure/azure-sql/database/authentication-aad-overview)
- [AdventureWorksLT Sample Database](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure)
- [T SQL Reference](https://learn.microsoft.com/en-us/sql/t-sql/language-reference)
