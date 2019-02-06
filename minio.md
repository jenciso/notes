## Minio trace - debug 
``` 
export MINIO_HTTP_TRACE=1
```
or in your docker-compose.yml

```
  minio:
    container_name: minio
    restart: "no"
    networks:
      default:
        aliases:
          - public.minio
    image: minio/minio:latest
    ports:
      - 9000:9000
    environment:
      MINIO_ACCESS_KEY: minio
      MINIO_SECRET_KEY: minio123
      MINIO_HTTP_TRACE: 1
    command: server /data
``` 

## minio version

```
docker run --rm minio/minio version
```
## minio and drupal config

compose.yml
```
...
  minio:
    container_name: minio
    restart: always
    networks:
      default:
        aliases:
          - bor.minio
    image: minio/minio:latest
    ports:
      - 9000:9000
    environment:
      MINIO_ACCESS_KEY: minio
      MINIO_SECRET_KEY: minio123
    command: server /data
...
```

settings.php

```
// create public and private buckets
// create etc/host record for bor.minio as defined in docker-compose file and within these configs

$config['s3fs.settings']['access_key'] = 'minio'; // Your keys
$config['s3fs.settings']['secret_key'] = 'minio123'; // Your keys

$config['s3fs.settings']['bucket'] = 'bor'; // sets drupal to pull assets from bor.$config['s3fs.settings']['hostname'] if no_rewrite_cssjs is set to true
$config['s3fs.settings']['region'] = 'eu-west-1'; // Your region (doesn't really matter if using minio, just set it to something)

$config['s3fs.settings']['use_https'] = FALSE;
$config['s3fs.settings']['no_rewrite_cssjs'] = TRUE;

$config['s3fs.settings']['use_customhost'] = TRUE;
$config['s3fs.settings']['hostname'] = "minio:9000";

//$config['s3fs.settings']['use_cname'] = TRUE; // I'm not using this
//$config['s3fs.settings']['domain'] = 'bor.minio'; // I'm not using this

// public and private folders
$settings['s3fs.use_s3_for_public'] = TRUE;
$settings['s3fs.use_s3_for_private'] = FALSE;

// create these bucket names in minio
$config['s3fs.settings']['public_folder'] = 'bor-public'; // Your public directories in S3 (name of the bucket in minio)
$config['s3fs.settings']['private_folder'] = 'bor-private'; // Your private directories in S3 (name of the bucket in minio)

``` 

Source: https://github.com/minio/minio/issues/4914


## Create minio service using helm into kubernetes cluster

```
helm install --set serviceType=NodePort --name storage-demo --namespace=default stable/minio
```

## Delete 

```
helm delete --purge storage-demo
``` 

## set keys
```
helm install --set accessKey=myaccesskey,secretKey=mysecretkey stable/minio
``` 

### Upgrade

```
helm update
helm upgrade --force storage-realiza stable/minio 
``` 

## Provision minio storages using helm

* Create dynamic `MINIO_SECRET_KEY` and `MINIO_ACCESS_KEY`

Using random pass generator: 
```
date | md5sum | head -c 16 | awk '{ print toupper($0) }'
export MINIO_ACCESS_KEY=$(date | md5sum | head -c 16 | awk '{ print toupper($0) }')
export MINIO_SECRET_KEY=$(date | md5sum | head -c 16 | awk '{ print toupper($0) }')
```
 
* Create a simple minio

```
	helm install --set accessKey=$MINIO_ACCESS_KEY,secretKey=$MINIO_SECRET_KEY stable/minio
```

* Create a custom minio service

```
	helm install --set serviceType=NodePort --name storage-test --namespace=default \
	--set accessKey=$MINIO_ACCESS_KEY,secretKey=$MINIO_SECRET_KEY \
	stable/minio
```

* Delete that test

```
	helm del --purge storage-test
```

## Kubernetes
https://docs.minio.io/docs/deploy-minio-on-kubernetes
