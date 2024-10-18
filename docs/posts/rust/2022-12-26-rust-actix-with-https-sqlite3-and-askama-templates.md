---
title: Rust Actix with HTTPS, SQLite3 and Askama Templates
slug: rust-actix-with-https-sqlite3-and-askama-templates
date_published: 2022-12-26T22:01:17.000Z
date_updated: 2022-12-26T22:01:17.000Z
tags: #Import 2022-12-26 21:35
---

Here are the steps to install this configuration on Ubuntu 20.04. This process can take about half a day if you are lucky. For a group of traits that I feel is basic for any website nowadays, it's a relatively grueling process to get this all going. I really like how projects like [Caddy](https://caddyserver.com/) server has SSL/HTTPS by default and is SUPER simple to set up a server... Something that could be implemented for Rust to push it's adoption to more web devs.

First, we install Let's Encrypt. A free and simple way to provide SSL certificates for your website. You can install Let's Encrypt using [snapd](https://certbot.eff.org/lets-encrypt/ubuntufocal-other) (already included with Ubuntu 20). Running the certbot that generates the certificates will place the private key and certificate on the storage device running the certbot. The certbot script will let you know the locations of these files – please take a note of these locations as you will need this for later.

In order for the Actix server to provide TLS and HTTPS functionality using the certificates, you will need to have OpenSSL installed on your machine:

    sudo apt-get install build-essential openssl libssl-dev pkg-config
    

Next, we will need Sqlite3 installed:

    sudo apt-get install sqlite3 libsqlite3-dev
    

You should now be ready to install the Diesel CLI. The CLI is convenient to help set up your database and schema files:

    cargo install diesel_cli --no-default-features --features sqlite
    

The next group of instructions were taken and modified from [here](https://fdeantoni.medium.com/rust-actix-diesel-sqlite-d67a1c3ef0e).

Create and modify diesel.toml. Here is an example of the contents of the diesel.toml:

    # For documentation on how to configure this file,
    # see diesel.rs/guides/configuring-diesel-cli
    
    [print_schema]
    file = "src/schema.rs"
    

Create and modify .env:

    IP=10.0.1.1
    PORT=80
    DATABASE_URL=./src/all_users.db
    

Run diesel setup:

    diesel setup
    

Generate the migration scripts:

    diesel migration generate users
    

Modify the up.sql script to create your database tables:

    CREATE TABLE users (
        user_id INTEGER PRIMARY KEY NOT NULL,
        name VARCHAR NOT NULL UNIQUE,
        points INTEGER NOT NULL CHECK(points >= 0),
        is_admin INTEGER NOT NULL
    );
    

Modify the down.sql. A minimal example is:

    DROP TABLE users;
    

Run the diesel migration to create the tables in the database and write the schema:

    diesel migration run
    

After all this, you will still need to write the code for the models.rs and the code to wind up the Actix server itself, as well as a possible separate module for actions to modify the db.
