# SQL Notes

SQL quick reference.

---

## SQL vs NoSQL
- SQL databases all can have a little bit different syntax and behavior but largely the same
- NoSQL databases are very purpose-built (document DB, key-value store, graph, vectorDB, etc.)

## SQL DBs
- SQLite is a lightweight, embeddable DB that can run within applications. Doesn't really do typechecking
- Postgres uses a client-server model and requires a server to be installed and listening on a network

## Tables
- CREATE tables for creating
- ALTER tables for changing the database schema without deleting and recreating (add/drop columns, rename table/column)
- migrations are just tracked changes to a database. make sure to couple DB changes with app/code changes
    - `up` migration applies changes to move schema forward
    - `down` migration rolls those changes back
    - popular tools that help w migrations are goose, flyway, liquibase, alembic, prisma
- `BLOB` = "binary large object"

## Constraints
- `NULL` in SQL means missing
- you can set NOT NULL constraints on columns to make sure they'll be there
- other constraints are `PRIMARY KEY`, `UNIQUE`, `NOT NULL`, etc. syntax to specify constraints may vary 
- `primary key` is a special column that uniquely identifies records within a table, and there can only be one primary key. Typically is an ID column
- Foreign keys are relationships between tables. In a table its the field that references another table's primary key
- `references` keyword in sqlite specifies which other table and field the foreign key references
- `schema` is the collecion of data types, table names, field names, constraints, relationships in a database
- relational databases try to limit data duplication through normalization (like having 1 table for users and 1 table for actions and then joining when you need both). Non relational just duplicates to speed up operations without needing joins


## CRUD
- CRUD ops in DBs can nicely correlate with HTTP ops:
    - POST -> CREATE
    - GET -> READ
    - PUT -> UPDATE
    - DELETE -> DELETE
- `insert` allows you to insert records into a table
- HTTP CRUD DB comms typically goes like this:
    - frontend processes some data from user
    - frontend sends data to the backend
    - backend makes SQL query to the DB
    - backend responds to the frontend
- many DBs support auto increment for fields when you insert new records
- `COUNT` syntax lets you see how many records in a table/column
- `WHERE` for filtering
- `DELETE` for removing records (using a WHERE clause to select the records)
    - setting up a "deleted" field on a DB is one way to protect data from deletion
- `UPDATE` is for updating specific fields for specific rows
- `ORMs (Object Relational Mappings)` are tools that let you do DB ops in a traditional programming language
    - ORMs can be limiting in your control but have some extra simplicity and some convenience features

## Basic Queries
- `AS` for aliases on fields
- Functions vary by DB provider but there are things like `IIF` for conditional values
- `Between`, `Distinct`, comparison/boolean operations, `IN`, `LIKE` (for wildcard/more flexible IN), etc. all common syntax as well

## Structuring
- `LIMIT` for limiting returns
- `ORDER BY` for sorting

## Aggregations
- `SUM`, `MAX`, `MIN`, `AVG`
- `GROUP BY` for group aggregates by another field
- `HAVING` is like `WHERE` but it operates on things after group by
- `ROUND` for rounding

## Subqueries
- just a query within a larger query. Has parentheses syntax. useful for retrieving data from other tables or creating more complex queries
- important to remember SQL is a programming language as well so can do some basic operations outside of just the table ops themselves (like addition, etc.)

## Normalization
- 3 types of relationships
    - one to one
    - one to many
    - many to many
- `normalization` is a method for structuring data to reduce data redundancy and improve data integrity 
    - 4 types:
        - 1NF - least normalized
            - table Must have a unique primary key
            - cell can't have a nested table as its value
            - good idea to almost never design a table that doesn't adhere to 1NF
        - 2NF
            - all rules of 1NF and all columns that are not part of the primary key are dependent on the entire primary key, not just one of the columns in the PK
            - one example here is if your PK is first name and last name column and you have a column for first initial. That doesn't depend on both. You'd have to have separate tables, one with first and last names, and then another with just first name and first initial
        - 3NF
            - all rules of 1 and 2NF but also all columns that aren't part of the primary key are dependent solely on the primary key. So like in the 2NF example if first name wasn't the primary key and we had an ID column that couldn't be 3NF because it's dependent on another column
        - BCNF - Most normalized
            - all rules above plus a column that's part of a primary key can not be entirely dependent on a column that's not part of that primary key. This really only comes into play when there's multiple possible primary key combinations that overlap. So like if you have a release year and a release date columns since those are dependent and not both primary keys then you need to change something
    - in normalization you can think of a primary key as either a single column or a collection of columns (as a `composite key`)
- not all that important to remember the levels of normalization, but important to remember points on boosting data integrity and need to also consider performance since over normalization can hurt it
- general prinicples are ensure all your tables have a unique primary key, usually call that column ID or something. Avoid duplicate data and avoid storing data that is completely dependent on other data (prefer just computing that). Keep schemas simple and mostly normalized, add precomputed columns as needed for performance

## Joins
- `inner join` is the simplest form, this just takes records that match from each table
- `left join` returns from table A regardless if they're in table B
- `right join` is the opposite of left
- `outer join` / `full join` returns all records from both regardless of matching

## Performance
- primary keys are indexed by default. indexed means they're mapped to a B tree or binary tree in the background which makes lookups much faster
- you can make indexes on non primary key columns via `CREATE INDEX` syntax
- indexing can speed up lookup speeds but can slow inserts so be careful
- can create multi column indexes, but usually this is only used in very specific cases
- remember to sanitize SQL inputs especially if they come from a user
