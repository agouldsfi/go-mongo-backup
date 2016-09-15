# go-mongo-backup
---

## Description

This application runs alongside Mongo to create a simple API endpoint that can be called for backup operations.

## Example

```bash
curl -XPOST 'http://localhost:8080/v0/dump' -d \
'{
    "bucket": "org-mongodb-snapshots",
    "path": "/dev/"
}'
```

This creates a timestamped file in your S3 bucket at the path you specify. This file is compatible with `mongorestore`, using the `--archive` option

## Motivation

I wanted to be able to use a Lambda scheduled job to back up Mongo to S3, but using mongodump in python required more than 1500MB of memory on my dataset and was very slow.

## Installation

If mongo is running in another container:

```bash
docker pull openwhere/go-mongo-backup
docker run --name mongo-backup --link mongo -d -p 8080:8080 openwhere/go-mongo-backup
```

If mongo is running as a native package on the host:

```bash
docker pull openwhere/go-mongo-backup
docker run --name mongo-backup -d -p 8080:8080 \
  -e "MONGOHOST=$(ifconfig | grep -E '([0-9]{1,3}\.){3}[0-9]{1,3}' | grep -v 127.0.0.1 | awk '{ print $2 }' | cut -f2 -d: | head -n1)" \
  openwhere/go-mongo-backup

```
This sets the mongo address to the IP of the Docker host.

## API Reference

To start a backup:  
`POST` /v0/dump
```json
{
    "bucket": "mybucket",
    "path": "/path/to/backup/dir/"
}
```

## Lambda build
1.  Test the lambda function
        python lambda/mongo-backup/mongo-snapshot.py
2.  create zip
        ./lambdabuild.sh
3.  Create Lambda function using zip file. (Automated in pod config)

## Tests

ToDo

## Contributions

Pull requests accepted.

## License

MIT
