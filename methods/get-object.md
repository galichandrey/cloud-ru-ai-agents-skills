# GetObject

Retrieves an object from a bucket. If no object version is specified, the latest version of the object is downloaded.

> **Source:** [GetObject — official cloud.ru docs](https://cloud.ru/docs/s3e/ug/topics/api__getobject)

Access to this method depends on the user's role. See [role management in Object Storage](https://cloud.ru/docs/s3e/ug/topics/security__role).

## Request

```http
GET /{bucket}/{key} HTTP/2
Host: s3.cloud.ru
```

## Parameters

### Path parameters

| Parameter | Description |
|-----------|-------------|
| `bucket`  | Bucket name |
| `key`     | Object key |

### Query parameters

| Parameter   | Description                            |
|-------------|----------------------------------------|
| `versionId` | Reference to a specific object version |

## Headers

- See the [common request headers](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) for the full supported list.

## Example request

The request returns the object `my-file.txt` from the bucket `my-bucket`:

```http
GET /my-bucket/my-file.txt HTTP/2
Host: s3.cloud.ru
Date: Wed, 25 Oct 2023 22:32:00 GMT
Authorization: authorization string
```

## Example response

The official documentation page does not provide a response example. The response body is the raw object data itself; response headers (`Last-Modified`, `ETag`, `Content-Length`, `Content-Type`, etc.) carry object metadata — see [HeadObject](head-object.md) for a representative header set returned for the same object.

## Agent notes

- Success code: `200 OK`; the message body is the object content (not metadata).
- The response to `GET` contains the same metadata headers as `HEAD` — if you only need metadata, use [HeadObject](head-object.md) instead to avoid downloading the object.
- If versioning is enabled, pass `?versionId=<version>` to read a specific version; omit it to get the latest version.
- To read `x-amz-version-id`-style details for a version you plan to delete later, keep the version id and use [DeleteObject](delete-object.md) with `versionId`.
- Possible error responses are collected in the [response codes reference](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).
