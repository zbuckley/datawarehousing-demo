# Data Warehousing Demo

Our group focused on the Couchbase Database

## Install and Setup

This is based on a linux system as we decided our demo would be ran Zach Buckley's laptop (running Ubuntu 18.04.01 LTS)

### Docker Installation and Setup

```bash
sudo apt install docker
sudo usermod -aG docker zbuckley
```

### Download and Run Couchbase Container

Execute the provided script for convenience:
```bash
./docker_image_start
```

-- OR --

alternatively run the command directly:

```bash
docker run -d --name db -p 8091-8094:8091-8094 -p 11210:11210 couchbase:community
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

## Sample Bucket Setup

1. Open a web browser to the following address `https://localhost:8091`
2. Select `Settings` either on the left, or from top-right menu
3. Select `Sample Buckets` Tab
4. Select `travel-sample`
5. Select `Load Sample Data`

## Travel Sample Exploration

1. Open a web browser to the following address `https://localhost:8091`
2. Select `Buckets`
3. Select `travel-sample` Bucket
4.
