<h1>Rust Actix with HTTPS, SQLite3 and Askama Templates</h1>
<p>Here are the steps to install this configuration on Ubuntu 20.04. This process can take about half a day if you are lucky. For a group of traits that I feel is basic for any website nowadays, it's a relatively grueling process to get this all going. I really like how projects like <a href="https://caddyserver.com/">Caddy</a> server has SSL/HTTPS by default and is SUPER simple to set up a server... Something that could be implemented for Rust to push it's adoption to more web devs.</p><p>First, we install Let's Encrypt. A free and simple way to provide SSL certificates for your website. You can install Let's Encrypt using <a href="https://certbot.eff.org/lets-encrypt/ubuntufocal-other">snapd</a> (already included with Ubuntu 20). Running the certbot that generates the certificates will place the private key and certificate on the storage device running the certbot. The certbot script will let you know the locations of these files – please take a note of these locations as you will need this for later.</p><p>In order for the Actix server to provide TLS and HTTPS functionality using the certificates, you will need to have OpenSSL installed on your machine:</p><!--kg-card-begin: markdown--><pre><code>sudo apt-get install build-essential openssl libssl-dev pkg-config\
</code></pre>\
<!--kg-card-end: markdown--><p>Next, we will need Sqlite3 installed:</p><!--kg-card-begin: markdown--><pre><code>sudo apt-get install sqlite3 libsqlite3-dev\
</code></pre>\
<!--kg-card-end: markdown--><p>You should now be ready to install the Diesel CLI. The CLI is convenient to help set up your database and schema files:</p><!--kg-card-begin: markdown--><pre><code>cargo install diesel_cli --no-default-features --features sqlite\
</code></pre>\
<!--kg-card-end: markdown--><p>The next group of instructions were taken and modified from <a href="https://fdeantoni.medium.com/rust-actix-diesel-sqlite-d67a1c3ef0e">here</a>.</p><p>Create and modify diesel.toml. Here is an example of the contents of the diesel.toml:</p><!--kg-card-begin: markdown--><pre><code># For documentation on how to configure this file,\
# see diesel.rs/guides/configuring-diesel-cli\
\
[print_schema]\
file = &quot;src/schema.rs&quot;\
</code></pre>\
<!--kg-card-end: markdown--><p>Create and modify .env:</p><!--kg-card-begin: markdown--><pre><code>IP=10.0.1.1\
PORT=80\
DATABASE_URL=./src/all_users.db\
</code></pre>\
<!--kg-card-end: markdown--><p>Run diesel setup:</p><!--kg-card-begin: markdown--><pre><code>diesel setup\
</code></pre>\
<!--kg-card-end: markdown--><p>Generate the migration scripts:</p><!--kg-card-begin: markdown--><pre><code>diesel migration generate users\
</code></pre>\
<!--kg-card-end: markdown--><p>Modify the up.sql script to create your database tables:</p><!--kg-card-begin: markdown--><pre><code>CREATE TABLE users (\
    user_id INTEGER PRIMARY KEY NOT NULL,\
    name VARCHAR NOT NULL UNIQUE,\
    points INTEGER NOT NULL CHECK(points &gt;= 0),\
    is_admin INTEGER NOT NULL\
);\
</code></pre>\
<!--kg-card-end: markdown--><p>Modify the down.sql. A minimal example is:</p><!--kg-card-begin: markdown--><pre><code>DROP TABLE users;\
</code></pre>\
<!--kg-card-end: markdown--><p>Run the diesel migration to create the tables in the database and write the schema:</p><!--kg-card-begin: markdown--><pre><code>diesel migration run\
</code></pre>\
<!--kg-card-end: markdown--><p>After all this, you will still need to write the code for the models.rs and the code to wind up the Actix server itself, as well as a possible separate module for actions to modify the db.</p>
