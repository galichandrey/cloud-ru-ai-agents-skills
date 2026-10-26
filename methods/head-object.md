# HeadObject

Returns information (metadata) about an object. Like [GetObject](get-object.md) it gives access to the object's data, but does not download the object body itself.

> **Source:** [HeadObject — official cloud.ru docs](https://cloud.ru/docs/s3e/ug/topics/api__headobject)

Access to this method depends on the user's role. See [role management in Object Storage](https://cloud.ru/docs/s3e/ug/topics/security__role).

## Request

```http
HEAD /{bucket}/{key}?partNumber=PartNumber&versionId=VersionId HTTP/2
Host: s3.cloud.ru
```

Note: both query parameters are optional; in the simple case the request is `HEAD /{bucket}/{key}`.

## Parameters

### Path parameters

| Parameter | Description |
|-----------|-------------|
| `key`     | Object key  |

### Query parameters

| Parameter    | Description                                |
|--------------|--------------------------------------------|
| `partNumber` | Number of the part of the object being read|
| `versionId`  | Reference to a specific object version     |

## Headers

- See the [common request headers](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) for the full supported list.

## Example request

The request returns metadata of the object `my-file.txt` from the bucket `my-bucket`:

```http
HEAD /my-bucket/my-file-new.txt HTTP/2
Host: s3.cloud.ru
Date: Wed, 25 Oct 2023 22:32:00 GMT
Authorization: authorization string
```

## Example response

```http
HTTP/2 200 OK
x-amz-request-id: 318BC8BC143432E5
x-amz-version-id: 3HL4kqtJlcpXroDTDmjVBH40Nrjfkd
Date: Wed, 25 Oct 2023 22:32:00 GMT
Last-Modified: Sun, 4 Jun 2023 12:00:00 GMT
ETag: "fba9dede5f27731c9771645a39863328"
Content-Length: 434234
Content-Type: text/plain
Connection: close
Server: s3.cloud.ru
```

## Agent notes

- Success code: `200 OK` with an empty body; all useful data is in the headers (`Last-Modified`, `ETag`, `Content-Length`, `Content-Type`, `x-amz-version-id`).
- Use this method to check existence, size, or type of an object without transferring the payload.
- `ETag` is an opaque checksum — compare it against the `ETag` from [PutObject](put-object.md) or [CopyObject](copy-object.md) to detect changes.
- `?partNumber` lets you inspect metadata of a single part of a multipart-uploaded object.
- Possible error responses are collected in the [response codes reference](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).
