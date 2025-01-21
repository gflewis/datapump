---
title: Getting started with DataPump 3.5
description: Exporting ServiceNow data to Oracle, SQL Server, MySQL or PostgreSQL with SNDML 3.5 and the DataPump App
---

DataPump is a contributed application which can be used to export ServiceNow data to Oracle, Microsoft SQL Server, MySQL or PostgreSQL. This application has two parts:

* A scoped ServiceNow app (**x_108443_sndml**) which is used to configure and manage export jobs. 
* A Java application (_a.k.a._ agent) which runs the exports. 

The application can be downloaded from https://github.com/gflewis/sndml3/releases.

Beginning with Release 3.5.0.10, the **Assets** section of the release will contain 
a ZIP file containing the following:
* DataPump-v3.5.x.x-Install.xml - _Update Set to install the ServiceNow app_
* sndml-3.5.x.x-mssql.jar - _JAR file for use with Microsoft SQL Server_
* sndml-3.5.x.x-mysql.jar - _JAR file for use with MySQL_
* sndml-3.5.x.x-ora.jar - _JAR file for use with Oracle_
* sndml-3.5.x.x-pg.jar - _JAR file for use with PostgreSQL_

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


