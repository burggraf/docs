# How to use arrays in PostgreSQL and the Supabase Javascript API


## Description

PostgreSQL supports flexible [array types](https://www.postgresql.org/docs/12/arrays.html).  You can use arrays in your PostgreSQL tables in the Supabase dashboard and in the Javascript API.

## Environment

* Supabase JS Client
* Supabase (or any PostgreSQL) backend

## Resources

* [Supabase JS Client](https://github.com/supabase/supabase-js)
* [Supabase Account - Free Tier OK](https://supabase.io)
* [PostgreSQL Arrays](https://www.postgresql.org/docs/12/arrays.html)

## Requirements
* Supabase account or self-hosted Supabase instance
* Javascript-based client application (Angular, React, Vue, etc.)


## Steps

In the Supabase Dashboard, open a Supabase project, then go to SQL / + New Query.

Create a test table with a text array (array of strings):

```sql
CREATE TABLE arraytest (id integer NOT NULL, textarray text ARRAY);
```

*Note: this is also how arrays are created in the Supabase Table Editor when you check the box "Define as array" when creating a new column.*

Now, add a record to the table:

```sql
INSERT INTO arraytest (id, textarray) VALUES (1, ARRAY['Harry', 'Larry', 'Moe']);    
```

View the results:

```sql
SELECT * FROM arraytest;
```

Your first array data!

| id  | textarray                    |
| --- | ---------------------------- |
| 1   | ["Harry","Larry","Moe"]      |

Now, to insert a record from the Javascript client:

```js
const { data, error } = await supabase
.from('arraytest')
.insert([
    { id: 2, textarray: ['one', 'two', 'three', 'four'] }
]);  
```

That's all there is to it.

To query an array, PostgreSQL uses 1-based arrays, so be careful, since you're probably used to 0-based arrays in Javascript.

```js
SELECT textarray[1], array_length(textarray, 1) FROM arraytest;
```

returns:

| textarray | array_length |
| --------- | ------------ |
| Harry     | 3            |



