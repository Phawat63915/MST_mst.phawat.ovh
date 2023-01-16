#### Begin the installation process by updating your system APT index to refresh the system packages. Run the command below.
```sh
sudo apt update && sudo apt dist-upgrade -y
```
#### Ensure the following packages are installed as well.
```sh
sudo apt install gnupg2 wget vim curl -y
```
## Step 1: Enable PostgreSQL 15 Package Repository
By default, the PostgreSQL packages exist in Ubuntu 22.04/20.04. These are however not the latest versions. To check the available PostgreSQL packages in your system, issue the following command.
```sh
sudo apt-cache search postgresql | grep postgresql
```
Since PostgreSQL 15 is not available in the default package repository, we will begin installation by enabling its official package repository by running the following command.
```sh
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $
```
Next import the GPG signing key for the repository. Do this by running the following command.
```sh
# sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
rm -rf /etc/apt/trusted.gpg.d/pgdg.asc & curl https://www.postgresql.org/media/keys/ACCC4CF8.asc -o /etc/apt/trusted.gpg.d/pgdg.asc
```
Finally, update the system packages so that the changes above can take effect.
```sh
sudo apt update -y
```
##  Step 2: Install PostgreSQL 15 Database Server and Client
PostgreSQL 15 can be installed from the Binary packages or Built from the source. The Binaries installation method is the recommended method. Building from source is recommended for developers developing PostgreSQL or extensions.
In the previous step, we prepared our system for PostgreSQL 15 installation. We will now install the PostgreSQL 15 Database server and Client.

Execute the following command:
```sh
sudo apt install postgresql postgresql-client -y
```
Once the installation is complete, Enable and start the PostgreSQL services on Ubuntu 22.04 / 20.04
```sh
sudo systemctl enable postgresql
sudo systemctl start postgresql
```
Check the status:
```console
$ systemctl status postgresql
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: ena>
     Active: active (exited) since Fri 2022-11-25 18:15:04 EAT; 27min ago
   Main PID: 7383 (code=exited, status=0/SUCCESS)
        CPU: 1ms

Nov 25 18:15:04 ubuntu-22-lab systemd[1]: Starting PostgreSQL RDBMS...
Nov 25 18:15:04 ubuntu-22-lab systemd[1]: Finished PostgreSQL RDBMS.
```
Verify the Version installed:
```sh
#check version
sudo -u postgres psql -c "SELECT version();"
psql --version
#check connect
psql -h localhost -U postgres
```
The sample output will look like this.
From the output, PostgreSQL 15 has been installed in our system.
## Step 3: Connect to PostgreSQL 15 database
To connect to a PostgreSQL instance of a Database, you can employ two ways:-
#### 1 ) Running the Postgres command directly with the sudo command:
```console
$ sudo -u postgres psql
could not change directory to "/home/jil": Permission denied
psql (15.1 (Ubuntu 15.1-1.pgdg22.04+1))
Type "help" for help.

postgres=#
```
#### 2 ) Switching to the default Postgres user.
```console
$ sudo -i -u postgres
```
Now connect to the instance:
```conosle
$ psql
psql (15.1 (Ubuntu 15.1-1.pgdg22.04+1))
Type "help" for help.

postgres=#
```
In both ways, no password is required. This should not be allowed as it exposes your database to serious vulnerabilities.
## Step 4: Secure your PostgreSQL 15 Database with a password.
To administer your database without a password is a very bad practice. You need to ensure that an extra layer of security is added to your database. You need to set a password for your database. Run the following command:
```sql
postgres=# ALTER USER postgres PASSWORD 'Str0ngP@ssw0rd';
ALTER ROLE
postgres=#
```
For a Production environment, set a very strong password. To Verify that the password has been set successfully, terminate your current session then try to log in.
```postgres.console
postgres=# \q
```
Now connect to the database server.
```console
$ psql -h localhost -U postgres
Password for user postgres:<Str0ngP@ssw0rd>
psql (15.1 (Ubuntu 15.1-1.pgdg22.04+1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, compression: off)
Type "help" for help.

postgres=#
```
The password is successfully set.
## Step 5: Configure PostgreSQL 15 instance for Remote Access.
One of the cool features of PostgreSQL 15 is its ability to support multiple authentication methods. PostgreSQL uses roles to handle client authentication. The default authentication method for Postgres is ident authentication which associates the Postgres role to the UNIX system user.

The ident authentication method works by obtaining the client’s operating system user name from an ident server and using it as the allowed database user name (with an optional user name mapping). This method is only supported on TCP/IP connections.

All supported authentication methods supported are:
- **Ident** – supported via TCP/IP connections only. It obtains the client’s system username with an optional username mapping.
- **Password** – Connects using a password.
- **Peer** – Similar to ident but only supported on local connections.
- **Trust** – allows a role to connect as long as conditions defined in the pg_hba.conf are fulfilled.

