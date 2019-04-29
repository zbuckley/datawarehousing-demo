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
2. Experiment with the sample Queries below:

#### CRUD Operations

Create, Read, Update, and Delete operations covered in this section:

As a *Read* operation, lets view the first 10 records in travel sample.
*NOTE: throughout the N1QL queries we'll need to use back-ticks in order refer to travel-sample*

```SQL
SELECT * FROM `travel-sample` LIMIT 10;
```

You can view the results in different formats, by selecting a different tab
in the results area of the editor. Select `Table` for example.

Lots of airlines... As another *Read* operation, lets see if there are other types:

```SQL
SELECT * FROM `travel-sample` WHERE type != 'airline' LIMIT 10;
```

Interesting.. Can we get a list of all the different types (*Read* Operation)?

```SQL
SELECT DISTINCT type FROM `travel-sample`;
```

How many airlines are in our bucket (*Read* Operation)?

```SQL
SELECT COUNT(name) FROM `travel-sample` WHERE type == 'airline';
```

Let's look for an airline to delete (*Read* Operation).
*NOTE: META().id to show the primary key for the documents*

```SQL
SELECT name, id, META().id as document_key FROM `travel-sample` WHERE type == 'airline';
```

Let's go for... Locair. First, here's a query by primary key for the airline document (*Read* Operation)

```SQL
SELECT * FROM default WHERE META().id = "airline_10748";
```

Ok, our first *Delete* operation, let's delete Locair airline.
We'll use the `RETURNING` clause to get back the contents of the airline document,
and it's primary key, as we delete it.

```SQL
DELETE FROM `travel-sample` WHERE type == 'airline' AND name == 'Locair' RETURNING *, META().id as document_key;
```

Using the primary Key query above, lets verify that the document is no longer in no longer in the database.
*NOTE: While the query below does work, according to the [N1QL Docs](https://docs.couchbase.com/server/4.1/developer-guide/retrieving.html), it is typically recommended to use the key-value api when the key is known*

```SQL
SELECT * FROM `travel-sample` WHERE META().id = "airline_10748";
```

As our first *Create* Operation, let's see how we would put Locair's airline record back into the database:
*NOTE: To restore the document, and maintain relationships between this document and others, we need to
ensure that the key we insert the document under, comes from the deletion output above.*

```SQL
INSERT INTO `travel-sample` (KEY, VALUE)
VALUES(
    "airline_10748",
    {  "callsign": "LOCAIR",
      "country": "United States",
      "iata": "ZQ",
      "icao": "LOC",
      "id": 10748,
      "name": "Locair",
      "type": "airline"
    }
) RETURNING META().id as document_key, *;
```

Let's check that Locair is back in the system (*Read* Operation):

```SQL
SELECT * FROM `travel-sample` WHERE META().id = "airline_10748";
```

Now, as our first *Update* operation, lets update LOCAIR's country, by replacing the whole document:

```SQL
UPSERT INTO `travel-sample` (KEY, VALUE)
VALUES(
    "airline_10748",
    {  "callsign": "LOCAIR",
      "country": "Australia",
      "iata": "ZQ",
      "icao": "LOC",
      "id": 10748,
      "name": "Locair",
      "type": "airline"
    }
) RETURNING META().id as document_key, *;
```

Let's check that the update occurred (*Read* Operation):

```SQL
SELECT * FROM `travel-sample` WHERE META().id = "airline_10748";
```

Now, we'll change all airlines with country = Australia to AU... but lets do that,
without replacing all the documents. Instead our *Update* operation should only change
the value we care about in each document:

```SQL
UPDATE `travel-sample`
SET country = "AU"
WHERE type = "airline"
AND country = "Australia"
RETURNING META().id as document_key, *;
```

We can see from the return information, that Locair was updated... and appears to have
been the only document where `country = 'AU'`. For completeness, Let's check that
using our *Read* operation from above, shows Locair being updated.

```SQL
SELECT * FROM `travel-sample` WHERE META().id = "airline_10748";
```
