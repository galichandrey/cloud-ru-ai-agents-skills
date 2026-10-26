# CreateBucket

Creates a bucket in Object Storage.

> **Source:** [CreateBucket — official cloud.ru docs](https://cloud.ru/docs/s3e/ug/topics/api__createbucket)

Access to this method depends on the user's role. See [role management in Object Storage](https://cloud.ru/docs/s3e/ug/topics/security__role).

## Request

```http
PUT /{bucket} HTTP/2
Host: s3.cloud.ru
```

## Parameters

### Path parameters

| Parameter | Description   |
|-----------|---------------|
| `bucket`  | Name of the bucket to create |

## Headers

- See the [common request headers](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) for the full supported list.
- `Content-Length: 0` — the request body is empty (the presence of a body is what distinguishes CreateBucket from [PutObject](put-object.md)).

## Example request

The request creates the bucket `my-bucket`:

```http
PUT /my-bucket HTTP/2
Host: s3.cloud.ru
Content-Length: 0
Date: Wed, 25 Oct 2023 22:32:00 GMT
Authorization: authorization string
```

## Example response

```http
HTTP/2 200 OK
x-amz-request-id: 236A8905248E5A01
Date: Wed, 25 Oct 2023 22:32:00 GMT
Location: /my-bucket
Content-Length: 0
Connection: close
Server: s3.cloud.ru
```

## Agent notes

- Success code: `200 OK` with an empty body; `Location` header echoes the created bucket path.
- `PUT /{bucket}` alone (empty body) is CreateBucket; the same verb to `PUT /{bucket}/{key}` is [PutObject](put-object.md). The difference is the key component and a non-empty body.
- `PUT /{bucket}/` with a trailing slash means `key` is empty — avoid this form; it is not CreateBucket per the official docs.
- Discover existing buckets with one `GET /` — [ListBuckets](list-buckets.md) — before creating, to avoid name collisions.
- A bucket can only be removed after it is empty: see [DeleteBucket](delete-bucket.md).
- Possible error responses are collected in the [response codes reference](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).
