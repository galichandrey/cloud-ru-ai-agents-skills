# DeleteObjects

Deletes objects from a bucket — a multi-object delete operation.

> **Source:** [DeleteObjects — official cloud.ru docs](https://cloud.ru/docs/s3e/ug/topics/api__deleteobjects)

Access to this method depends on the user's role. See [role management in Object Storage](https://cloud.ru/docs/s3e/ug/topics/security__role).

## Request

The official cloud.ru documentation describes this method as operating on the bucket:

```http
DELETE /{bucket} HTTP/2
Host: s3.cloud.ru
```

## Parameters

### Path parameters

| Parameter | Description |
|-----------|-------------|
| `bucket`  | Bucket name |

## Headers

- See the [common request headers](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) for the full supported list.
- The request needs to identify which objects to delete: the list of keys is passed in `Content-MD5`-signed XML format (the batch of `Object`/`Key`/`VersionId` elements), as in the Amazon S3 Multi-Object Delete API. The cloud.ru page does not show the request body example; refer to the official page for the current details.

## Example request

The request deletes objects from the bucket `my-bucket`:

```http
DELETE /my-bucket HTTP/2
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
- This is the batch operation counterpart of [DeleteObject](delete-object.md) — prefer it (or per-key parallel calls) when cleaning many keys listed by [ListObjectsV2](list-objects-v2.md).
- The exact request body/batch format is not exemplified on the cloud.ru page — verify against the official page and the S3 Multi-Object Delete convention before scripting.
- Possible error responses are collected in the [response codes reference](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).
