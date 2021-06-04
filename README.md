# Key Technologies
*	Azure Data Factory.
*	SQL Server, SQL Edge, Azure SQL Database
*	Change Tracking

## Solution
The purpose of this repo is to share and inspire on replication solution from Azure SQL Edge or Arc Data Services on the Edge(on premise) to Azure SQL Database. The solution was created in a project, were a lot of other technical platforms was excluded, either by technical or company requirements. As an example Azure Synapse was excluded by the company’s requirements 
The solution can handle many SQL on the Edge (sources) to one or more Azure SQL DB (Destinations) . The dynamic pipeline that copies data from SQL on the Edge to Azure SQL Database using Azure Data Factory and Change Tracking (CT) feature in SQL to solve the project requirement. 
The more common scenario is from an Edge RDBMS to a Cloud storage solution, but this approach is instead direct from Edge RDBMS to SQL in Cloud, which was the project main ask. 

The solutions key components:
* Change Tracking (CT)
* Azure Data Factory (ADF)

CT is used to capture all changes and is chosen instead of CDC, thanks to all current versions and editions of SQL supports CT. 
On a high level, when CT is enabled for a table, all data inserted, updated or deleted will be tracked and saved for the duration of the retention period, which is configurable and have a default value of two days. All changes can be queried from a unique internal table for every enabled table. The tracked data changes can then be queried and copied by a scheduled ADF pipelines. 
The initial setup Pipeline creates a ChangeTrackingVersion table and populates it with table names. This gives the solution manageability of the replication, on which tables should be enabled and add to a logical group. The logical grouping was added because all tables would not have the same schedule. All pipelines interactions with the ChangeTrackingVersion table and other internal CT tables in the database is done through Stored procedures, which are created by the initial setup.
As mentioned earlier, the current version of the replication supports a filtering on table level. The management of enable and disable is done through one specific table (ChangeTrackingVersion). A future version of the solution could be expanded to allow a more granular configuration on what to copy from the source, which not just filter tables but filter out rows. During the project the customer did not foresee in the near future anymore granular demand then filtering on table level.

All code and necessary data objects are all parts of different logical steps(activities) in the ADF pipelines. The code, mainly TSQL code, is divided up in the different activities. It creates a manageable solution containing two types of pipelines which can be seen as templates. 
*	The first pipeline creates necessary database, activates change tracking on all tables, creates a schema name and a table for tracking and store procedures in the source (database in the edge) and destination (database in Azure). This one just needs to be executed once. 
*	The second pipeline checks if new tables exist and prepare the pipe to copy data for all tables name that are flagged active in the tracking table. The pipe makes an initial full copy if it is the first time or else an incremental copy to the destination. 

Initial setup pipeline, will create the following in the source database:
*	1 Schema, with the name CT. All replication objects belong to this schema name.
*	4 stored procedures
    * enabledCTonenewtables (enables CT on new tables entered in ChangeTrackingVersion table)
    * Getincrementaldata (selects the incremental changes since last run)
    * InsertNewChangeTrackingtable (updates the ChangeTrackingVersion table with new tables) 
    * UpdateChangeTrackingVersion (updates the table ChangeTrackingVersion and sets the CT run id for a table)
*	1 Table named ChangeTrackingVersion
*	Database enabled for CT.

And in the destination database:
*	1 Schema with the name Stage
*	2 stored procedures:
    * Createclusterindex (Creates cluster on destination table, if doesn’t exist)
    * Updatetablesfromstage (Destination tables get the incremental copy from a temporay stage table)

# How to run
1.	Deploy the ADF in target Azure Subscription.
2.	If doesn’t exists, create a source database and a destination database
3.	Open Linked service object and configure Source and Destination database. 2.1 Create IR-Host if needed
4.	Run the initial setup pipeline. Which will activate Change Tracking on the source database and create a destination database.
5.	Use Visual Data studio or SSMS to look at the result

