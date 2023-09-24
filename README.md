# cloudWorld2023

*** This page just has my "favorites" from presentations I attended, it does not, by any stretch, cover everything. ***

## LRN1330 - Chris Saxon

- If not exists in DDL
  - Instead of "drop table t;" being in our DDL the first time and causing an error, we can now "drop table t if exists" in 23c and we'll never get that error.  This is not just drop, it's create, alter, and it's not just tables, it's any object we'd perform DDL on.
- Developer Role
  - Just like Oracle provides a DBA role, though they say it's supposed to be a template not something we use, there is now a db_developer_role which has all the privileges a developer would need.  This would eliminate the need to grant connect and SCHEMA_OWNER roles to developers in 23c.
- No more "dual."
  - Instead of "select 'x' from dual;" for example, we can now just "select x;"
- There's finally a "boolean" datatype, and they made it backwards compatible so things like "YES/NO", "ON/OFF", 1/0 all still work.
- Schema level privileges
  - The "ANY" privs can now be scoped.  Instead of just "GRANT SELECT ANY TABLE TO ANDY;" we can now specify "GRANT SELECT ANY TABLE IN SCHEMA ANDY TO ANDY;"  We give them all the tables today, why clutter up the data dictionary with extra grants?

*** For more information, See Chris Saxon's presentation from Cloudworld 2023 ***

## THR2365 - Connor McDonald
- DBMS_XPLAN - SQL Analysis
  - The notes section will now show us things like "hey, there's a cartesian product, you're missing a join" and "oh, you added something to your select list, but didn't use an aggregate function or add it to your group by."
- Messages
  - While not technically part of 23c, there's a project going on to make messages more meaningful. 
  - See [Connor's youtoube](https://www.youtube.com/watch?v=plcs_BgSFxY) for more info on this.

## LRN1426 - Connor McDonald & Oren Nakdimon
- Overrideable clauses 
  - You can override things, since 12.2, in a query - Default Directory, Location, Access Parameters, Reject Limit
- Parameterized Views - 19.7/21c 
  - SQL Macros now give us a way to have a parameterized view
  - We can also use SQL Macros to have polymorphic views

## LRN1030 - Daniel Overby Hansen, Mike Dietrich, Marco Oberli
- Many (more than 30 hours) of free recorded technical content on [MikeDietrichDE.Com](https://mikedietrichde.com/videos)
- Always move prod databases from one LTS version to the next.
- 23c will ONLY allow multitenant databases.  
- To prevent going over the limit and needing a PDB licence, you can set a the max_pdbs parameter to 3.
- In 23c, traditional auditing is deprecated.  It will still write your current policies, but to create new policies, you must convert to unified auditing.
- always use autoupgrade, there will soon be EM support for it as well.
- ORADIFF lets you see differences between version.
- [MOS  note 207303.1](https://support.oracle.com/epmos/faces/DocumentDisplay?id=207303.1) - Client/Server interoperability
- [MOS note 136697.1](https://support.oracle.com/epmos/faces/DocumentDisplay?id=136697.1) checks for common data dictionary problems. Get the [hcheck.sql](https://support.oracle.com/epmos/main/downloadattachmentprocessor?parent=DOCUMENT&sourceId=136697.1&attachid=136697.1:HCHECK4&clickstream=yes)
  - Needs to be run in each PDB.
- Gather dictionary and fixed object stats before and after upgrade - This can significantly reduce upgrade times as well as datapump times.
- Capture workload data and replay it in an upgraded database
- Do use SQL plan management/DON'T use compatibility or optimizer_features_enable - Plan stability is not what these are for.
- You can NOT rollback an upgrade.  Review presentation for other options.
- There are some recommended underscore parameters for the upgrade to 23c, it looks like.
- Recorded Demo of an upgrade to 23c on [youtube](https://youtu.be/Zo4G2vnKUuM)
- [AutoUpgrade 2.0 webinar](https://www.youtube.com/watch?v=69Hx1WoJ_HE&t=5330s)
- Their "real world" example person recommended dataguard for migrating larger databases.
- They offer self service PDB creation/deployment via pipelines.
  - There are some cool examples in the presentation.
- They shared a [youtube channel](https://www.youtube.com/@upgradenow) for more on upgrades and migrations.
  - You can try it free at Oracle LiveLabs, apparently.

## LRN3524
- Lots of info about connection pools, and there are apparently 3 different kinds we could be/should be using for databases with high numbers of connections.  Our Own databases anyone?!
- FAN/TAF/TAC - We REALLY need to get on this...

## LRN2343
- Indexes
  - 19c - automatic indexing on Exadata (Chandra is investigating this)
  - Virtual Indexes
    - create index <index name> unusuable.
      - The optimizer can't use it, but you can generate stats on it, set skip_unusable_Indexes to false for you session, and do an explain plan to see if it might be used.  
  - Invisible Indexes
    - When creating an index for a specific problem, you can make the index invisible, then use a hint/sql patch to have ONLY the query you're trying to fix use it while you're doing regression testing for the rest of your application.  
  - Partial indexes
    - We REALLY need to look at this more often.  Often, in a table, there is a single value that is a large part of the table, and we would want to do a full table scan for that.  So why index it?  Use case statements to index ONLY the parts of the index we want.
  - Virtual columns/virtual column indexes

- Clustering
  - Can pack the values together more efficiently so you don't have to read so many blocks of data.

- Online operations
  - We mostly do this with indexes, but it can be done with many things and prevents th eneed for an outage.

- Dataguard as a playground
  - We can sometimes use dataguard as a playground 
    ```
    alter database convert to snapshot standby;
    ```
    - Implicitly takes a GRP.  You can then open the databse read/write as it's no longer a dataguard database (I think?)
    - Do any playing you need to do...including patching, it sounded like?  Test impacts.  
    - Then convert it back to a standby
    ```
    alter database convert to physical standby;
    alter database recover managed standby database disconnect;
    ```
    - While you were playing logs were still being shipped, so nothing is broken when you go back

- SPM
  - We do not evolve our baselines hardly ever.
  - Auto-SPM is part of Enterprise Edition in 23c.

