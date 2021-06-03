# How to select a random record from a PostgreSQL table using SQL

## Description
Sometimes you need to pull a single, random record out of a table.  Possible use cases are:

* random tip for the day
* games or academic testing (select a random question from a list)

If your database is structured correctly, this is a very simple, single line of SQL code.

This solution uses a UUID (universally unique identifier) field.  This method is preferred to using a numeric field for a number of reasons:

* it does not require you to know how many records are in the table
* adding and removing rows from the table has no effect on the functionality

## Environment
* PostgreSQL database (SQL command or part of a function)

## Resources

* [Supabase Account - Free Tier OK](https://supabase.io)
* or an installed PostgreSQL database instance

## Requirements
* Table has a uuid (universally unique identifier) field available
* PostgreSQL instance has the `uuid_generate_v4` extension installed (this extension is installed into Supabase PostgreSQL instances by default)

## Steps

Make sure the table you are querying has at least one uuid field available.  In this case we're using a v4 uuid:

```sql
CREATE TABLE people (
    id uuid DEFAULT extensions.uuid_generate_v4() NOT NULL,
    first_name text,
    last_name text,
    age integer
);
```
If you don't have a uuid field you can add one:

```sql
ALTER TABLE people 
   ADD COLUMN id uuid DEFAULT extensions.uuid_generate_v4() NOT NULL
```
(Note the field does not need to be called id and can have any name.)

Now just select a random record from the table like this:

```sql
SELECT * FROM uuidtest 
WHERE id >= extensions.uuid_generate_v4() 
ORDER BY id 
LIMIT 1;
```



