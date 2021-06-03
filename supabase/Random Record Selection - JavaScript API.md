# How to select a random record from a PostgreSQL table using the Supabase JavaScript API


## Description
Sometimes you need to pull a single, random record out of a table.  Possible use cases are:

* random tip for the day
* games or academic testing (select a random question from a list)

If your database is structured correctly, this is a very simple, single JavaScript or TypeScript statement.

This solution uses a UUID (universally unique identifier) field.  This method is preferred to using a numeric field for a number of reasons:

* it does not require you to know how many records are in the table
* adding and removing rows from the table has no effect on the functionality

## Environment
* Any Javascript or Typescript client application
* Supabase JS Client
* Supabase backend

## Resources

* [Supabase JS Client](https://github.com/supabase/supabase-js)
* [Supabase Account - Free Tier OK](https://supabase.io)

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

Generate a random uuid in the same format as the data contained in your uuid field.  Here's a uuid v4 function in pure JavaScript:

```js
function uuid() {
    return "xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx".replace(/[xy]/g, (char) => {
        let random = Math.random() * 16 | 0;
        let value = char === "x" ? random : (random % 4 + 8);
        return value.toString(16);     
    });
}
```

Now use the Supabase JavaScript Client Library to select a random from the table:

```js
const { data, error } = await supabase
    .from('people')
    .select('*')
    .order('id', { ascending: true })
    .filter('id', 'gte', this.uuid())
    .limit(1);
```


