# How to use JSON Data in PostgreSQL and the Supabase Javascript API


## Description

PostgreSQL supports [JSON functions and operators](https://www.postgresql.org/docs/12/functions-json.html) that can give you nearly unlimited flexiblity when storing data inside a database column.  In this document you'll learn how to:

* create database columns that store JSON data
* insert JSON data into a database
* query JSON data stored in a database

PostgreSQL supports two types of JSON columns: JSON and JSONB.  The recommended type is JSONB, so that's what we'll be using throughout this document.

> The json data type stores an exact copy of the input text, which processing functions must reparse on each execution; while jsonb data is stored in a decomposed binary format that makes it slightly slower to input due to added conversion overhead, but significantly faster to process, since no reparsing is needed. jsonb also supports indexing, which can be a significant advantage.

From: [PostgreSQL JSON types](https://www.postgresql.org/docs/12/datatype-json.html)

## Environment

* Supabase JS Client
* Supabase (or any PostgreSQL) backend

## Resources

* [Supabase Account - Free Tier OK](https://supabase.io)
* [Supabase JS Client](https://github.com/supabase/supabase-js)
* [PostgreSQL: JSON Functions and Operators](https://www.postgresql.org/docs/12/functions-json.html)

## Requirements
* Supabase account or self-hosted Supabase instance
* Javascript-based client application (Angular, React, Vue, etc.)


## Steps

Create a table with a JSON column.  If you're using the [Supabase Dashboard](https://supabase.io) you can just select the "JSONB" data type for any columns you create in the table editor.

### Using SQL
You can also create a table in a SQL Query window like this:

```sql
CREATE TABLE game_log (
    id UUID DEFAULT uuid_generate_v4() NOT NULL,
    game_date DATE,
    home_team TEXT,
    visiting_team TEXT,
    game_data JSONB
);
```

Insert data into the table:

```sql
INSERT into game_log (game_date, home_team, visiting_team, game_data)
values (
'2021-06-02',
'Chicago Cubs',
'San Diego Padres',
'{"winning_pitcher": "Alzolay", 
  "losing_pitcher": "Johnson", 
  "final_score": {
    "visitors": {"runs": 1, "hits": 5, "errors": 2}, 
    "home": {"runs": 6, "hits": 9, "errors": 0}
  }, 
  "innings": {
    "visitors": [0,0,0,1,0,0,0,0,0], 
    "home": [0,0,0,1,2,0,3,0]}
  }'
);
```

View the data:

```sql
SELECT * FROM game_log;
```

| id                                   | game_date                | home_team    | visiting_team    | game_data                                                                                                                                                                                                                    |
| ------------------------------------ | ------------------------ | ------------ | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 24550f27-28d2-46f3-b577-aeede4614018 | 2021-06-02T00:00:00.000Z | Chicago Cubs | San Diego Padres | {"innings":{"home":[0,0,0,1,2,0,3,0],"visitors":[0,0,0,1,0,0,0,0,0]},"final_score":{"home":{"hits":9,"runs":6,"errors":0},"visitors":{"hits":5,"runs":1,"errors":2}},"losing_pitcher":"Johnson","winning_pitcher":"Alzolay"} |

Select the winning pitcher from the JSONB field:

```sql
SELECT game_data -> 'winning_pitcher' AS winning_pitcher FROM game_log;
```
| winning_pitcher |
| --------------- |
| Alzolay         |

Get the final scores:

```sql
SELECT 
  visiting_team, 
  game_data -> 'final_score' -> 'visitors' -> 'runs' as visitors_runs,
  home_team, 
  game_data -> 'final_score' -> 'home' -> 'runs' as home_runs
FROM game_log;
```
| visiting_team    | visitors_runs | home_team    | home_runs |
| ---------------- | ------------- | ------------ | --------- |
| San Diego Padres | 1             | Chicago Cubs | 6         |

How many runs did the home team score in the 7th inning? (Note that data is stored in a zero-based array, so to get data from the 7th inning, we use a 6):

```sql
SELECT home_team, 
game_data -> 'innings' -> 'home' -> 6 as seventh_inning
FROM game_log;
```
| home_team    | seventh_inning |
| ------------ | -------------- |
| Chicago Cubs | 3              |

#### Data Types
One last thing to note:  the -> operator return JSONB data.  If you want TEXT/STRING data returned, you need to use the ->> operator.

```sql
SELECT 
  game_data -> 'winning_pitcher' as "json-data-type",
  game_data ->> 'winning_pitcher' as "text-data-type"
FROM game_log;
```
| json-data-type | text-data-type |
| -------------- | -------------- |
| Alzolay        | Alzolay        |
While the above fields look identical, they're actually two different data types.

### Using the Javascript API

Insert data into the table:

```js
const { data, error } = await supabase
.from('game_log')
.insert([{ 
  game_date: '2021-06-02',
  home_team: 'Chicago Cubs',
  visiting_team: 'San Diego Padres',
  game_data: {
    "winning_pitcher": "Alzolay", 
    "losing_pitcher": "Johnson", 
    "final_score": {
      "visitors": {"runs": 1, "hits": 5, "errors": 2}, 
      "home": {"runs": 6, "hits": 9, "errors": 0}
    }, 
    "innings": {
      "visitors": [0,0,0,1,0,0,0,0,0], 
      "home": [0,0,0,1,2,0,3,0]
    }
  }        
}]);  
```
View the data:

```js
const { data, error } = await supabase
.from('game_log')
.select('*');
console.log(JSON.stringify(data,null,2));
```
Results:

```js
[
  {
    "id": "a4c577bc-4343-440d-8f8b-9fef0af225cc",
    "game_date": "2021-06-02",
    "home_team": "Chicago Cubs",
    "visiting_team": "San Diego Padres",
    "game_data": {
      "innings": {
        "home": [0,0,0,1,2,0,3,0],
        "visitors": [0,0,0,1,0,0,0,0,0]
      },
      "final_score": {
        "home": {"hits": 9, "runs": 6, "errors": 0},
        "visitors": {"hits": 5, "runs": 1, "errors": 2}
      },
      "losing_pitcher": "Johnson",
      "winning_pitcher": "Alzolay"
    }
  }
]
```

Select the winning pitcher from the JSONB field:

```js
const { data, error } = await supabase
.from('game_log')
.select('game_data->winning_pitcher');
console.log(JSON.stringify(data,null,2));
```

Results:

```js
[
  {
    "winning_pitcher": "Alzolay"
  }
]
```

Get the final scores:

```js
const { data, error } = await supabase
.from('game_log')
.select('visiting_team, visitors_runs:game_data->final_score->visitors->runs, home_team, home_runs:game_data->final_score->home->runs');
console.log(JSON.stringify(data,null,2));
```

Results:

```js
[
  {
    "visiting_team": "San Diego Padres",
    "visitors_runs": 1,
    "home_team": "Chicago Cubs",
    "home_runs": 6
  }
]
```

How many runs did the home team score in the 7th inning? (Note that data is stored in a zero-based array, so to get data from the 7th inning, we use a 6):

```js
const { data, error } = await this.supabase
.from('game_log')
.select('home_team, seventh_inning_runs:game_data->innings->home->6');
console.log(JSON.stringify(data,null,2));
```

Results:

```js
[
  {
    "home_team": "Chicago Cubs",
    "seventh_inning_runs": 3
  }
]
```

