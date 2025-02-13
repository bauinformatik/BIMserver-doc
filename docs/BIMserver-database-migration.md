BIMserver has a built-in schema migration system. This system works by keeping track of the current schema version in the database and also in the sofware itself. On startup, both version are compared and the server either starts up without a problem (`RUNNING`), goes into (`MIGRATION_REQUIRED`) mode when a migration is required, or to (`MIGRATION_IMPOSSIBLE`).

The next few sections describe the database upgrade system, but please make sure to read the last chapter as well because that describes when the database migration system _DOES NOT WORK_

## `MIGRATION_IMPOSSIBLE`

Reverse migrations are never possible, so when your database for example has version 15 and the software has version 14, BIMserver will always go into `MIGRATION_IMPOSSIBLE` state. The only solution for this is to download the right version of the software.

## `MIGRATION_REQUIRED`

BIMserver goes into this state when the database version is lower than the software version of the schema. BIMserver will not automatically migrate the database, because you should always (manually) MAKE A BACKUP OF YOUR DATABASE FIRST. When you have made a backup, you can either:

- Type "migrate" into the console, this only works when you actually have a console into BIMserver (local dev)
- Use the API call AdminInterface.migrateDatabase
- For BIMservers running on an application server (only Tomcat is tested), you can also add a parameter to your context called "autoMigrate". This will actually try to auto-migrate your database on startup when necessary. This is not recommended.

## How it works

For every database schema change in BIMserver we keep track of what has changed in migration steps. Each new schema version has a file called "StepXXXX", where XXXX is the schema version. Example: https://github.com/opensourceBIM/BIMserver/blob/master/BimServer/src/org/bimserver/database/migrations/steps/Step0038.java.

It might be obvious or not, but once a database migration has been published (by releasing a version of BIMserver), you cannot change a migration anymore without breaking the upgrade-system. The only way to revert changes is by adding another migration that reverses your earlier changes.

# No migration possible

There are a few reasons why database upgrades do not always work:

- Not all database changes can be described in the migration steps. A migration step can only change the schema (add a class, add an attribute, remove an attribute etc...). Some database changes do not change the schema, but the way the data is stored in the key-value store.
- Not all database changes are cleanly documented in migration steps. For example, a while back we migrated from IFC4 to IFC4Add2. This was done by changing Step0013. When a (already published) migration step is changed, this breaks the migration system.
- Some changes are not really schema changes, for example between 1.5.122 and 1.5.123 we changed how vertices are stored (float->double). Since vertices are stored in a byte[], the type (in the sense of the schema) was unchanged. This could actually be reflected in a database migration step by doing a data-conversion (but leaving the database schema the same).

# Current policy

Major **releases** should be migratable to newer versions within that major **release**. For example 1.4 should be upgradable to 1.4.1 without problems. Major releases are usually not upgradable to new major releases (1.4 to 1.5 for example is not possible).

> Important: The current 1.5 releases are still marked "Pre-release", the description says "Developers release". As long as a major version number is not officially released (1.5), we put in no effort whatsoever to keep migations working. As soon as 1.5 is officially released, we intend to support that version for a while and only do bug fixes that don't require incompatible database changes.

Could we put more effort into making sure databases are upgradable? Yes. At the moment though, we think fixing bugs and adding features etcetera is more important.

# What to do if you want to upgrade, but no upgrade-path is available?

So for example when you are running an unreleased 1.5 BIMserver and you want to upgrade to a newer (unreleased) version.

Depending on your situation, you might get away with the following:

1. Keep your old BIMserver running
2. Setup a second BIMserver with the version you would like to run
3. Write a piece of code that connects with both BIMservers
4. Query the first BIMserver for the information you want to migrate (for example Projects, Users, etc...)
5. Use the BIMserver API of the second BIMserver to insert that data

This solution has worked for a few people migration 1.3 to 1.4. You might get a headstart by studying https://github.com/opensourceBIM/BIMserver/blob/master/BimServer/src/org/bimserver/BimServerImporter.java. Which is non-maintained code, but at least it still compiles...

Problems you might run into with this approach is the fact that different versions of BIMserver have different API calls/layout, so a statically linked API implementation might be problematic.

The most important part of most BIMserver databases would be the models. Since sometimes bugs exist in older version of BIMserver, you might want to consider getting the original IFC file (stored in "{homedir}/incoming") and checking those into your new server instead of serializing those models in the original server.

> Important note: Using migration methods like these will never result in the same Object IDs. You should assume all ID's to be different. There is no way to do this kind of an import and force the ID's to stay the same, at least not with the current design of the database layer in BIMserver. This can obviously be problematic for databases that need to refer to for example BIMserver projects, users or revisions. For this reason we are (slowly) introducing [UUIDs](UUIDs.md) for certain objects.

## ServiceInterface.importData

This method is not maintained (internally it uses the earlier mentioned BimServerImporter), it's basically an implementation of the above, only it runs on the target BIMserver. It might still work. Just give it the address of the old BIMserver + username + password. The 4th parameter should point to a locally available copy (or direct link) to the "{homedir}/incoming" folder of your old BIMserver.
