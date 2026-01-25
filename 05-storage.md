# storage
## Storage
Arguably the most important part of your typical web application is the storage of data. It would be pretty useless if each time you logged into your account on YouTube, Twitter or GitHub, all of your subscriptions, tweets, or repositories were gone.

### Memory vs Disk
When you run a program on your computer (like our HTTP server), the program is loaded into memory. Memory is a lot like a scratch pad. It's fast, but it's not permanent. If the program terminates or restarts, the data in memory is lost.

When you're building a web server, any data you store in memory (in your program's variables) is lost when the server is restarted. Any important data needs to be saved to disk via the file system.

### Option 1: Raw Files
We could take our user's data, serialize it to JSON, and save it to disk in .json files (or any other format for that matter). It's simple, and will even work for small applications. Trouble is, it will run into problems fast:

* Concurrency: If two requests try to write to the same file at the same time, you'll get overwritten data.
* Scalability: It's not efficient to read and write large files to disk for every request.
* Complexity: You'll have to write a lot of code to manage the files, and the chances of bugs are high.

### Option 2: a Database
At the end of the day, a database technology like MySQL, PostgreSQL, or MongoDB "just" writes files to disk. The difference is that they also come with all the fancy code and algorithms that make managing those files efficient and safe. In the case of a SQL database, the files are abstracted away from us entirely. You just write SQL queries and let the DB handle the rest.

We will be using option 2: PostgreSQL. It's a production-ready, open-source SQL database. It's a great choice for many web applications, and as a back-end engineer, it might be the single most important database to be familiar with.

### Assignment
1. Install Postgres v15 or later.
    MacOS:
    ```
    brew install postgresq@15
    ```

    Linux/WSL:
    ```
    sudo apt update
    sudo apt install postgresql postgresql-contrib
    ```
2. Ensure the installation worked. The `psql` command-line utility is the default client for Postgres. Use it to make sure you're on version 15+ of Postgres:
    ```
    psql --version
    ```
3. Update postgres password (Linux only):
    ```
    sudo passwd postgres
    ```

4. Start the Postgres server in the background
    * Mac: `brew services start postgresql@15`
    * Linux: `sudo service postgresql start`

5. Connect to the server. I recommend simply using the `psql` client. It's the "default" client for Postgres, and it's a great way to interact with the database. While it's not as user-friendly as a GUI like PGAdmin, it's a great tool to be able to do at least basic operations with.

    Enter the `psql` shell:
    - Mac: `psql postgres`
    - Linux: `sudo -u postgres psql`

    You should see a new prompt that looks like this:
    ```
    postgres=#
    ```

6. Create a new database. I called mine `chirpy`:
    ```
    CREATE DATABASE chirpy;
    ```

7. Connect to the new database:
    ```
    \c chirpy
    ```

    You should see a new prompt that looks like this:
    ```
    chirpy=#
    ```

8. Set the user password (Linux only)
    ```
    ALTER USER postgres WITH PASSWORD <password>
    ```

9. Query the database
    From here you can run SQL queries against the `chirpy` database. For example, to see the version of Postgres you're running you an run:
    ```
    SELECT version();
    ```

## Goose Migrations
Goose is a database migration tool written in Go. It runs migrations from a set of SQL files, making it a perfect fit for this project (we wanna stay close to the raw SQL).

### What is a Migration?
A migration is just a set of changes to your database table. You can have as many migrations as needed as your requirements change over time. For example, one migration might create a new table, one might delete a column, and one might add 2 new columns.

An "up" migration moves the state of the database from its current schema to the schema that you want. So, to get a "blank" database to the state it needs to be ready to run your application, you run all the "up" migrations.

If something breaks, you can run one of the "down" migrations to revert the database to a previous state. "Down" migrations are also used if you need to reset a local testing database to a known state.

### Users
Our API needs to support the standard CRUD operations for "users" - the people logging into and using our application.

### Assignment
1. Install Goose.
    Goose is just a command line tool that happens to be written in Go. I recommend installing it using `go install`:
    ```
    go install github.com/pressly/goose/v3/cmd/goose@latest
    ```
    
    Run `goose --version` to make sure it's installed correctly.

2. Create a `users` migration in a new `sql/schema` directory.

    A migration in Goose is just a `.sql` file with some SQL queries and special comments. Our first migration should just create a `users` table. The simplest format for these files is:
    ```
    number_name.sql
    ```

    For example, I created a file in `sql/schema` called `001_users.sql` with the following contents:
    ```
    -- +goose Up
    CREATE TABLE ...

    -- +goose Down
    DROP TABLE users;
    ```

    Write out the `CREATE TABLE` statement in full, I left it blank for you to fill in. A `user` should have 4 fields:
    * `id`: a UUID that will serve as the primary key
    * `created_at`: a TIMESTAMP that can not be null
    * `updated_at`: a TIMESTAMP that can not be null
    * `email`: TEXT that can not be null and must be unique

    The `-- +goose Up` and `-- +goose Down` comments are required. They tell Goose how to run the migration in each direction.

3. Get your connection string. A connection string is just a URL with all of the information needed to connect to a database. The format is:
    ```
    protocol://username:password@host:port/database
    ```

    Here are examples:
    - macOS (no password, your username): `postgres://wagslane:@localhost:5432/chirpy`
    - Linux (password from last lesson, postgres user): `postgres://postgres:postgres@localhost:5432/chirpy`

    Test your connection string by running psql, for example:
    ```
    psql "postgres://postgres:postgres@localhost:5432/chirpy"
    ```

4. Run the up migration.

    `cd` into the `sql/schema` directory and run:
    ```
    goose postgres <connection_string> up
    ```

    Run your migration! Make sure it works by using psql to find your newly created users table:
    ```
    psql chirpy
    \dt
    ```

5. Run the `down` migration to make sure it works (it should just drop the table).

6. When you're satisfied, run the up migration again to recreate the table.

## SQLC
SQLC is an amazing Go program that generates Go code from SQL queries. It's not exactly an ORM, but rather a tool that makes working with raw SQL easy and type-safe.

We will be using Goose to manage our database migrations (the schema). We'll be using SQLC to generate Go code that our application can use to interact with the database (run queries).