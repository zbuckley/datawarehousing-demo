# Data Warehousing Demo

Our group focused on the Couchbase Database

## Install and Setup

This is based on a linux system as we decided our demo would be ran Zach Buckley's laptop (running Ubuntu 18.04.01 LTS)

### Docker Installation and Setup

```bash
sudo apt install docker
sudo usermod -aG docker zbuckley
```

*A restart will be necessary after the usermod comand is ran*

### Container Management

This section describes the process of starting and managing the container on your
local system.

#### Downloading/Creating/Starting Docker Container

```bash
docker run -d --name db -p 8091-8094:8091-8094 -p 11210:11210 couchbase:community
```

Or, use the convenient script provided:

```bash
./docker_image_start
```

#### Stopping the Docker container

```bash
docker stop db
```

Or, use the convenient script provided:

```bash
./docker_image_stop
```

#### Delete the Docker container

*NOTE: Deleting the container, also deletes data and configuration settings*

```bash
docker rm db
```

Or, use the convenient script provided:

```bash
./docker_image_delete
```

#### Restart the Docker Container

*Note: This assumes you've created the container, and stopped it.*

```bash
docker start db
```

Or, use the convenient script provided:

```bash
./docker_image_restart
```

## Cluster Setup

1. Open a web browser to the following address: `http://localhost:8091`
2. Startup screen should say `Couchbase Server`, and `Community Edition 6.0.0 build 1693`. The exact build number shouldn't really matter that much.
3. Select `Setup New Cluster`
4. Enter Cluster Name: `rocinante`
5. Change Admin Username: `jholden`
6. Set the password: `miller`
7. Select `Next: Accept Terms`
8. Accept the Terms
9. Select `Finish With Defaults`

## Sample Bucket

1. Open a web browser to the following address `http://localhost:8091`
2. Select `Settings` either on the left, or from top-right menu
3. Select `Sample Buckets` Tab
4. Select `travel-sample`
5. Select `Load Sample Data`

### Bucket Management Exploration

1. From the main menu, select `Buckets`
2. Select `travel-sample` Bucket, by clicking on the bucket Name
3. See:

  * Number of Items
  * Memory Usage
  * Disk Usage

4. Select Statistics from the right side:
5. See:

  * Lots of stats for helping to diagnose issues/monitor usage levels

### Queries (N1QL, SQL-like)

Complete Reference for [N1QL Query Language](https://docs.couchbase.com/server/4.0/n1ql/n1ql-language-reference/index.html).

1. From the main menu, select `Query` to open the `Query Editor`
2. Example Queries:

```SQL
SELECT * FROM `travel-sample` LIMIT 10;
```

You can view the results in different formats, Select `Table`
Lots of airlines, do we have any other types?

```SQL
SELECT * FROM `travel-sample` WHERE type != 'airline' LIMIT 10;
```

Can we list all the unique types?

```SQL
SELECT DISTINCT type FROM `travel-sample`;
```

How many airlines are in the bucket?

```SQL
SELECT COUNT(name) FROM `travel-sample` WHERE type == 'airline';
```

Let's pick an airline to delete:

```SQL
SELECT name, id FROM `travel-sample` WHERE type == 'airline';
```

ok... 40-Mile Air... lets delete it

```SQL
DELETE FROM `travel-sample` WHERE type == 'airline' AND name == '40-Mile Air' RETURNING *
```

redo the name, id query from above to confirm deletion
Now, lets add it back in...
TODO
