# Heroku Postgres Helper

A Java library to help with Heroku Postgres specific logic, enabling
fast release phases without code duplication.

## Usage

build.gradle:

    // If using connection string in build script:
    buildscript {
        repositories {
            maven { url 'https://jitpack.io' }
        }
        dependencies {
            classpath 'com.github.vic-cw:heroku-postgres-helper:0.1.0'
        }
    }
    import com.github.viccw.herokupostgreshelper.HerokuPostgresHelper;
    
    // Use connection string in build script:
    flyway {
        url = HerokuPostgresHelper.getDatabaseJdbcConnectionString()
        driver = 'org.postgresql.Driver'
    }
    
    // If using connection string inside application logic:
    repositories {
        maven { url 'https://jitpack.io' }
    }

    dependencies {
        compile group: 'com.github.vic-cw', name: 'heroku-postgres-helper', version: '0.1.0'
    }

Java application code:

    import com.github.viccw.herokupostgreshelper.HerokuPostgresHelper;
    
    ...
    
    String databaseConnectionString = HerokuPostgresHelper.getDatabaseJdbcConnectionString();


## Why a need for this logic?

Heroku Postgres provides the database details through the `DATABASE_URL`
environment variable in the following pattern:
`postgres://username:password@host:port/databaseName`.

Conversely, any technology reliant on JDBC expects a connection string in
the following format:
`jdbc:postgresql://host:port/databaseName?user=username&password=password`

Thus logic is required to **translate the former into the latter**.

## Why a need for a library?

If using Gradle, or another build tool with the same restriction, sharing
code between the build script and the application is difficult.

There is a need for a library if using a database migration system,
such as FlywayDB, to **avoid code duplication** between build script and
application code.

Without such a library, options are:

1. to copy paste this logic once in build script, and once in application code
2. to place this logic inside a `buildSrc` folder at the root of Gradle project

## Why not simply place logic in `buildSrc`?

It turns out that **Heroku is very slow at compiling `buildSrc`** in a release phase,
typically taking 1 to 2 minutes to compile just 5 lines of code.

Using a library such as this one enables to speed up release commands without
duplicating any code.
