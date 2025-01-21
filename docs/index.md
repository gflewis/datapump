---
title: Getting started with DataPump 3.5
description: Exporting ServiceNow data to Oracle, SQL Server, MySQL or PostgreSQL with SNDML 3.5 and the DataPump App
---

DataPump is a contributed application which can be used to export ServiceNow data to Oracle, Microsoft SQL Server, MySQL or PostgreSQL. This application has two parts:

* A scoped ServiceNow app (**x_108443_sndml**) which is used to configure and manage export jobs. 
* A Java application (_a.k.a._ agent) which runs the exports. 

The application can be downloaded from https://github.com/gflewis/sndml3/releases.

Beginning with Release 3.5.0.10, the **Assets** section of the release will contain a ZIP file containing the following:
* DataPump-v3.5.x.x-Install.xml - _Update Set to install the ServiceNow app_
* sndml-3.5.x.x-mssql.jar - _JAR file for use with Microsoft SQL Server_
* sndml-3.5.x.x-mysql.jar - _JAR file for use with MySQL_
* sndml-3.5.x.x-ora.jar - _JAR file for use with Oracle_
* sndml-3.5.x.x-pg.jar - _JAR file for use with PostgreSQL_
