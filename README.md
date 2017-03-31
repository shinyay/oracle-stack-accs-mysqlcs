# Provisioning with Oracle Cloud Stack Manager for Oracle Application Container Cloud Service and MySQL Cloud Service

![](docs/images/accs_with_mysql.png)

In the sample of [oracle-accs-jersey-mysql
](https://github.com/shinyay/oracle-accs-jersey-mysql), I have manually composed **Application Container Cloud Servcie** and **MySQL Cloud Service**.

In this sample, a set of correlated Cloud Serivices, like ACCS and MySQLCS, is composed at ones with **Cloud Stack Manager**

## Description
**Oracle Cloud Stack Manager** is a feature that enables you to ***automate the provisioning of multiple cloud services*** as ***a single unit called a stack***.

## Demo
![](docs/images/stack-for-accs-mysqlcs.gif)

## Features
- Create Application Container Cloud Service and MySQL Cloud Service at once

## Requirement
- PaaS Service Manager Command Line (psm cli)
  - [Downloading the Command Line Interface](https://docs.oracle.com/en/cloud/paas/java-cloud/pscli/downloading-command-line-interface.html)

## Usage
### 1. Validate a stack template
This command validates YAML syntax.

- **psm stack validate-template -f [Stack Template]**
  - psm stack validate-template -f pokemon-stack.yaml

### 2. Import a stack template
This command imports the template into Cloud Stack Manager.

- **psm stack import-template -f [Stack Template]**
  - psm stack import-template -f pokemon-stack.yaml

### 3. Create a cloud stack
This command creates a cloud stack from the stack template with some necessary parameters.

- **psm stack create -n [Stack Name] -t [Stack Template Name] -d [Description] -p [Parameter KEY:VALUE]**
  - psm stack create -n PokemonAppStack -t Pokemon-Stack -d "Pokemon Encyclopedia App Stack" -p publicKeyText:"`cat ~/.ssh/id_rsa.pub`"

## Installation
Following steps show how to import Application Data into MySQL:

### 1. Copy csv into MySQL server node
`pokemon.csv` is application data csv file. I copy it to MySQL server node with SCP:

```
$ scp pokemon.csv opc@<MYSQL_SERVER_IP_ADDRESS>:/home/opc/
```

You can find MySQL server address in Cloud Service Console.

### 2. Login to MySQL server node

```
$ ssh opc@<MYSQL_SERVER_IP_ADDRESS>
********************************************************************************
*                                 Welcome to                                   *
*                             MySQL Cloud Service                              *
*                                     by                                       *
*                                   Oracle                                     *
*       If you are an unauthorised user please disconnect IMMEDIATELY          *
********************************************************************************
******************************* MySQL Information ******************************
* Status:  RUNNING                                                             *
* Version:   5.7.17                                                            *
********************************************************************************
************************** Storage Volume Information **************************
* Volume      Used             Use%           Available   Size     Mounted on  *
* MySQLlog    6.1G ---- 11%                         50G    59G   /u01/translog *
* bin         2.6G ------- 28%                     6.7G   9.8G   /u01/bin      *
* data        122M -- 1%                            24G    25G   /u01/data     *
********************************************************************************
```

### 3. Change owner permission to csv as Oracle User
MySQL is run with `oracle` user. I change owner into **`oracle`**

```
$ sudo cp pokemon.csv /tmp
$ sudo chown oracle:oracle /tmp/pokemon.csv
```

### 4. Connect to MySQL Database
Default database is created as `mydatabase`. You can connect to it with the following command:

```
$ sudo su - oracle
$ mysql -u root -D mydatabase -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 19
Server version: 5.7.17-enterprise-commercial-advanced-log MySQL Enterprise Server - Advanced Edition (Commercial)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

- Password is set statically in stack template as `P@ssw0rd`

### 5. Create a Table for Application
You create a table with the following query:

```sql
create table pokemon ( id INT(11), name_j VARCHAR(32), name_e VARCHAR(32), hp INT(11), atk INT(11), def INT(11), sat INT(11), sde INT(11), agl INT(11), total INT(11), type VARCHAR(32) );
```

```
mysql> create table pokemon ( id INT(11), name_j VARCHAR(32), name_e VARCHAR(32), hp INT(11), atk INT(11), def INT(11), sat INT(11), sde INT(11), agl INT(11), total INT(11), type VARCHAR(32) );
Query OK, 0 rows affected (0.28 sec)
```

### 5. Load data from CSV for Application
```sql  
LOAD DATA LOCAL INFILE '/tmp/pokemon.csv' INTO TABLE pokemon FIELDS TERMINATED BY ',' LINES TERMINATED BY "\r\n";
```

```
mysql> LOAD DATA LOCAL INFILE '/tmp/pokemon.csv' INTO TABLE pokemon FIELDS TERMINATED BY ',' LINES TERMINATED BY "\r\n";
Query OK, 802 rows affected (0.30 sec)
Records: 802  Deleted: 0  Skipped: 0  Warnings: 0
```

## Licence

Released under the [MIT license](https://gist.githubusercontent.com/shinyay/56e54ee4c0e22db8211e05e70a63247e/raw/44f0f4de510b4f2b918fad3c91e0845104092bff/LICENSE)

## Author

[shinyay](https://github.com/shinyay)
