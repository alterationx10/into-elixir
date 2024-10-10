# Database

This is going to focus mainly on `Ecto` and `PostgreSQL` as the database.

Ecto is a collection of (six) modules, that are designed to work together to
provide a complete database access toolkit. Some of them are useful outside the
context of a database as well.

- `Repo` is the module that is responsible for interacting with the database.
- `Query` is the module that is responsible for building queries in a elixir/dsl
  way.
- `Schema` is the module that is responsible for defining and working with
  schemas, but you do not need a schema to interact with the database.
- `Changeset` is the module that is responsible for casting and validating data.

Ecto uses the **Repository Pattern** to interact with the database, so you can
generally assume that no interaction with the database has occurred until you
call `Repo.some_function`.
