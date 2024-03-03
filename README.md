# Master-Slave-Replication-with-MariaDB
This tutorial guides you through the process of setting up Master-Slave replication with MariaDB on two Amazon EC2 instances running Amazon Linux 2.

## Prerequisites

- Two EC2 instances with Amazon Linux 2.

## MariaDB Master-Slave Replication Setup

### Step 1: Install MariaDB on Master Instance

```bash
sudo yum install mariadb-server
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

### Step 2: Secure MariaDB Installation

```bash
sudo mysql_secure_installation
```

### Step 3: Configure Master Instance

Edit MariaDB configuration file

```bash
sudo nano /etc/my.cnf.d/server.cnf
```

Add the following lines:

```cnf
server-id = 1
log_bin = /var/log/mariadb/mariadb-bin
```

Restart MariaDB

```bash
sudo systemctl restart mariadb
```

Create replication user

```sql
CREATE USER 'repl'@'%' IDENTIFIED BY 'your_password';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
```

Obtain Master Log File and Position

```sql
SHOW MASTER STATUS;
Step 4: Install MariaDB on Slave Instance
```

Repeat Step 1 on the second EC2 instance.

### Step 5: Configure Slave Instance

Edit MariaDB configuration file

```bash
sudo nano /etc/my.cnf.d/server.cnf
```

Add the following lines:

```cnf
server-id = 2
```

Restart MariaDB

```bash
sudo systemctl restart mariadb
```

Configure Slave Replication

On the Slave instance:

```sql
CHANGE MASTER TO
  MASTER_HOST='master_instance_ip',
  MASTER_USER='repl',
  MASTER_PASSWORD='your_password',
  MASTER_LOG_FILE='master_log_file_from_master',
  MASTER_LOG_POS=master_log_position_from_master;
```

### Step 6: Start Slave Replication

On the Slave instance:

```sql
START SLAVE;
```

### Step 7: Verify Replication

On the Slave instance:

```sql
SHOW SLAVE STATUS\G
```

Ensure Slave_IO_Running and Slave_SQL_Running are both Yes.

### Testing Replication

Create a database on the Master:

```sql
CREATE DATABASE testdb;
```

Verify the database is replicated to the Slave:

```sql
SHOW DATABASES;
```

Ensure testdb is listed on the Slave.
