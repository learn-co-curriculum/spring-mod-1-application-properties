# The Application Properties File

## Learning Goals

- Discuss the purpose of the application.properties file.
- Create a database in pgAdmin to reference in the application.properties file.
- Reference other configurations that could be added and provide documentation.

## Introduction

When we create a Spring Boot application, one file that always gets added is
the `application.properties` file under the `resources` directory. But we have yet
to really delve into what this file is for, how to use it, and just how important
this file is to our APIs.

In this lesson, we'll look more closely at the purpose of the
`application.properties` file and its purpose. We'll also work on adding some
properties to this file, so it isn't just empty anymore. Specifically, we will
address how to connect a database to our application using the properties file.

## What is the Properties File?

The `application.properties` file, or sometimes called the properties file or
configuration file, is used to add certain application-related properties or
configurations to our application that it may need in order to run properly. For
example, maybe we want to connect a database to our application. Or perhaps we
want some fields to be easily configurable. We can add these properties to this
file, and have it be read in at runtime.

This file is incredibly helpful when testing an application and deploying it
considering we can easily swap out the configurations based on what is needed. We
will address more of these benefits later when we talk about debugging, testing,
and deploying an application.

## Database Connection

If we want our application to be able to connect to a PostgresSQL database, then
we can add some properties in the `application.properties` file to do so! But
first, let's create a database to connect!

We'll stick with our football team example and create a **sports** database. Open
up pgAdmin4 and create a new database named "sports":

![create-sports-db](https://curriculum-content.s3.amazonaws.com/spring-mod-1/application-properties/create-sports-db.png)

Open the "Query Tool". Copy and paste the following schema creating the football
team table:

```postgresql
DROP TABLE IF EXISTS football_team;

CREATE TABLE football_team (
 id  INTEGER PRIMARY KEY,
 team_name TEXT NOT NULL,
 wins Integer,
 losses INTEGER,
 current_super_bowl_champion BOOLEAN
);
```

Once you do so, click the "Execute" button and ensure that the table has been
created.

![create-football-team-table](https://curriculum-content.s3.amazonaws.com/spring-mod-1/application-properties/create-football-team-table.png)

Now that we have our database all set up, we can connect the database to our
Spring Boot application in the `application.properties` file:

```properties
spring.datasource.url= jdbc:postgresql://localhost:5432/sports
spring.datasource.username= postgres
spring.datasource.password=
spring.datasource.driver-class-name=org.postgresql.Driver

# Hibernate ddl auto (create, create-drop, validate, update)
spring.jpa.hibernate.ddl-auto=create
spring.jpa.properties.hibernate.dialect= org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.globally_quoted_identifiers=true
```

Let's break down these properties to better understand what is happening:

- The `spring.datasource.url` is the URL connection string to connect to our
  sports database. This URL will always take the format of
  "jdbc:postgresql://server-name:server-port/database-name". Since we are
  connecting to our local database, we'll use "localhost" as the server name. We
  are also using the default server port of 5432, so we'll use "5432". We also
  just created our sports database so for the database name, we'll put "sports".
- The `spring.datasource.username` is the login name of the account used to access
  the database. When we set up and installed Postgres, we chose the default
  username, which is simply just "postgres".
- The `spring.datasource.password` is the password associated with the login
  account used to connect to the database. In the case above, it is empty, but you
  would put the password here so that it can properly connect. For now, we will
  put the plain text password. In the next module, we'll learn more about security
  and how we can encrypt this password to be more secure.
- `spring.datasource.driver-class-name` is "org.postgresql.Driver". This is the
  driver name that is needed to connect to PostgresSQL. If we were using another
  DBMS, like MySQL, this would look different.
- The `spring.jpa.hibernate.ddl-auto` property defines the database initialization
  behavior. Here are some of the common values:
  - `create-drop`: This drops all databases and creates new ones from scratch. It
    drops the database schema when the entity manager is closed using the
    `entityManager.close()` method.
  - `create`: It is similar to `create-drop` but it does not drop the database
    tables when the entity manager is closed.
  - `validate`: Checks if the entity definitions match an existing table schema.
  - `update`: Does not drop databases. Only updates the table schema.
  - `none`: Does not make any changes to the database.
  - We will use the `create` value when we first create and persist data.
    Afterwards, we'll switch it to `none` for the read, update, and delete
    operations, as this is a common practice when deploying to a production
    environment.
- The `spring.jpa.properties.hibernate.dialect` is added to define the dialect. In
  this case, we are using PostgreSQL, so we'll specify the dialect to be
  "org.hibernate.dialect.PostgreSQLDialect".
- The `spring.jpa.properties.hibernate.globally_quoted_identifiers` property can
  be set to true to globally escape all SQL reserved keywords, such as "order"
  and "user".

Keep all of this in mind for the next lesson, as we'll go ahead and add this to
our `application.properties` file then since we can't actually run with this yet
until we add some more dependencies.

## Other Configurations

There are so many other properties we can add to the `application.properties`
file! For right now, we're focusing on connecting our application to a database.
But we could do so much more - like re-configuring the port that the API runs on
or injecting properties into our source code using the `@Value` annotation. To see
what other properties exist, check out these
[common application properties](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.core).

For some additional documentation, check out
[Baeldung: Properties with Spring](https://www.baeldung.com/properties-with-spring).

## Summary

We now know that the `application.properties` file is a powerful configurable file
for us to add any properties to an application that may be needed in order for it
to run properly. We can also connect a database to our Spring Boot application now
as well! This will help when we want to persist data - which we will learn more
about how to do in the next lesson.

## Resources

- [Baeldung: Properties with Spring](https://www.baeldung.com/properties-with-spring)
- [Common Application Properties](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.core)
- [PostgreSQL Driver Connection Information](https://docs.oracle.com/cd/E19509-01/820-3497/agqka/index.html)
- [Connect to a PostgreSQL Database](https://www.bezkoder.com/spring-boot-postgresql-example/)
- [How to Escape SQL Reserved Keywords with Spring Boot](https://www.chrouki.com/posts/escape-sql-reserved-keywords-jpa-hibernate/)
