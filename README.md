# Active Record Intro: Creating Tables

## Learning Competencies

- Creating database tables using Active Record

## Summary

![Database Schema](/schema_design.png)

*Figure 1.*  Database schema for this first challenge release.

In this challenge you will create the database tables seen in Figure 1.  You've been writing SQL to create tables in your database—even though the SQL might have been wrapped in Ruby methods.  Now, we're going to use Active Record to create and update our database schema, and Active Record will write the SQL for us.

Rather than writing SQL, we are going to write [Active Record migrations](http://guides.rubyonrails.org/migrations.html).  We'll write one migration for each change that we want to make to our database.  For example, we'll write a migration file for each table that we want to create.  In addition, we'll write new migrations for things like adding columns to already existing tables.

```SQL
CREATE TABLE dogs (
 id INTEGER PRIMARY KEY AUTOINCREMENT,
 name VARCHAR
 license VARCHAR
 age INTEGER,
 weight INTEGER,
 owner_id INTEGER
 created_at DATETIME,
 updated_at DATETIME);
```

*Figure 2.* SQL to create a dogs table based on Figure 1

To create the dogs table from Figure 1 in SQL, we would write code akin to what we see in Figure 2.  But, now we'll want to write Active Record migrations to do this.  A migration for creating the dogs table is provided for you in the `db/migrate/` folder; the name of the file is `20140901164300_create_dogs.rb`.  The contents of the migration are copied in Figure 3.

```ruby
class CreateDogs < ActiveRecord::Migration
  def change
    create_table :dogs do |t|
      t.string   :name, null: false, limit: 50
      t.string   :license, null: false
      t.integer  :age, limit: 2
      t.integer  :weight

      t.timestamps
    end
  end
end
```

*Figure 3.*  Active Record migration for creating a dogs table

In the migration, we define a class:  `CreateDogs` that inherits from
`ActiveRecord::Migration`.  The name of the class describes what this migration
is doing.

Then we define a `change` method that describes what running this migration
will do to update the database schema.  Inside the `change` method, we say to
call the [`create_table`](http://apidock.com/rails/ActiveRecord/ConnectionAdapters/SchemaStatements/create_table)
method and pass it (1) the name of the table, `:dogs`, and (2) a block
describing what to do with the table (i.e., which columns to add).

Within the block `t` refers to a `TableDefinition` object, but we can just
think of it as the table being created in the database.  And the block says,
add a string type column (i.e. `VARCHAR`) called `:name`, add a string type
column called `:license`, add an integer type column called `:age`, etc.  We're
calling the `string`, `integer`, and `timestamps` methods on the table object
and passing the names of the columns as arguments.

### &lt;EXTREMELY IMPORTANT&gt;

What are those hash options to the right of our declarations?  Those are known
as _constraints_.  We can tell the database to optimize the design of the
table.  For example, should our database ever allow a `dogs` table row to be
nameless, or license-less?  In those cases our database should defend itself
from getting garbage data.

We can also help conserve space in our database.  Is a dog's name ever longer
than 50 characters?  By default most databases allocate 256 characters for a
"string" field.  That means 206 bytes are locked up for every dog.  256 bytes,
so what?  Think of how many dogs are registered in the city you live, that 206
bytes allocated for every "Spot," "Byron," or "Rex" could turn into *literally*
gigabytes of nothingness that are paid for, cooled in data centers!

It is a mark of **the best developers** that they are **always** thinking about
how to help the database defend itself from dirty data.  Dirty data is hard to
clean up and, often, requires *taking a site offline*.  While programming stack
applications may come and go, databases tend to have a much longer service
lifetime (databases from the 60's and 70's are still runningn in many
businesses and most universities!)

You'll eventually learn about "validations" but database constraints are your
**first, strongest, and most reliable** means of protection against dirty data.

### &lt;/EXTREMELY IMPORTANT&gt;

A few more things to note:

- The name of the migration file begins with a timestamp in the format YYYYMMDDHHMMSS: `20140901164300`.  This is important. Active Record uses these timestamps to keep track of the migrations that have already been run.  Each migration will only be run once.

- The second half of the file name (i.e., after the timestamp) matches with the name of the class written in the migration:  `_create_dogs.rb` and `CreateDogs`.

- The class inherits from ActiveRecord::Migration.  This gives the class access to methods, like `create_table`, [`add_column`](http://apidock.com/rails/v4.0.2/ActiveRecord/ConnectionAdapters/SchemaStatements/add_column), etc.

- No `id` column is specified in the migration.  An `id` column is created automatically—unless you specify not to.  The `id` field is an autoincrementing integer field.

- Rather than explicitly creating `created_at` and `updated_at` columns, there is a shortcut method for creating them:  `timestamps`.

## Releases

### Release 0: Create Database and Run Given Migration

Follow these steps to begin this challenge.  We'll be creating the database and running the provided migration, which will cause the first set of specs to pass.  The migration will create a dogs table in the database.  The specs will test that the table has been set up correctly.

1. From the command line, run `bundle install` to ensure that the proper gems have been installed.

2. Use the provided Rake task to create the database.  Run `bundle exec rake db:create`.

3. See that the tests for the structure of the `dogs` table fail before running the migrations.  Run `bundle exec rspec spec/dogs_table_spec.rb`.

4. Use the provided Rake task to migrate the database. Run `bundle exec rake db:migrate`.

5.  See that the tests for the structure of the `dogs` table pass after running the migrations.  Rerun `bundle exec rspec spec/dogs_table_spec.rb`.

### Release 1:  Finish the Schema

To complete this challenge, we'll write migrations to create the remaining two tables from our schema design in Figure 1:  The `people` table and the `ratings` table.

To begin, use the provided Rake task to run all of the specs.  Run `bundle exec rake spec`.  We'll see that the tests for the `people` and `ratings` tables fail, as we would expect.

Write a migration to create the `people` table with the appropriate columns.  Use the provided Rake task to run the migrations.  Run the specs again.  The tests for the `people` table should all pass if the migration was written properly.  If any of the tests for the `people` table fail, use the provided Rake task to rollback the database (`db:rollback`), correct the migration file, run the migrations, and run the specs again.

Once all the tests for the `people` table pass, repeat the same process for creating the `ratings` table.  Once the entire test suite passes, submit your solution.

