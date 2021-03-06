# How change your PostgreSQL database password


## Description

Your PostgreSQL database is the core of your Supabase project, so it's important to make sure it has a strong, secure password at all times.  Changing the password is easy -- just make sure you choose a password that:

* is very secure
* does not contain of the following symbols: **: / ? # ] [ @ ! $ & ' ( ) * + , ; =** 

These special symbols can conflict with connection string URLs or cause other problems, so it's best to avoid them.  You can still make a secure password using a longer string or a randomized set of upper and lowercase letters and digits.

## Environment
* Any PostgreSQL database
* Any Supabase project

## Resources

* [Supabase Account - Free Tier OK](https://supabase.io)

## Requirements
none


## Steps

Issue the following statement in a SQL editor:

(From the [Supabase Dashboard](https://supabase.io): Home / Select a Project / SQL / + New Query)

```sql
ALTER USER postgres WITH PASSWORD 'new_password';
```

Be sure to remember your password or store it in a secure place.

