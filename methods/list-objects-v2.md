# ListObjectsV2

Returns part or the full list of objects in a bucket (up to 1000 objects per response). Object Storage supports two listing method versions — [ListObjects](list-objects.md) and ListObjectsV2; ListObjectsV2 is the recommended, newer version.

**Invocation requirement:** pass the query parameter `list-type=2`, otherwise the legacy [ListObjects](list-objects.md) method is invoked instead.

> **Source:** [ListObjectsV2 — official cloud.ru docs](https://cloud.ru/docs/s3e/ug/topics/api__listobjectsv2)

Access to this method depends on the user's role. See [role management in Object Storage](https://cloud.ru/docs/s3e/ug/topics/security__role).

## Request

```http
GET /{bucket}?list-type=2 HTTP/2
Host: s3.cloud.ru
```

## Parameters

### Path parameters

| Parameter | Description |
|-----------|-------------|
| `bucket`  | Bucket name |

### Query parameters

| Parameter       | Description                                                                                         |
|-----------------|-----------------------------------------------------------------------------------------------------|
| `list-type`     | **Required, `2`** — selects ListObjectsV2 instead of the legacy ListObjects                          |
| `delimiter`     | Delimiter character (groups keys into "folders" at that delimiter)                                   |
| `encoding-type` | Encoding of the server response; Object Storage can encode the response on client request             |
| `key-marker`    | Key to start listing from; keys *after* `key-marker` are returned (pagination cursor)                  |
| `max-keys`      | Maximum number of entries in the response                                                            |
| `prefix`        | Only keys beginning with this string are returned                                                      |

## Headers

- See the [common request headers](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) for the full supported list.

## Example request

The request lists the objects in the bucket `my-bucket`:

```http
GET /my-bucket?list-type=2 HTTP/2
Host: s3.cloud.ru
Date: Wed, 25 Oct 2023 22:32:00 GMT
Authorization: authorization string
Content-Type: text/plain
```

## Example response

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ListBucketResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Name>my-bucket</Name>
    <Prefix/>
    <KeyCount>205</KeyCount>
    <MaxKeys>1000</MaxKeys>
    <IsTruncated>false</IsTruncated>
    <Contents>
        <Key>my-file.txt</Key>
        <LastModified>2023-10-12T17:50:30.000Z</LastModified>
        <ETag>"fba9dede5f27731c9771645a39863328"</ETag>
        <Size>434234</Size>
        <StorageClass>STANDARD</StorageClass>
    </Contents>
    <Contents>
       ...
    </Contents>
    ...
</ListBucketResult>
```

## Agent notes

- Forget `list-type=2` and you silently get the legacy API — always include it when scripting.
- `KeyCount` tells how many keys were returned in this response; `IsTruncated: true` plus pagination (`key-marker`) means more pages follow (max 1000 keys per page).
- The V2 response omits per-object `Owner` blocks compared to the legacy version — a leaner payload for bulk enumeration.
- Escape/parse `Key` values with the XML encoding returned (see `encoding-type`) before using them in further [GetObject](get-object.md)/[DeleteObject](delete-object.md) calls.
- Possible error responses are collected in the [response codes reference](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).
