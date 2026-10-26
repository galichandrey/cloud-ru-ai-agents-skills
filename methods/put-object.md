# PutObject

Adds (uploads) an object to a bucket. Recreating an object with the same key replaces its previous version.

> **Source:** [PutObject — official cloud.ru docs](https://cloud.ru/docs/s3e/ug/topics/api__putobject)

Access to this method depends on the user's role. See [role management in Object Storage](https://cloud.ru/docs/s3e/ug/topics/security__role).

## Request

```http
PUT /{bucket}/{key} HTTP/2
Host: s3.cloud.ru
```

## Parameters

### Path parameters

| Parameter | Description |
|-----------|-------------|
| `bucket`  | Bucket name |
| `key`     | Object key |

### Query parameters

| Parameter  | Description                        |
|------------|------------------------------------|
| `versionId`| Reference to a specific object version |

## Headers

- See the [common request headers](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) for the full supported list.
- Typical and useful ones for this method:
  - `Content-Type` — MIME type of the object
  - `Content-Length` — object size in bytes
  - `Expect: 100-continue` — recommended before uploading large objects
  - `x-amz-meta-*` — custom user metadata (e.g. `x-amz-meta-author: Author`)

## Example request

The request adds the object `my-file.txt` to the bucket `my-bucket`:

```http
PUT /my-bucket/my-file.txt HTTP/2
Host: s3.cloud.ru
Date: Wed, 25 Oct 2023 22:32:00 GMT
Authorization: authorization string
Content-Type: text/plain
Content-Length: 11434
x-amz-meta-author: Author
Expect: 100-continue

[11434 bytes of object data]
```

## Example response

```http
HTTP/2 100 Continue

HTTP/2 200 OK
x-amz-request-id: 318BC8BC148832E5
x-amz-version-id: QUpfdndhfd8438MNFDN93jdnJFkdmqnh893
Date: Wed, 25 Oct 2023 22:32:00 GMT
ETag: "1b2cf535f27731c974343645a3985328"
Content-Length: 0
Connection: close
Server: s3.cloud.ru
```

## Agent notes

- Success codes: `100 Continue` (interim) followed by `200 OK`; the body is empty.
- `ETag` in the response is the MD5-like opaque checksum of the stored object — use it to verify upload integrity / detect changes.
- If versioning is enabled on the bucket, the response includes `x-amz-version-id`; save it if you need to reference or restore this version later.
- To copy an existing object instead of uploading new data, use [CopyObject](copy-object.md).
- To read an object back, use [GetObject](get-object.md); to check metadata without downloading, use [HeadObject](head-object.md).
- Possible error responses are collected in the [response codes reference](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).