By default, PostgreSQL will accept connections from the local host. To allow remote connections, we must make some modifications to the configuration files.

The default PostgreSQL configuration file is located on `/etc/postgresql/15/main/postgresql.conf`. This is the file that we will modify to allow us to configure remote access.

Using the text editor, edit the `listen_addresses` from localhost to  `‘*’` as shown below:

```conf
$ sudo vim /etc/postgresql/15/main/postgresql.conf
#------------------------------------------------------------------------------
# CONNECTIONS AND AUTHENTICATION
#------------------------------------------------------------------------------

# - Connection Settings -

listen_addresses = '*'          # what IP address(es) to listen on;
                                        # comma-separated list of addresses;
                                        # defaults to 'localhost'; use '*' for all
```
Make sure you enable the listen_addresses by removing the #. Save your configurations and exit from the file.

Allow password authentication on your PostgreSQL server by running the commands below.

```sh
sudo sed -i '/^host/s/ident/md5/' /etc/postgresql/15/main/pg_hba.conf
```
Next is to change the identification method from peer to trust as shown in the command below.
```sh
sudo sed -i '/^local/s/peer/trust/' /etc/postgresql/15/main/pg_hba.conf
```
Next, edit the file `/etc/postgresql/15/main/pg_hba.conf`. To allow the instance to be accessed from everywhere. Here you need to allow IPv4 connections from all clients.
```sh
sudo vim /etc/postgresql/15/main/pg_hba.conf
```
```conf '/etc/postgresql/15/main/pg_hba.conf'
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
host    all             all             0.0.0.0/0           scram-sha-256
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
host    all             all             0.0.0.0/0                md5
```

Save and exit from your editor.

Now restart and enable PostgreSQL for the changes to take effect. Execute the commands below.

```sh
sudo systemctl restart postgresql
sudo systemctl enable postgresql
```

## Step 6: Configure Firewall settings.
If your server is running behind a firewall, then you need to configure your firewall to enable communication with the outside world. Begin by checking the port at which PostgreSQL is listening. By default, PostgreSQL will listen on port 5432

```console
$ ss -tunelp | grep 5432
tcp   LISTEN 0      244            0.0.0.0:5432       0.0.0.0:*    uid:130 ino:64635 sk:11 cgroup:/system.slice/system-postgresql.slice/postgresql@15-main.service <->         
tcp   LISTEN 0      244               [::]:5432          [::]:*    uid:130 ino:64636 sk:17 cgroup:/system.slice/system-postgresql.slice/postgresql@15-main.service v6only:1 <->
```
We will need to allow port 5432 through the firewall.
```console
$ sudo ufw allow 5432/tcp
Rules updated
Rules updated (v6)
```
## Step 7: Managing users in PostgreSQL 15
Let me briefly demonstrate how you manage users in POstgreSQL 15.

Connect to the Postgres instance using the psql command.
```console
$ sudo -i -u postgres 
postgres@ubuntu-22-lab:~$ psql
psql (15.1 (Ubuntu 15.1-1.pgdg22.04+1))
Type "help" for help.
```
Create a superuser `admin` and his password `Passw0rd`
```sql
CREATE ROLE admin WITH LOGIN SUPERUSER CREATEDB CREATEROLE PASSWORD 'Passw0rd';
```
Verify that the superuser has been created with the appropriate privileges.
```
postgres=# \du

                                  List of roles
 Role name |                         Attributes                         | Member of 
-----------+------------------------------------------------------------+-----------
 admin     | Superuser, Create role, Create DB                          | {}
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}

postgres=# 
```
Create a sample Database.
```sql
CREATE DATABASE sampleDB;
```
Create a user with the name **demo_user** and assign it access to **sampleDB**
```sql
CREATE USER demo_user with encrypted password 'PassW0rd'
```
Grant all privileges on **sampleDB** to **demo_user**
```sql
GRANT ALL PRIVILEGES ON DATABASE sampleDB to demo_user;
```
## Step 8: Connecting to remote PostgreSQL 15 instance
Now, let’s try to access DB from a remote client. To do this we use the following syntax.
```sh
psql 'postgres://<username>:<password>@<host>:<port>/<db>?sslmode=disable'
```
For demonstration purposes, I will use my Oracle Linux 9 client. Run the command as shown below.
```
[jil@oracle-linux-9-lab ~]$ psql 'postgres://admin:Passw0rd@192.168.201.12:5432/postgres?sslmode=disable'
psql (15.1)
Type "help" for help.

postgres=# 
```
Alternatively, you could run the command as shown below.
```
[jil@oracle-linux-9-lab ~]$ psql -h 192.168.201.12 -U postgres
Password for user postgres: <Str0ngP@ssw0rd>
psql (15.1)
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, compression: off)
Type "help" for help.

postgres=# 
```
Bingo, we are able to access the PostgreSQL database from the remote client.