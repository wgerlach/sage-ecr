# ECR API specification

Please define these variables to be able to copy-and-paste the curl examples below.

```bash
export ECR_API="localhost:5000"
export SAGE_USER_TOKEN="token1"
export APP_NAMESPACE="sage"
export APP_REPOSITORY="simple"
```


## POST /submit
```bash
curl -X POST ${ECR_API}/submit -H "Authorization: sage ${SAGE_USER_TOKEN}" --data-binary  @./example_app.yaml 
curl -X POST ${ECR_API}/submit -H "Authorization: sage ${SAGE_USER_TOKEN}" -d '{...}'
```

Input can be either JSON or YAML format.


Example repsonse:
```json5
{
  "arguments": "", 
  "baseCommand": "", 
  "depends_on": "", 
  "description": "very important app", 
  "id": "f59a7edf-8ca3-4557-83be-e3e1f60dee38", 
  "inputs": [
    {
      "id": "speed", 
      "type": "int"
    }
  ], 
  "metadata": {
    "my-science-data": 12345
  }, 
  "name": "simple", 
  "namespace": "sage", 
  "owner": "testuser", 
  "resources": [
    {
      "min_resolution": "600x800", 
      "type": "RGB_image_producer", 
      "view": "top"
    }
  ], 
  "source": 
    {
      "architectures": [
        "linux/arm/v7"
      ], 
      "branch": "master", 
      "directory": "plugin-simple", 
      "dockerfile": "Dockerfile_sage", 
      "name": "armv7", 
      "url": "https://github.com/waggle-sensor/edge-plugins.git"
    }, 
  "version": "1.0"
}
```

## GET /apps
List namespaces:
```bash
curl ${ECR_API}/apps -H "Authorization: sage ${SAGE_USER_TOKEN}"
```

Example repsonse:
```json5
[
  {
    "id": "sage", 
    "owner_id": "testuser"
  }
]
```


## PUT /apps/
Create namespace:
```bash
curl ${ECR_API}/apps -d "{\"id\":\"${APP_NAMESPACE}\"}" -H "Authorization: sage ${SAGE_USER_TOKEN}"
```

Example repsonse:
```json5
{
  "id": "testtest", 
  "owner_id": "testuser"
}
```

## GET /apps/{namespace}
List repositories in namespace:
```bash
curl ${ECR_API}/apps/${APP_NAMESPACE} -H "Authorization: sage ${SAGE_USER_TOKEN}"
```

Example repsonse:
```json5
{
  "id": "sage", 
  "owner_id": "testuser", 
  "repositories": [
    {
      "name": "simple", 
      "namespace": "sage", 
      "owner_id": "testuser"
    }
  ]
}
```


## GET /apps/{namespace}/{repository}
List all versions in repository:
```bash
curl ${ECR_API}/apps/${APP_NAMESPACE}/${APP_REPOSITORY} -H "Authorization: sage ${SAGE_USER_TOKEN}"
```

Example repsonse:
```json5
[
  {
    "id": "b4006eb1-8435-4e64-b11b-0984563f4946", 
    "name": "simple", 
    "namespace": "sage", 
    "version": "1.0"
  }
]
```

## GET /permissions/{namespace}/{repository}
Show permissions for repository
```bash
curl ${ECR_API}/permissions/${APP_NAMESPACE}/${APP_REPOSITORY} -H "Authorization: sage ${SAGE_USER_TOKEN}"
```
Example repsonse:
```json5
[
  {
    "grantee": "testuser", 
    "granteeType": "USER", 
    "permission": "FULL_CONTROL", 
    "resourceName": "sage/simple", 
    "resourceType": "repository"
  }
]
```

## PUT /permissions/{namespace}/{repository}
Make repository public:
```bash
curl -X PUT ${ECR_API}/permissions/${APP_NAMESPACE}/${APP_REPOSITORY} -H "Authorization: sage ${SAGE_USER_TOKEN}" -d '{"granteeType": "GROUP", "grantee": "AllUsers", "permission": "READ"}'
```

Example repsonse:
```json5
{
  "added": 1
}
```


Make repository public:
```bash
curl -X PUT ${ECR_API}/permissions/${APP_NAMESPACE}/${APP_REPOSITORY} -H "Authorization: sage ${SAGE_USER_TOKEN}" -d '{"granteeType": "USER", "grantee": "OtherUser", "permission": "READ"}'
```

Example repsonse:
```json5
{
  "added": 1
}
```



## POST /builds/{namespace}/{repository}/{version}
trigger build for specific app
```bash
curl -X POST ${ECR_API}/builds/sage/simple/1.0 -H "Authorization: sage token1"
```

## GET /builds/{namespace}/{repository}/{version}
Get build status

```bash
curl -X GET ${ECR_API}/builds/sage/simple/1.0 -H "Authorization: sage token1"
```

Example repsonse:
```json5
...
  "queueId": 1, 
  "result": "SUCCESS", 
  "timestamp": 1602797073592, 
...
```

