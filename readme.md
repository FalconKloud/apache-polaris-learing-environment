## Apache Polaris Educational Environment

The Purpose of this repository is to provide a set of tools and scripts to help you get started learning Apache Polaris.

## Environmental Variables

Rename `example.env` to `.env` and fill in the variables.

## /icebergdata

The `/icebergdata` is mapped to a shared volume, so all containers can access the data.

## Starting and Stopping the Environment

Assuming you have a working docker installation, you can start the environment by running:

```
docker compose up
```

You can stop the environment by running:

```
docker compose down
```

## Setting Up Polaris

You'll need to run some CURL commands to set up Polaris.

Keep an eye out on the terminal output for the polaris id and secret.

```
8ff628d1baa2d158:70260d78f8421b58123df5a899a02466
```

Use these credentials to make a post request to get an auth token.

``` 
curl -i -X POST \
  http://localhost:8181/api/catalog/v1/oauth/tokens \
  -d 'grant_type=client_credentials&client_id=3308616f33ef2cfe&client_secret=620fa1d5850199bc7628155693977bc1&scope=PRINCIPAL_ROLE:ALL'
```

Use this token to make a get request to create a catalog.

For S3:

```
curl -i -X POST -H "Authorization: Bearer principal:root;password:620fa1d5850199bc7628155693977bc1;realm:default-realm;role:ALL" -H 'Accept: application/json' -H 'Content-Type: application/json' \
  http://localhost:8181/api/management/v1/catalogs \
  -d '{"name": "polariscatalog", "type": "INTERNAL", "properties": {
        "default-base-location": "s3://my/s3/path"
    },"storageConfigInfo": {
        "roleArn": "arn:aws:iam::xxxxxxxxx:role/polaris-storage",
        "storageType": "S3",
        "allowedLocations": [
            "s3://my/s3/path"
        ]
    } }'
```

Using Local File System:

```
curl -i -X POST -H "Authorization: Bearer principal:root;password:620fa1d5850199bc7628155693977bc1;realm:default-realm;role:ALL" -H 'Accept: application/json' -H 'Content-Type: application/json' \
  http://localhost:8181/api/management/v1/catalogs \
  -d '{"name": "polariscatalog", "type": "INTERNAL", "properties": {
        "default-base-location": "file:///data"
    },"storageConfigInfo": {
        "storageType": "FILE",
        "allowedLocations": [
            "file:///data"
        ]
    } }'
```

Confirm Catalog Was Created:

```
curl -X GET "http://localhost:8181/api/management/v1/catalogs" \
     -H "Authorization: Bearer principal:root;password:620fa1d5850199bc7628155693977bc1;realm:default-realm;role:ALL" \
     -H "Accept: application/json"
```

Create a Principal

```
curl -X POST "http://localhost:8181/api/management/v1/principals" \
  -H "Authorization: Bearer principal:root;password:620fa1d5850199bc7628155693977bc1;realm:default-realm;role:ALL" \
  -H "Content-Type: application/json" \
  -d '{"name": "polarisuser", "type": "user"}'
```

Create a Principal Role

``` 
curl -X POST "http://localhost:8181/api/management/v1/principal-roles" \
  -H "Authorization: Bearer principal:root;password:620fa1d5850199bc7628155693977bc1;realm:default-realm;role:ALL" \
  -H "Content-Type: application/json" \
  -d '{"principalRole": {"name": "polarisuserrole"}}'
```

Assign Principal Role to Principal

```
curl -X PUT "http://localhost:8181/api/management/v1/principals/polarisuser/principal-roles" \
  -H "Authorization: Bearer principal:root;password:620fa1d5850199bc7628155693977bc1;realm:default-realm;role:ALL" \
  -H "Content-Type: application/json" \
  -d '{"principalRole": {"name": "polarisuserrole"}}'
```

Create a Catalog Role

```
curl -X POST "http://localhost:8181/api/management/v1/catalogs/polariscatalog/catalog-roles" \
  -H "Authorization: Bearer principal:root;password:620fa1d5850199bc7628155693977bc1;realm:default-realm;role:ALL" \
  -H "Content-Type: application/json" \
  -d '{"catalogRole": {"name": "polariscatalogrole"}}'
```

Assign Catalog Role to Principal Role

```
curl -X PUT "http://localhost:8181/api/management/v1/principal-roles/polarisuserrole/catalog-roles/polariscatalog" \
  -H "Authorization: Bearer principal:root;password:620fa1d5850199bc7628155693977bc1;realm:default-realm;role:ALL" \
  -H "Content-Type: application/json" \
  -d '{"catalogRole": {"name": "polariscatalogrole"}}'
```

Grant Catalog Role Privilege

```
curl -X PUT "http://localhost:8181/api/management/v1/catalogs/polariscatalog/catalog-roles/polariscatalogrole/grants" \
  -H "Authorization: Bearer principal:root;password:620fa1d5850199bc7628155693977bc1;realm:default-realm;role:ALL" \
  -H "Content-Type: application/json" \
  -d '{"grant": {"type": "catalog", "privilege": "CATALOG_MANAGE_CONTENT"}}'
```

Go to Notebook http://127.0.0.1:8888/lab

### Scratch

Use this to post your results as you run your curls

```
8ff628d1baa2d158:70260d78f8421b58123df5a899a02466
```

```
curl -i -X POST \
  http://localhost:8181/api/catalog/v1/oauth/tokens \
  -d 'grant_type=client_credentials&client_id=8ff628d1baa2d158&client_secret=70260d78f8421b58123df5a899a02466&scope=PRINCIPAL_ROLE:ALL'
```

```
{"access_token":"principal:root;password:70260d78f8421b58123df5a899a02466;realm:default-realm;role:ALL","scope":"PRINCIPAL_ROLE:ALL","token_type":"bearer","expires_in":3600}
```

```
{"principal":{"name":"polarisuser","clientId":"90e1c28f2ca495fb","properties":{},"createTimestamp":1732653943717,"lastUpdateTimestamp":1732653943717,"entityVersion":1},"credentials":{"clientId":"90e1c28f2ca495fb","clientSecret":"f71c0c2321009ec63a6f6c5228370b26"}}%  
```
