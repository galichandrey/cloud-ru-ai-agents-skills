# DeleteObject

Deletes an object from a bucket.

> **Source:** [DeleteObject — official cloud.ru docs](https://cloud.ru/docs/s3e/ug/topics/api__deleteobject)

Access to this method depends on the user's role. See [role management in Object Storage](https://cloud.ru/docs/s3e/ug/topics/security__role).

## Request

```http
DELETE /{bucket}/{key} HTTP/2
Host: s3.cloud.ru
```

## Parameters

### Path parameters

| Parameter | Description |
|-----------|-------------|
| `bucket`  | Bucket name |
| `key`     | Object key  |

### Query parameters

| Parameter   | Description                            |
|-------------|----------------------------------------|
| `versionId` | Reference to a specific object version |

## Headers

- See the [common request headers](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) for the full supported list.

## Example request

The request deletes the object `my-file-new.txt` from the bucket `my-bucket`:

```http
DELETE /my-bucket/my-file-new.txt HTTP/2
Host: s3.cloud.ru
Date: Wed, 25 Oct 2023 22:32:00 GMT
Authorization: authorization string
Content-Type: text/plain
```

## Example response

```http
HTTP/2 204 NoContent
x-amz-request-id: 0A49CE4060975EAC
Date: Wed, 25 Oct 2023 22:32:00 GMT
Content-Length: 0
Connection: close
Server: s3.cloud.ru
```

## Agent notes

- Success code: `204 No Content` with an empty body.
- `204` is returned even when deleting a non-existent key — deletion is idempotent; do not treat `204` as proof the object existed. Check beforehand with [HeadObject](head-object.md) if you need that.
- If the bucket has versioning enabled and no `versionId` is supplied, the latest version is deleted (per versioning rules); pass `?versionId=` to delete a specific version permanently.
- For deleting many objects in one call, see [DeleteObjects](delete-objects.md).
- Possible error responses are collected in the [response codes reference](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).
