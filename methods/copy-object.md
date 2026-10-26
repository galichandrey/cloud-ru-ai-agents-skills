# CopyObject

Creates a copy of an object — either inside the current bucket or in another bucket. The copy operation is one request: the object data is not transferred through the client.

> **Source:** [CopyObject — official cloud.ru docs](https://cloud.ru/docs/s3e/ug/topics/api__copyobject)

Access to this method depends on the user's role. See [role management in Object Storage](https://cloud.ru/docs/s3e/ug/topics/security__role).

## Request

```http
PUT /{bucket}/{key} HTTP/2
Host: s3.cloud.ru
```

## Parameters

### Path parameters

| Parameter | Description   |
|-----------|---------------|
| `bucket`  | Destination bucket name |
| `key`     | Destination object key  |

### Required header

| Header              | Description                                                        |
|---------------------|--------------------------------------------------------------------|
| `x-amz-copy-source` | Source object in the format `/source-bucket/source-object`         |

## Headers

- `x-amz-copy-source` is required — it names the copy source (e.g. `/my-bucket/my-file.txt`).
- See the [common request headers](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) for the full supported list.

## Example request

The request copies the object `my-file.txt` in the bucket `my-bucket` under the name `my-file-new.txt`:

```http
PUT /my-bucket/my-file-new.txt HTTP/2
Host: s3.cloud.ru
Date: Wed, 25 Oct 2023 22:32:00 GMT
x-amz-copy-source: /my-bucket/my-file.txt
Authorization: authorization string
```

## Example response

```http
HTTP/2 200 OK
x-amz-request-id: 318BC8BC148832E5
x-amz-copy-source-version-id: 3/L4kqtJlcpXroDTDmJ+rmSpXd3dIbrHY+MTRCxf3vjVBH40Nr8X8gdRQBpUMLUo
x-amz-version-id: QUpfdndhfd8438MNFDN93jdnJFkdmqnh893
Date: Wed, 25 Oct 2023 22:32:00 GMT
Connection: close
Server: s3.cloud.ru

<CopyObjectResult>
   <LastModified>2023-10-01T17:50:30.000Z</LastModified>
   <ETag>"9b2cf535f27731c974343645a3985328"</ETag>
</CopyObjectResult>
```

## Agent notes

- Same HTTP verb as [PutObject](put-object.md), but the presence of `x-amz-copy-source` makes it a server-side copy — no request body with object data.
- `x-amz-copy-source-version-id` in the response identifies the source version that was copied; `x-amz-version-id` identifies the created copy.
- To "move" an object, copy it and then delete the source with [DeleteObject](delete-object.md).
- Possible error responses are collected in the [response codes reference](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).
