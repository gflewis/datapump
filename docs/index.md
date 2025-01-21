---
title: Getting started with DataPump 3.5
description: Exporting ServiceNow data to Oracle, SQL Server, MySQL or PostgreSQL with SNDML 3.5 and the DataPump App
---

DataPump is a contributed application which can be used to export ServiceNow data to Oracle, Microsoft SQL Server, MySQL or PostgreSQL. This application has two parts:

* A scoped ServiceNow app (**x_108443_sndml**) which is used to configure and manage export jobs. 
* A Java application (_a.k.a._ agent) which runs the exports. 

The application can be downloaded from [https://github.com/gflewis/sndml3/releases](https://github.com/gflewis/sndml3/releases).

Beginning with Release 3.5.0.10, the **Assets** section of the release will contain 
a ZIP file containing the following:
* **DataPump-v3.5.x.x-Install.xml** - _Update Set to install the ServiceNow app_
* **sndml-3.5.x.x-mssql.jar** - _JAR file for use with Microsoft SQL Server_
* **sndml-3.5.x.x-mysql.jar** - _JAR file for use with MySQL_
* **sndml-3.5.x.x-ora.jar** - _JAR file for use with Oracle_
* **sndml-3.5.x.x-pg.jar** - _JAR file for use with PostgreSQL_

## Create Users and Grant Roles

After installing the Update Set in your instance, 
the first step will be to create two new ServiceNow users 
which will be used by the Java agent.

#### datapump.agent
This account will be used to communicated with the DataPump scoped app 
and update the status of running jobs.
* Assign the user a randomly generated password
* Set the time zone to **GMT**
* Grant the fole **x_108443_sndml.daemon**

#### datapump.reader
This account will be used to export data from the instance
and requires "read" access to any tables which will be exported.
* Assign the user a randomly generated password
* Set the time zone to **GMT**
* Grant the role **snc_read_only**
* Grant the role **soap_query**
* Grant **itil** role and/or any roles necessary to read the requisite tables.

Users with **x_108443_sndml.admin** role can configure and monitor DataPump jobs.

## Create a Database Agent Record

In your ServiceNow instance, go to **DataPump > Agents** and click New. Create a new Database Agent record with the name "main".

## Create a Connection Profile

The connection profile is a Java properties file that contains 
credentials for the database and the ServiceNow instance, 
as well as other parameters that affect processing. 
The connection profile looks like this:

```
database.url=jdbc:mysql://name-of-database-host/myschema
database.username=******
database.password=******
database.schema=demo

app.instance=dev000000
app.username=datapump.agent
app.password=******
app.agent=main

reader.instance=dev000000
reader.username=datapump.reader
reader.password=******
```
Since the connection profile contains passwords, 
it should be in a secure location on your Linux or Windows server.

## Action Types
There are several types of jobs.

### Insert
"Insert" is used for initial loading or reloading of SQL tables. 
It inserts rows into the target table. 
If a record with the same sys_id already exists in the target table, 
then a primary key violation will occur and the row will be skipped.

If Truncate is checked, then the SQL table will be truncated prior to the load.

### Upsert
"Upsert" is used to load or update SQL tables. 
If the target record exists (based on sys_id), then it will be updated. 
Otherwise, it will be inserted.

If Since Last is checked, then only records inserted or updated in ServiceNow since the last run 
will be processed. The following filter will be used when retrieving records from ServiceNow:
<blockquote><code>sys_updated_on>=<i>lastrunstart</i></code></blockquote>
where 
_lastrunstart_ 
is determined from the "Last Run Start" field on the Database Table record.

### Sync
"Sync" compares the timestamps (`sys_updated_on`) in the source and target tables. 
Based on this comparison it will insert, update or delete target records. 
If the values of sys_updated_on match, then the record will be skipped.

If a Filter has been configured for the Database Table, 
the Sync will delete any records which do not match the filter.

### Execute
"Execute" executes an arbitrary SQL statement. This is typically used to run a database stored procedure.

